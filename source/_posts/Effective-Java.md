---
title: Effective Java
date: 2018-11-02 00:42:42
tags: [Java]
---
## Creating and Destroying Objects
1. Consider static factory methods instead of constructors
2. Consider a builder when faced with many constructor parameters
3. Enforce the singleton property with a private constructor or an enum type

<!-- more -->
4. Enforce non-instantiated with a private constructor 
5. Prefer dependency injection to hard-writing resources
6. Avoid creating unnecessary objects.
7. Eliminate obsolete object references
8. Avoid finalizers and cleaners
9. Prefer try-with-resources to **try-finally**

## Methods Common to All Objects
10. Obey the general contract when overriding **equals**
11. Always override **hashCode** when you override **equals**
12. Always override **toString**
13. Override **clone** judiciously
14. Considering implementing **Comparable**

## Classes and Interfaces
15. Minimize the accessibility of classes and members
16. In public classes, use accessor methods, not public fields
17. Minimize mutability
18. Favor composition over inheritance
19. Design and document for inheritance or else prohibit it
- Good API documentation should describe what a given method does and not how it does it.
20. Prefer interfaces to abstract classes
21. Design interfaces for posterity
22. Use interfaces only to define types
23. Prefer class hierarchies to tagged classes
24. Favor static member classes over non-static
25. Limit source files to a single top-level class

## Generics
26. Don't use raw types
27. Eliminate unchecked warnings
28. Prefer lists to arrays
29. Favor generic types
30. Favor generic methods
31. Use bounded wildcard to increase API flexibility
32. Combine generics and var-args judiciously
33. Consider type safe heterogeneous containers

## Enums and Annotations
34. Use enums instead of int constants
35. USe instance fields instead of ordinals
36. Use **EnumSet** instead of bit fields
37. Use **EnumMap** instead of ordinal indexing
38. Emulate extensible enums with interfaces
39. Prefer annotations to naming patterns
40. Consistently use the **Override** annotations
41. Use marker interfaces to define types

## Lambdas and Streams
42. Prefer lambdas to anonymous classes
43. Prefer method references to lambdas
44. Favor the use of standard functional interfaces
45. Use stream judiciously
46. Prefer side-effect-free functions in streams