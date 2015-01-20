# Macros

We already encoutered macros specified as page attributes. How do we reference them? We use the construct "$(macro)".

```
page: t
    name: Hui

    Welcome to $(name)'s page.
```

Because MyDef works all kinds of text files and prgramming languages, the $(macro) construct is deliberately chosen to avoid conflicts with the underlying language syntax. 

In addition to page attribute macros, we can define macros outside the page blocks using a macros block.

```
macros:
    name: Hui

page: t
    Welcome to $(name)'s page.
```


