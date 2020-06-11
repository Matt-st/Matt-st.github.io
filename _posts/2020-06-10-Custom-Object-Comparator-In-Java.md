---
layout: post
title: "Custom Object Comparator in Java"
date: 2020-06-11
categories: Java, Collections, Comparator, String
published: true
---


## What is the Comparator in Java

A Comparator in java is an interface seen [here](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html), where a developer can leverage the comparator functional interface to build a custom compare method.  We can use the custom compare method to sort our objects.  In this post I will be showing an example of sorting using custom compare functions.

We've seen in a previous post that we can compare strings and do chaining with our comparator object in this post [Building a String Comparator in Java](https://matt-st.github.io/Comparator-in-java/).

In this post I want to dive into custom objects sorting and more chaining examples with Comparator.

### Example 1: Simple custom object sorting

Movie.class  
```java
class Movie {
	//skip getters and setters to reduce verbosity
    public String title;
    public int rating;
    Movie (String title, int rating){
        this.title = title;
        this.rating = rating;
    }
}
```

Sort function which uses collection.sort and a custom comparator sorting method.

```java
 static void sortList( List<Movie> movieList) {
 	//custom comparator function
        Comparator<Movie> sortingByTitle =
                (Movie m1, Movie m2)->m1.title.compareTo(m2.title);
       //pass the comparator function into the sort function call
        Collections.sort(movieList, sortingByTitle);
        
        for(int i = 0; i < movieList.size(); i++){
            System.out.println(" " + movieList.get(i).title + " " + movieList.get(i).rating);
        }
    }
```

The driver method is below:
```java
  public static void main(String[] args) {
       List<Movie> movieList = new ArrayList<>();
       movieList.add(new Movie("Rainman", 4));
       movieList.add(new Movie("Godzilla", 2));
       movieList.add(new Movie("Queen of the South", 4));
       movieList.add(new Movie("Hitman", 3));
       sortList(movieList);
    }

```

Output:
```text
 Apples 4
 Godzilla 2
 Godzilla 3
 Hitman 3
 Hitman 2
 Moana 4
 Queen of the South 4
 Rainman 4

```

### Example 2 sort by rating

The runner and primary code stays the same we only use a different comparator function.

```java
	Comparator<Movie> sortingByRating =
                (Movie m1, Movie m2)->Integer.compare(m1.rating, m2.rating);

    Collections.sort(movieList, sortingByRating);

```

Output:
```txt
 Godzilla 2
 Hitman 2
 Hitman 3
 Godzilla 3
 Apples 4
 Rainman 4
 Queen of the South 4
 Moana 4

```


### Example 3 Chaining with Custom Class
Thanks to a post [here](https://medium.com/faun/chaining-comparators-65890bc2ad6a),  I was able to learn about this custom class way of handling chaining for multiple comparators. 

```java
class MovieChainedComparator implements Comparator<Movie> {
    private List<Comparator<Movie>> listComparators;
    @SafeVarargs
    public MovieChainedComparator(Comparator<Movie>... comparators) {
        this.listComparators = Arrays.asList(comparators);
    }
    @Override
    public int compare(Movie m1, Movie m2) {
        for (Comparator<Movie> comparator : listComparators) {
            int result = comparator.compare(m1, m2);
            if (result != 0) {
                return result;
            }
        }
        return 0;
    }
}

 class MovieTitleComparator implements Comparator<Movie> {
    @Override
    public int compare(Movie m1, Movie m2) {
        return m1.title.compareTo(m2.title);
    }
}

class MovieRatingComparator implements Comparator<Movie> {
    @Override
    public int compare(Movie m1, Movie m2) {
        return Integer.compare(m1.rating, m2.rating);
    }
}

```


The driver method stays the same and the collections method now takes a `MovieChainedComparator` with multiple `Comparators` in the constructor.

```java
static void sortList( List<Movie> movieList) {



        Collections.sort(movieList, new MovieChainedComparator(
                new MovieRatingComparator().reversed(),
                new MovieTitleComparator()

        ));

        for(int i = 0; i < movieList.size(); i++){
            System.out.println(" " + movieList.get(i).title + " " + movieList.get(i).rating);
        }
    }

    public static void main(String[] args) {
        List<Movie> movieList = new ArrayList<>();
        movieList.add(new Movie("Apples", 4));
        movieList.add(new Movie("Rainman", 4));
        movieList.add(new Movie("Godzilla", 2));
        movieList.add(new Movie("Queen of the South", 4));
        movieList.add(new Movie("Hitman", 3));
        movieList.add(new Movie("Hitman", 2));
        movieList.add(new Movie("Godzilla", 3));
        movieList.add(new Movie("Moana", 4));
        sortList(movieList);
    }
```

Notice the output is reversed on the rating order and than sorted by lexical order with in the rating.

Output:
```text
 Apples 4
 Moana 4
 Queen of the South 4
 Rainman 4
 Godzilla 3
 Hitman 3
 Godzilla 2
 Hitman 2

```


### Example 4 Chaining without a Custom Class

So in example three we saw how we could create a custom class that takes any number of `Comparator<T>` classes in the constructor.  This is a nice way to visualize what happens when we leverage the built in `thenComparing` method provided to us by the `Comparator` class.  However if we don't want to be as verbose we can use the built in methods which I will show examples of below.


We can see that our driver function stays the same and we only make changes to our sortList function.  We need to add our 2 original comparator lambda functions and than chain them together in our collections.sort method call.

```java
 static void sortList( List<Movie> movieList) {
        Comparator<Movie> sortingByTitle =
                (Movie m1, Movie m2)->m1.title.compareTo(m2.title);
        Comparator<Movie> sortingByRating =
                (Movie m1, Movie m2)->Integer.compare(m1.rating, m2.rating);


        Collections.sort(movieList, sortingByRating.reversed().thenComparing(sortingByTitle));

        for(int i = 0; i < movieList.size(); i++){
            System.out.println(" " + movieList.get(i).title + " " + movieList.get(i).rating);
        }
    }

```

See the output is now the same as the example 3.
Output:
```text
 Apples 4
 Moana 4
 Queen of the South 4
 Rainman 4
 Godzilla 3
 Hitman 3
 Godzilla 2
 Hitman 2
```

### Ending remarks

I hope this post has helped better explain how to do comparator functions on custom objects and on how to chain multiple comparator functions together.

Code for these examples can be found on my github [here](https://github.com/Matt-st/Learning-Topics/blob/master/ds-algo/Comparator/src/main/java/)

Thanks for reading!
