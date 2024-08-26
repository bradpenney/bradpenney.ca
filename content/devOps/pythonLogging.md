---
title: "Python Logging Module"
date: 2022-11-15
slug: "pythonLogging"
description: "Python Logging Module"
keywords: ["python", "logging"]
draft: false
#tags: ["python", "logging"]
math: false
toc: true
---

As an experienced and comfortable shell scripter, I've been spending some time lately trying to find a way to use Python to replace all my beloved BASHisms.  While nothing compares to the beautiful simplicity of BASH/KSH, Python is just so much more powerful that I can't resist trying to use it for tasks traditionally handled by shell scripts.

One of the huge advantages of BASH scripts is writing to logs is so very simple.  To write the output of any command to a designated log, simply append to the `$log` using:

``` bash
    #!/bin/bash

    outputLog=/var/app/logs/output.log
    errorLog=/var/app/logs/errors.log
    
    myCommand.sh 1>> $outputLog 2>> $errorLog
````

While this is effective, it really does not provide logs that are in a standard format (see [Log Formatting: 7 Best Practices for Readable Log Files](https://www.sentinelone.com/blog/log-formatting-best-practices-readable/)).  Creating log entries with better formatting is certainly possible from BASH scripts, however the complexity quickly increases and it has to be built manually in each script (at least to the best of my knowledge).

For someone like me who is trying to replace BASH scripts with Python programs, one of the major leaps forward in this journey is that Python does have a `logging` core module built into the programming language. Even better, the generated logs can be formatted automagically to follow best practices for logging formats.

## Levels of Logging

The first thing to consider is the level of logging that should append to the logs.  While all levels of logging have their purpose, it usually only makes sense to write major problems to the logs.  In that spirit, the Python `logging` module has five levels available by default.  In order of severity, these are:

1. **Debug** - called with `logging.debug("Debugging message")`
1. **Info** - called with `logging.info("Informational message")` 
1. **Warning** - called with `logging.warning("Warning message")`
1. **Error** - called with `logging.error("Error message")`
1. **Critical** - called with `logging.critical("Critical message")`

## Basic Usage

Like other Python core modules, it needs to be imported (by adding `import logging` to the top of the program) before use.  After importing some basic setup is required.  The essential setup is:

``` python
import logging

logging.basicConfig(
    level=logging.ERROR, 
    filename="errors.log", 
    filemode="a"
    )
```
The above statement would set the logging level to "ERROR", meaning that only ERROR and CRITICAL alerts would be written to the `errors.log` file, and the file access mode would be "append", so the log would grow with each use but retain historical information.  Of course, enterprise grade applications normally split their logging up by days (if not hours), but that is beyond the scope of this introduction (although certainly possible with this tool!).

The real power of the Python `logging` module begins to flex when designating the format of the log to be similar to industry standards.  This can be done by declaring attributes for the log using the `format` argument as below:

``` python
logging.basicConfig(
    level=logging.ERROR, 
    filename="errors.log", 
    filemode="a", 
    format="%(asctime)s - %(levelname)s - %(lineno)d - %(message)s"
    )
```
There are many attributes that can be added to the log format, the listing can be found in the [official documentation for the `logging` module](https://docs.python.org/3/library/logging.html#logrecord-attributes).

## Embedding Variables in Logs

Python `logging` also accepts variables and substitutes them into `f strings` (and other Python string formatting). For example:

``` python
import logging

logging.basicConfig(
    level=logging.ERROR, 
    filename="errors.log", 
    filemode="a", 
    format="%(asctime)s - %(levelname)s - %(message)s"

myName = "Brad"
logging.critical(f"Oh noes! {myName} created an error!")
```

With this example, the log would record:

``` html
2022-11-15 15:23:51,490 - CRITICAL - Oh noes!  Brad created an error!
```

## Embedding Tracebacks in Logs


In Python, errors are generally caught using a `try/except` block.  The `logging` module has a special `exception()` function built in to append caught errors to the log.  For example:

``` python
import logging

logging.basicConfig(
    level=logging.ERROR, 
    filename="errors.log", 
    filemode="a", 
    format="%(asctime)s - %(levelname)s - %(message)s"

try:
    1/0
except ZeroDivisionError:
    logging.exception("ZeroDivisionError")
```

Which would print the following info to the log:

``` html

2022-11-14 10:00:05,348 - ERROR - ZeroDivisionError
Traceback (most recent call last):
File "/home/brad/Documents/test.py", line 9, in <module>
    1/0
ZeroDivisionError: division by zero
```

This really only scratches the surface of what is possible with the Python `logging` module, but these basics are more than enough to get started.  While BASH can do all of these things (except tracebacks), Python `logging` makes this task more robust and repeatable.

## Further Resources

[![Tech with Tim - Python Logging](https://img.youtube.com/vi/urrfJgHwIJA/maxresdefault.jpg)](https://youtu.be/urrfJgHwIJA "Tech with Tim - Python Logging")

[![Socratica - Logging Module](https://img.youtube.com/vi/g8nQ90Hk328/maxresdefault.jpg)](https://youtu.be/g8nQ90Hk328 "Socratica - Logging Module")
