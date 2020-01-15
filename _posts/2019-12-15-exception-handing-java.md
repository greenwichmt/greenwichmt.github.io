---
layout: post
title: design patterns for exception or error handling
date: 2019-12-15 16:30
author: admin
comments: true
categories: [java]
tags: [pattern,exception,design,guidelines]
---

1. Don't manage business logic with exceptions. Use conditional statements instead. If a control can be done with if-else statement clearly, don't use exceptions because it reduces readability and performance  (e.g. null control, divide by zero control). .
2. Exception names must be clear and meaningful, stating the causes of exception.
3. Throw exceptions for error conditions while implementing a method. E.g. if you return -1, -2, -3 etc. values instead of FileNotFoundException, that method can not be understand.
4. Catch specific exceptions instead of the top Exception class. This will bring additional performance, readability and more specific exception handling.
5. Null control with conditionals is not an alternative to catching NullPointerException. If a method may return null, control it with if-else statement. If a return may throw NullPointerException, catch it.
6. Try not to re-throw the exception because of the price. Bu if re-throwing had been a must, re-throw the same exception instead of creating a new exception. This will bring additional performance. You may add additional info in each layer to that exception.
7. Define your own exception hierarchy by extending current Exception class (e.g. UserException, SystemException and their sub types) and use them. By doing this you can specialize your exceptions and define a reusable module/layer of exceptions.
8. Use types of exceptions clearly. Fatal: System crash states. Error: Lack of requirement. Warn: Not an error but error probability. Info: Info for user. Debug: Info for developer. 
9. Don't absorb exceptions with no logging and operation. Ignoring exceptions will save that moment but will create a chaos for maintainability later.
10. Don't log the same exception more than once. This will provide clearness of the exception location.
11. Always clean up resources (opened files etc.) and perform this in "finally" blocks.
12. Exception handling inside a loop is not recommended for most cases. Surround the loop with exception block instead.
13. Granularity is very important. One try block must exist for one basic operation. So don't put hundreds of lines in a try-catch statement.
14. Produce enough documentation for your exception definitions (at least JavaDoc).
15. Giving a number/code for each different exception message is a good practice for documentation and faster communication.
