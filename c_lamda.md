## Anonymouse

C does not really support anonymous functions, and for those language who do, often the syntax is clunky. This is because when we think about anonymous function, we are simply thinking about procedures -- the statements in the function. But to support in a context free manner, the syntax has to include arguments and return types. Let's see what MyDef can do, even in plain C:

```
page: t, basic_frame
    $struct(record) s_name, n_score
    $(set:N=10)

    $global struct record p_recordlist[$(N)]
    $foreach p_recordlist
        $(t).n_score=rand()

    &call qsort, p_recordlist, $(N), struct record
        &call numcmp, $(a).n_score, $(b).n_score
            return 0

    $foreach p_recordlist
        $print "%d", $(t).n_score


    $print "----- Reverse -----\n"
    &call qsort, p_recordlist, $(N), struct record
        &call numcmp, $(b).n_score, $(a).n_score
            return 0

    $foreach p_recordlist
        $print "%d", $(t).n_score

```
It compi into:
```
#include <stdio.h>
#include <stdlib.h>

struct record {
	char * s_name;
	int n_score;
};

int qsort_cmp(const void * a, const void * b);
int qsort_cmp_2(const void * a, const void * b);

struct record p_recordlist[10];

int main(int argc, char** argv){
    int i;

    for(i=0;i<10;i++){
        p_recordlist[i].n_score = rand();
    }
    qsort(p_recordlist, 10, sizeof(struct record), qsort_cmp);
    for(i=0;i<10;i++){
        printf("%d\n", p_recordlist[i].n_score);
    }
    puts("----- Reverse -----");
    qsort(p_recordlist, 10, sizeof(struct record), qsort_cmp_2);
    for(i=0;i<10;i++){
        printf("%d\n", p_recordlist[i].n_score);
    }
    return 0;
}

int qsort_cmp(const void * a, const void * b){
    if((*(struct record*)a).n_score < (*(struct record*)b).n_score){
        return -1;
    }
    else if((*(struct record*)a).n_score > (*(struct record*)b).n_score){
        return 1;
    }
    else{
        return 0;
    }
}

int qsort_cmp_2(const void * a, const void * b){
    if((*(struct record*)b).n_score < (*(struct record*)a).n_score){
        return -1;
    }
    else if((*(struct record*)b).n_score > (*(struct record*)a).n_score){
        return 1;
    }
    else{
        return 0;
    }
}

```
As we see, it is not really anonymous, there is a name in each case and the names are not random either, but it is anonymous in the sense that we don't really need care about its name and they will automatically resolve into some uniquenames. 

The qsort subcode (as well as numcmp subcode) are included in standard library std_c.def:

```
# ---- part of file std_c.def -----------------
subcode: qsort(list, size, type)
    $function qsort_cmp(const void * a, const void * b)
	$return_type int
	$(set:a=(*($(type)*)a))
	$(set:b=(*($(type)*)b))
	BLOCK
    qsort($(list), $(size), sizeof($(type)), $(lamda))

subcode: numcmp(na, nb)
    $(allow_recurse:10)
    $if $(na)<$(nb)
	return -1
    $elif $(na)>$(nb)
	return 1
    $else
	BLOCK

```
Anonymous functions are typically used as call back functions or part of API interface, which almost always carries a context. It always makes sense to code anonymous function in a library that can utilize the context thus make the calling routine clean and simple.

Just to make sure, let's compile and run it:
```
$ make all
mydef_page t.def out/t.c
PAGE: t
  --> [out/t.c]
make -C out
make[1]: Entering directory `/media/whiteplug/projects/test/C/out'
gcc -c   -o t.o t.c
gcc   -o t t.o
make[1]: Leaving directory `/media/whiteplug/projects/test/C/out'

$ out/t
424238335
596516649
719885386
846930886
1189641421
1649760492
1681692777
1714636915
1804289383
1957747793
----- Reverse -----
1957747793
1804289383
1714636915
1681692777
1649760492
1189641421
846930886
719885386
596516649
424238335

```
