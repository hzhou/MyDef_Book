Installing MyDef
================

MyDef can be installed on Linux, OSX, and Windows. Because MyDef is actually pure Perl package, it can run in any platform where Perl is installed. However, running MyDef on a non-UNIX environment is not well tested. So for this book, we'll assume a UNIX like system. On Windows platform, this means cygwin (https://www.cygwin.com/).

First, obtain the source from github repository:

    $ git clone https://github.com/hzhou/MyDef.git
    Cloning into 'MyDef'...
    remote: Counting objects: 2546, done.
    remote: Compressing objects: 100% (19/19), done.
    remote: Total 2546 (delta 5), reused 0 (delta 0)
    Receiving objects: 100% (2546/2546), 1.03 MiB, done.
    Resolving deltas: 100% (1882/1882), done.

The author of MyDef recommend install into one's home directory. First add following line into one's .bashrc file:

    PATH=/bin:/usr/bin:~/bin
    PERL5LIB=~/lib/perl5
    MYDEFLIB=~/lib/MyDef
    export PATH PERL5LIB MYDEFLIB

If this is your first time, we need import these settings into your current shell:
    
    $ source ~/.bashrc
    
Now we can proceed to install MyDef:

    $ cd MyDef
    $ sh bootstrap.sh
    
MyDef is installed!

To test, create a test.def:

    page: test, basic_frame
        $print Hello world!
        
Compile and run:

    $ mydef_page -mperl test.def && perl test.pl
    
If you get:

    PAGE: test
      --> [./test.pl]
    Hello world!
    
then the installation is successful.
