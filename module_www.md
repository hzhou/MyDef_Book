## WWW Module

If you are diveloping websites, then you need write HTML, CSS, JavaScript, and maybe PHP. And in the way these language are being used, they are often being mixed together. Because of that, the output module for HTML, CSS, JavaScript and PHP are together called "www" module. In order to distinguish which language you code is being written, MyDef recognizes the subcode "mode".

You know about "subcode", but did you also notice sometime we use "fncode", and in some of the examples we use, you may also have seen "htmlcode", "jscode", "phpcode", or even "xscode", "perlcode"?

In fact, MyDef's core module recognizes any "xxxcode", and simply treat them all the same as subcode. The prefix "xxx" is recognized as the subcode's type, or "mode". Not all output modules use these subcode types, but in the case of www module, they are very convenient.

Look at the following example:

```
page: t
    <p>test</p>
    $call js_code
    $call html_code
    $call php_code

jscode: js_code
    var s = "This is in JavaScript"

phpcode: php_code
    print "This is from PHP";

htmlcode: html_code
    <p> This is in HTML </p>
```
It compiles into:
```
<p>test</p>
<script type="text/javascript">
var s = "This is in JavaScript";
</script>
<p> This is in HTML </p>
<?php
print "This is from PHP";
?>
```

As we have here, MyDef switches the language context for us.

Of course we can write pure HTML file:

```
page: t
    type: html

    $call html_code

```
It compiles into:
```
<p> This is in HTML </p>
```

Or JavaScript file:
```
page: t
    type: js

    $call js_code

```
It compiles into:
```
var s = "This is in JavaScript";
```

Or a pure php file:
```
page: t
    type: php

    $call php_code

```
It compiles into:
```
<?php
print "This is from PHP";
?>
```

The old "subcode" still works, but it simply inherits whatever mode it was:
```
page: t
    type: php

    $call php_code
    $call html_code
    $call js_code

jscode: js_code
    var s = "This is in JavaScript"
    $call whatever

phpcode: php_code
    print "This is from PHP";
    $call whatever

htmlcode: html_code
    <p> This is in HTML </p>
    $call whatever

subcode: whatever
    What code will this be?
```
It compiles into:
```
<?php
print "This is from PHP";
What code will this be?;
?>
<p> This is in HTML </p>
What code will this be?
<script type="text/javascript">
var s = "This is in JavaScript";
What code will this be?;
</script>
```

Now we covered the basic, we can discuss each code type respectively.
