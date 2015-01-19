# Multiple pages in a single def file

We already know the basic unit in a def file is page:

    page: filename
        ...
        
In fact, a single def file can contain multiple page:

    page: file1
        ...
        
    page: file2
        ...
        
Upon compile, multiple output source file will be generated:

    $ mydef_page.pl example.def
    PAGE: file1
      --> [file1.xxx]
    PAGE: file2
      --> [file2.xxx]
    
There are many situations this feature can be appreciated. For example, when we program a website, often we will have frontend pages for forms and server side scripts to process the form submissions, or frontend Ajax pages and backend Ajax server. Because those are essentially paired -- changes on one side necessarily involves changes on the other side -- semantically we would like to compose them together and potentially use the same macros to avoid potential discrepancies. So we would like to have:

```
macros:
    form1_fieldlist: firstname, lastname, ...
    
page: form1
    type: html
    ...
       $map form_input, $(form1_fieldlist)
       ...

page: process_form1
    type: php
    ...
       $map load_post, $(form1_fieldlist)
```

Please ignore the syntax of macros and subcode mapping calls for now, which will be covered in next section. Having both pages together makes it easier for our mind to semantically grasp the structures. Also, in case we want add or delete form fields, we only need edit the list of "form1_fieldlist", and the macro system will ensure the change reflected in both pages. Avoid duplications is a proven way to reduce potential bugs.

In addition to frontend and backend separation, some conventions calls for script, html and stylesheet seperations. It is important to conform to the conventions of your team, but MyDef let's you still group them together in your source if you deem them semantically belong together.

In another often encoutered situations, we are writing scripts to extract data, plotting them, gathering statistics and running transformations, and exporting them in the end. We often need write multiple short scripts to do each of these tasks. However, they are ultimately connected by whatever data structure and context we establish. Having these scripts in one place makes referencing back and forth easy, as well as allowing us saving the conventions in subcodes or macros so we don't have to duplicate code.