---
layout: post
title: "PSSE and Floating point exceptions"
date: 2015-04-07
comments: false
categories:
---

```python
float(NaN)
Floating Point exception (Invalid Operation)
```

== Quick Fix ==
Set the environment variable ``FPMASK=xx``

PSSE uses this environment variable to set the floating point control word for
the math coprocessor [1]. 

== x87 Control Word ==

The control word is a special 16-bit register maintained by the math
coprocessor. By setting and clearing bit fields in the control word, you can
exercise control of certain aspects of coprocessor operation. 

Inline-style: 
![x87 control word](images\x87_control.PNG "x87 Control Word")

The environment variable is in base 10.  So we convert to hex then binary.

```
53 (base 10) = 0x35 = 0011 1001
```

== Set using python ==
You can set the environment variable inside your python script.  This way the
script will work on other systems.

```
import os
os.setenv('FPMASK') = 93
import psspy

# Make sure no exceptions are thrown.
x = float(NaN)
x = 1.0/0
```
