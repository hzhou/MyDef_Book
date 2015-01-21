# Autoload

Library def files merely defines subcodes and macros. Including the library usually does not direct effect on the compiled output. Some time the library will need setup certain macros and define certain functions (structures of the underlying library). But by default, even these routine need explicitly called in the main code or you will find the functions are not being defined eventhough the library def files are included. 

To remedy this inconvinience, each library can define an _autoload subcode:

```
subcode: _autoload
    # set up macros
    # set up structures and functions
    # declare global variables
    # etc
```

All the library's _autoload subcodes will be concatenated together (even only "subcode:" is used instead of "subcode::"), and it will get run before the page's main subcode.


