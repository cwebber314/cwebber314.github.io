---
layout: post
title: "PSSE and Floating point exceptions"
date: 2015-04-07
comments: false
categories:
---

PSSE and Floating point exceptions 
=====================================

PSSE crashes hard when it runs across certain floating point numbers.  This can
be a particularly hard problem to debug, because there may be no traceback or
error message at all depending on how you are using PSSE.

The trivial case to reproduce:

  1. Start PSSE
  2. In the Python command line enter ``float('NaN')``
  3. Result: PSSE Crashes

This issue can also cause crashes deep inside 3rd party libraries which are 
hard to debug.  I first saw this issue in pandas and pytables.

Quick Fix 
-----------

Set the environment variable ``FPMASK=59``

PSSE uses this environment variable to set the floating point control word for
the math coprocessor [1]. 

x87 Control Word
---------------------
The control word is a special 16-bit register maintained by the math
coprocessor. By setting and clearing bit fields in the control word, you can
exercise control of certain aspects of coprocessor operation. 

![x87 control word](/assets/x87_control.PNG "x87 Control Word")

The environment variable is in base 10.  So we convert to hex then binary.

```
53 (base 10) = 0x35 = 0011 1001
```

Fix inside python 
-------------------
You can set the environment variable inside your python script.  This way the
script will work on other systems.

    import os
    os.setenv('FPMASK') = 93
    import psspy

    # Make sure no exceptions are thrown.
    x = float(NaN)
    x = 1.0/0

