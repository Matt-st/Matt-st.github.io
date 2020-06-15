---
layout: post
title: "Building a String Comparator in Java"
date: 2020-06-10
categories: Java, Collections, Comparator, String
published: true
---


## What is the Comparator in Java

A Comparator in java is an interface seen [here](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html), where a developer can leverage the comparator functional interface to build a custom compare method.  We can use the custom compare method to sort our objects.  In this post I will be showing an example of sorting using custom compare functions.

### Simple comparator example to sort by string length
We have a basic main method that passes in a sentence to a our function `parseWords(String input)`.  In this function we get each word from the sentence into a string array by splitting on spaces `String[] strArray = input.split(" ");`.  Once we have the String array we convert it to an ArrayList and call `Collections.sort(wordList, new SimpleCompareStringLength());`.  



```java

public class SimpleComparator {
    static void parseWords(String input) {
        String[] strArray = input.split(" ");
        List<String> wordList = Arrays.asList(strArray);
        Collections.sort(wordList, new SimpleCompareStringLength());

        // Arrays.sort(str, Comparator.comparingInt(String::length));
        for(int i = 0; i < wordList.size(); i++){
            System.out.println(" " + wordList.get(i));
        }
    }

    public static void main(String[] args) {
        parseWords("So that his place shall never be with those cold and timid souls who neither know victory nor defeat");
    }
}
```

If we take a look at the collections.sort method from `java.util.Collections` class, we can see that it takes a list of generic items and a Comparator of the same generic items.

```java

public static <T> void sort(List<T> list, Comparator<? super T> c) {
        list.sort(c);
    }


```

The `list.sort(c)` calls several methods down the stack but ultimately ends up at a merge sort implementation on the `java.util.Arrays`  in this method signature:

` private static void mergeSort(Object[] src, Object[] dest, int low, int high, int off, Comparator c)`

The mergesort implementation can be seen in the code [here](https://docs.oracle.com/javase/7/docs/api/java/util/Arrays.html#sort(T[],%20int,%20int,%20java.util.Comparator)) but I think for now it's sufficient for us to know that it exists and this is what is happening during the `Collections.sort()` call.

Below we have our simple compare string length class which implements the functional interface Comparator<T>.  We have implemented the compare function which will take 2 Strings and compare their lengths.  The compare method will return 1, -1, or 0 based on if the values are different or equal.

```java
class SimpleCompareStringLength implements Comparator<String> {
    public int compare(String o1, String o2) {
        int result = Integer.compare(o1.length(), o2.length());
        System.out.println("compared: " +  o1 +" -> "+ o2 + " = " + result);
        return result;
    }
}

```

Inside the compare method we can see that we call an existing implementation of `Integer.compare`.  Below we see the implmentation of that method and it's a simple next teranary operator.  There are three simple cases:
1. x == y returns 0
2. x < y returns -1
3. x > y returns 1

```java
  public static int compare(int x, int y) {
        return x < y ? -1 : (x == y ? 0 : 1);
    }
```


We can run the code above as a simple java main execution and the output is as follows. 

```text
compared: that -> So = 1
compared: his -> that = -1
compared: his -> that = -1
compared: his -> So = 1
compared: place -> his = 1
compared: place -> that = 1
compared: shall -> that = 1
compared: shall -> place = 0
compared: never -> that = 1
compared: never -> shall = 0
compared: be -> place = -1
compared: be -> his = -1
compared: be -> So = 0
compared: with -> that = 0
compared: with -> shall = -1
compared: with -> place = -1
compared: those -> with = 1
compared: those -> shall = 0
compared: those -> never = 0
compared: cold -> with = 0
compared: cold -> never = -1
compared: cold -> shall = -1
compared: cold -> place = -1
compared: and -> cold = -1
compared: and -> his = 0
compared: and -> with = -1
compared: and -> that = -1
compared: timid -> with = 1
compared: timid -> shall = 0
compared: timid -> those = 0
compared: souls -> cold = 1
compared: souls -> never = 0
compared: souls -> timid = 0
compared: who -> cold = -1
compared: who -> and = 0
compared: who -> with = -1
compared: who -> that = -1
compared: neither -> cold = 1
compared: neither -> those = 1
compared: neither -> souls = 1
compared: know -> cold = 0
compared: know -> those = -1
compared: know -> shall = -1
compared: know -> place = -1
compared: victory -> know = 1
compared: victory -> those = 1
compared: victory -> souls = 1
compared: victory -> neither = 0
compared: nor -> know = -1
compared: nor -> who = 0
compared: nor -> with = -1
compared: nor -> that = -1
compared: defeat -> know = 1
compared: defeat -> timid = 1
compared: defeat -> neither = -1
compared: defeat -> souls = 1
 So
 be
 his
 and
 who
 nor
 that
 with
 cold
 know
 place
 shall
 never
 those
 timid
 souls
 defeat
 neither
 victory
```
### What if we want to chain our sorts
Imagine that you wanted to do several stages of sorting.  
1. sort by length (we already have this implemented above)
2. sort by alphabet and capitals

We will want to do chaining in this case.  With chaining we can call `thenComparing(Comparator)` as seen below:

```java

 Collections.sort(wordList, new SimpleCompareStringLength()
                .thenComparing(String::compareTo));

```

Here we can see the code for the `String.compareTo`

```java
public int compareTo(String anotherString) {
        byte[] v1 = this.value;
        byte[] v2 = anotherString.value;
        if (this.coder() == anotherString.coder()) {
            return this.isLatin1() ? StringLatin1.compareTo(v1, v2) : StringUTF16.compareTo(v1, v2);
        } else {
            return this.isLatin1() ? StringLatin1.compareToUTF16(v1, v2) : StringUTF16.compareToLatin1(v1, v2);
        }
    }
```

If we run our code with the new changes to our collections sort function call we get:

```text
 So
 be
 and
 his
 nor
 who
 cold
 know
 that
 with
 never
 place
 shall
 souls
 those
 timid
 defeat
 neither
 victory
```

### Ending remarks

I hope that this explanation sheds some light on the usage of comparator's and the collections.sort function.  I also wanted to point out as our example text I used part of the quote from Theodore Roosevelt titled "The man in the arena" originally taken from his “Citizenship in a Republic” speech.

Code for these examples can be found on my github [here](https://github.com/Matt-st/Learning-Topics/blob/master/ds-algo/Java/src/main/java/comparator/SimpleComparator.java)

Thanks for reading!
