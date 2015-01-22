# Preprocessing

MyDef implements some preprocessing directives such as to offer conditional compilations. All MyDef preprocessing directives take the form of "$(keyword...)" and it always takes up the entire line. 

Conditional switches
---------------

These are straight forward $(if)...$(elif)...$(else) switches. 

```
subcode: A(name)
    $(if:name=Hui)
        $call very_specific_behavior
    $(elif:name=None)
        # do nothing
    $(else)
        $call general_behavior
```

These conditional switches tests the following condtions:

* $(if:0)
  
  Always false. The block is switched off. It can be used as effective block comment.

* $(if:1)
  
  Always true. The block is switched on.

* $(if:number:name)
  
  True if macro $(name) is defined and starts with digits

* $(if:string:name)
  
  True if macro $(name) is defined and enclosed in quotes, either "..." or '...'

* $(if:hascode:name)
  
  True if "subcode: name" is defined

In the followin rules, name can be replaced with name:number (e.g. name:4), which means take the first number characters of the macro $(name).

* $(if:name)
  
  True if macro $(name) is defined and not empty and not 0, false otherwise

* $(if:name in item1,item2,...,itemN)
  
  True if macro $(name) is equal to one of the itemlist. 

* $(if:name in range)
  
  range is of the form a-z, where a and z is any alphanumeric character. It is true when the first letter of macro $(name) is in the range.

* $(if:name=string)
  
  True if macro $(name) is equal to "string" 

* $(if:name!=string)
  
  True if macro $(name) is not equal to "string" 

* $(if:name>value)
  
  True if macro $(name) is greater than value numerically

* $(if:name<value)
  
  True if macro $(name) is less than value numerically

* $(if:name~string)
  
  True if macro $(name) starts with "string"

In the following rules, cond refers to any of above conditions (following if:)

* $(if:!cond)
  
  Negate. True become false, and false become true.

* $(if:cond1 or cond2 or ...)
  
  Or. True if any of the conditions are true.

* $(if:cond1,cond2,...)
  
  And. True if all the conditions are true.

The above three operations can be combined. And has the highest precedence, followed by Or and Negate. No parentheses are allowed.

Constant repeats
---------------

Since it is a merely macro preprocessing, variable loops does not make sense. But simple constant repeats are quite useful.

```
$(for:name in a,b,c)
    BLOCK
```

Here BLOCK represents arbitrary number indented lines. BLOCK is repeated 3 times, each time having macro $(name) set to a, b, and c. This is equiallent to:
```
subcode: A(name)
    BLOCK

$map A, a, b, c
```

But in a anonymous, more succinct way. 

```
$(for:name in 1..9)
    BLOCK
```
In this case, BLOCK repeats 9 times, each time macro $(name) takes value of 1 to 9 respectively. MyDef will comply if you try $(for:name in 1..1000) but make sure that is what you mean.


