# YASER
Yet Another SERialization format

## Goals

There are several serialization problems which have a varying degree of support in other formats.
YASER aims to handle those serialization problems from the beginning:

1. Circular references
2. Maps
3. Lists
4. Maps with non-string keys
5. UUID
6. Big Decimal
7. Dates
8. Classes with no default constructor
9. Schema evolution
10. Full power to customize (de)serialization for a class, field, interface, base-class etc.



## Algorithm

During serialization, aim is to keep on traversing the object structure until we reach a primitive field.
Primitive fields are: Null, Boolean, String, Number, Character and Enum.
Once a primitive is found, we can easily write that out.
For non-primitives, we just need to preserve the structure somehow so that it can be easily reconstructed later on.



## Primitives

1. Null
  
   00

2. Strings
  
   01:\<strlen\>:\<string-content\>

3. Numbers

   02:\<boolean-in-1-bit\>
   
   03:\<byte-in-8-bits\>
   
   04:\<character-in-16-bits\>
   
   05:\<short-in-16-bits\>
   
   06:\<integer-in-32-bits\>
   
   07:\<float-in-32-bits\>
   
   08:\<long-in-64-bits\>
   
   09:\<double-in-64-bits\>
   
4. Enums
  
   10:\<enum-ordinal-in-32-bits\>



## Non-primtives

5. Arrays

   11:\<array-len\>:\<elements\>

6. Records
  
   12:\<elements\> (We need to write the beginning number for not-null records. For null-records, this will be 00)




8-bits (a byte) of information is needed to describe what to expect while reading.
(Technically, we need a lot less than that, but we keep it a byte for future extensibility)



## Non-primitives (Containers holding primitives)

Consider the following container:

```java
class Person {
    int age;
    String name;
    boolean isMale;
    double salary;
}
```

The schema of this class dictates the order in which the binary format will be read.
It is not possible to read a serialized binary with a new schema unless older schema is provided too.
Once you have the older schema, you can map the fields by name from older to newer schema.



## Non-primitives (Containers holding containers)

Consider the following container:

```java
class Person {
    int age;
    String name;
    Address addr;
}

class Address {
    String streetName;
    Integer zipCode;
}
```



