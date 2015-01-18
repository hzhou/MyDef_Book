# Perl

Since MyDef uses indentation as basic structure unit, one may think it is heavily influenced by Python. However, the biggest influencer is Perl. In fact, MyDef itself is written in Perl (with MyDef of course).

Perl is flexible and direct. Quote from the book "Programming Perl": "One thing that's easy about Perl is that you don't have to say much before you say what you want to say." The price for that is Perl often being perceived as one ugly language.

However, let's see if MyDef can make Perl programming even more direct and as well as more cosmetic.

Consider following program:

    #!/usr/bin/perl
    
    my @out
    open In, "molecule.xyz" or die "Can't open molecule.xyz!\n";
    while <In>{
        if(/^([A-Z]\w*)\s+(\S+)\s+(\S+)\s*(\S+)/){
            my ($atom, $x, $y, $z)=($1, $2, $3, $4);
            my $color="[1,0,0]";
            my $radius=0.3;
            if($atom=~/^C/){
                $color="[0.5,0.5,0.5]";
                $radius=0.2;
            }
            elsif($atom=~/^N/){
                $color="[0.1, 0.1, 0.8]";
                $radius=0.2;
            }
            
            push @out, "    var t=set_sphere($x, $y, $z, $radius)\n";
            push @out, "    objlist.push({size:t, color: $color})\n";
        }
    }
    close In;
    
    open Out, ">~/webgl/set_molecule.def" or die "Can't write ~/webgl/set_molecule.def!\n";
    print Out "subcode: set_molecule\n";
    foreach my $l (@out){
        print Out $l;
    }
    close Out;
    

What it does is to reads in atomic coordinates from an input file and then output code that will plot them in a webpage (using webgl, which I'll include in the next example).

Now with MyDef, we could write:

    page: read_molecule
        my @out
        &call open_r, molecule.xyz
            $if /^([A-Z]\w*)\s+(\S+)\s+(\S+)\s*(\S+)/
                $call set_atom, $1, $2, $3, $4
                
        &call open_w, ~/webgl/set_molecule.def
            print Out "subcode: set_molecule\n"
            $foreach $l in @out
                print Out $l
            
    subcode: set_atom(atom,x,y,z)
        my ($atom, $x, $y, $z)=($1,$2,$3,$4)
        my $color="[1,0,0]"
        my $radius=0.3
        $if $atom=~/^C/
            $color="[0.5,0.5,0.5]"
            $radius=0.2
        $elif $atom=~/^N/
            $color="[0.1,0.1,0.8]"
        push @out, "    var t=set_sphere($x, $y, $z, $radius)\n"
        push @out, "    objlist.push({size:t, color: $color})\n"

There is not much difference in the set_atom part of the code, except in MyDef both semicolon and brackets are optional. Also in MyDef, we can easily seperate the set_atom code out into a subcode without worry about variable scopes -- which we should worry if we move it into a perl sub. Moving the set_atom code out also allows us to read the overall program structure succinctly. Without any comments, we understand we are reading in a text file, extrating some information, and writing out to another file.

Experienced readers may also appreciate where we use &call open_r and &call open_w to encapsulate a code structure with both initialization and finalization. This would be very difficult to achieve with functions in a imperative language. But because MyDef is merely a macro system, this is not difficult. Here are the subcode: open_r in std_perl.def:

    subcode: open_r(name)
        $(if:name~")
            $(set:name=$(name:strip))
        open In, "$(name)" or die "Can't open $(name).\n"
        $while <In>
            BLOCK
        close In
            
            
            