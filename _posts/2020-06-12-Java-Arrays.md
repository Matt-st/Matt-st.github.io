---
layout: post
title: "Java Collections: Arrays"
date: 2020-06-11
categories: Java, Util, Collections, Comparator, String
published: false
---


## Overview

I wanted to do a deep dive into the java util and collections code starting with Arrays.

We've seen some of the collections framework in a previous post that we can compare strings and do chaining with our comparator object to do sorting:
1. [Building a String Comparator in Java](https://matt-st.github.io/Comparator-in-java/).
2. [Custom Object Comparator in Java](https://matt-st.github.io/Custom-Object-Comparator-In-Java/)

### Arrays.sort subset

The sort functions in the arrays class for most primitive datatypes in the java language.  Below we will look into sorting of primitive integer arrays and the sorting subset of integer arrays:

### Example 1: sorting a subset
This can be useful for sorting a subset of your integer or other primitive data type arrays.

```java
static void sortIntArray( int[] numArr) {
        Arrays.sort(numArr, 1,5);
        System.out.println("Subset of sorted numbers : " + Arrays.toString(numArr));

    }

 public static void main(String[] args) {
        int[] myIntArray = new int[]{100, 20, 320, 400, 2, 9, 4, 3, 67, 54, 34};
        sortIntArray(myIntArray);
    }

```

Output:
```text
Subset of sorted numbers : [100, 2, 20, 320, 400, 9, 4, 3, 67, 54, 34]
```

Example 2: Arrays.sort
This is useful to sort an array of primitives. In the example below we sort an integer array.

```java
static void sortIntArray( int[] numArr) {
        Arrays.sort(numArr);
        System.out.println("All sorted numbers : " + Arrays.toString(numArr));

    }

    public static void main(String[] args) {
        int[] myIntArray = new int[]{100, 20, 320, 400, 2, 9, 4, 3, 67, 54, 34};
        sortIntArray(myIntArray);
    }
```

Output:
```text
All sorted numbers : [2, 3, 4, 9, 20, 34, 54, 67, 100, 320, 400]
```





### Example 3: Arrays.binarySearch
This function is useful to perform a binary search over a primitive array.  What's interesting is that the search will return the array place that it was found at with base 0.  So for example if we search for a number at the beginning of the sorted array it will be 0 or at the end it will be n-1.  The runtime of the binary search is log2n.

```java
 static void sortIntArray( int[] numArr) {
     
        Arrays.sort(numArr);
        System.out.println("All sorted numbers : " + Arrays.toString(numArr));
       int val = Arrays.binarySearch(
               numArr, 2
       );
        System.out.println("Found 2 at this position: " + val);

    }

    public static void main(String[] args) {
        int[] myIntArray = new int[]{100, 20, 320, 400, 2, 9, 4, 3, 67, 54, 34};
        sortIntArray(myIntArray);
    }

```

Output:
```text

All sorted numbers : [2, 3, 4, 9, 20, 34, 54, 67, 100, 320, 400]
Found 2 at this position: 0
```

### Example 4 Sorting String object
Earlier I referenced Arrays.sort() function as only sorting primitives but it can also sort objects as well.  Let's take a look at the example below of sorting an array of strings.

```java
  static void sortIntArray( String[] str ) {
        Arrays.sort(str);
        System.out.println("All sorted strings : " + Arrays.toString(str));
    }

    public static void main(String[] args) {
        String[] str = new String[]{"Volvo", "BMW", "Ford", "Mazda"};
        sortIntArray(str);
    }

```

Output:
```text

All sorted strings : [BMW, Ford, Mazda, Volvo]
```
