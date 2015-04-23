# C++

MyDef C++ output module inherits the C module, therefore, all the features covered in C is automatically available in writing C++. But std_c.def will not be automatically included, std_cpp.def will be included instead.

MyDef's C++ specific support is not complete. For example, it does not support classes yet. However, you always can write any code using C++ specific syntax, as well as benefit from all MyDef's macro system. 

Let's simply look at an example:

```
include: macros/vector.def
include: macros/permutation.def
#---------------------------------------
#-   Return all pumutations
page: test, basic_frame
    $list permuteF

    fncode: permuteF(vn_num)
        &call vvn_return
            n=vn_num.size()
            $if n>0
                &call permute, n
                    &call vvn_append, vvn_ret
                        $for i=0:n
                            $(V).push_back(vn_num[$(perm)[i]])

#---------------------------------------- 
#- macros/vector.def
subcode:: _autoload    
    $include <vector>
    $register_prefix(vvn) vector<vector<int> >
    $register_prefix(rvvn) vector<vector<int> > &
    $register_prefix(vs) vector<string>
    $register_prefix(rvs) vector<string> &
    $register_prefix(vvs) vector<vector<string> >
    $register_prefix(rvvs) vector<vector<string> > &
    $register_prefix(vc) vector<char>
    $register_prefix(rvc) vector<char> &
    $register_prefix(vvc) vector<vector<char> >
    $register_prefix(rvvc) vector<vector<char> > &

    $register_name(matrix) vector<vector<int> > &

subcode: vvn_return
    $local vvn_ret
    BLOCK
    return vvn_ret

subcode: vvn_append(vvn)
    tn_vvn_i=vvn_ret.size()
    vvn_ret.resize(tn_vvn_i+1)
    $(set:V=vvn_ret[tn_vvn_i])
    BLOCK


#---------------------------------------- 
#- macros/permutaion.def

subcode: permute(n)
    $(set:perm=pn_counter)
    $local_allocate(n) $(perm)
    $for i=0:n
        $(perm)[i]=i
    $while 1
        BLOCK
        $call permute_inc

subcode: permute_inc
    # $call debug_perm
    tn_max=-1
    $for i=n:0
        $if tn_max<$(perm)[i]
            tn_max=$(perm)[i]
        $else
            $for j=n:0
                $if $(perm)[j]>$(perm)[i]
                    $call swap, $(perm)[i], $(perm)[j]
                    break
            j=i+1
            k=n-1
            $while j<k
                $call swap, $(perm)[j], $(perm)[k]
                j++
                k--
            break
    $if i<0
        break

```
