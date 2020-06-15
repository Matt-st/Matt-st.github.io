---
layout: post
title: "Building Spring-Boot RESTful API"
date: 2020-06-11
categories: Java, Util, Collections, Comparator, Spring Boot
published: true
---


## Overview
I wanted to take some time and turn a spring boot api I built a couple months ago into an api tutorial.  In this tutorial we will cover building out a simple api and talk through some specific elements such as jackson annotations, Comparator, unit testing, and integration testing.

This api will allow us to search for movies by title name.  We will be using an api that is presented by hacker rank `https://jsonmock.hackerrank.com/api/movies/search/`.  The idea is that we want to search for movies by a string that represents the title and than return those as a list.

Please see here for the [finished product](https://github.com/Matt-st/Learning-Topics/tree/master/spring-boot/cache/ehcache/movie-search-api)


### Building the API
This API is built using maven so let's start with the pom.xml

1. We are using the spring boot starter parent
2. starter-web which brings in all our api annotations
3. starter-test gives us the basic test dependencies
4. Mockito for testing so we can mock external dependencies
5. Caching dependencies so we can implement a cache for the results

```xml

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.matt.movie</groupId>
	<artifactId>movie-search-api</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.6.RELEASE</version>
	</parent>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.mockito</groupId>
			<artifactId>mockito-junit-jupiter</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-cache</artifactId>
		</dependency>
		<dependency>
			<groupId>javax.cache</groupId>
			<artifactId>cache-api</artifactId>
		</dependency>
		<dependency>
			<groupId>org.ehcache</groupId>
			<artifactId>ehcache</artifactId>
			<version>3.7.1</version><!--$NO-MVN-MAN-VER$-->
		</dependency>

	</dependencies>

	<properties>
		<java.version>1.8</java.version>
		<mockito.version>2.24.0</mockito.version>
	</properties>


	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-surefire-plugin</artifactId>
				<version>2.22.0</version>
			</plugin>
		</plugins>
	</build>
</project>

```

Next we will add our spring boot application class.  In this class we have the basic spring boot application annotation and we are creating a bean of a restTemplate object with java configuration.  We will use the restTemplate object later to make our `HttpMethod.GET` calls.

```java
package com.matt.movie;

import java.time.Duration;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.client.RestTemplateBuilder;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
public class MovieSearchAPI {
	
	 public static void main(String[] args) {
	        SpringApplication.run(MovieSearchAPI.class, args);
	    }
	 
	 @Bean
	 public RestTemplate restTemplate(RestTemplateBuilder builder) {

	     return builder.setConnectTimeout(Duration.ofMillis(30000))
	      .setReadTimeout(Duration.ofMillis(30000)).build();
	 }

}

```

Next we create our Controller.  

1. Add our `@RestController` annotation
2. Autowire our Movie Search Service which we will explore later in the post
3. Add `@RequestMapping`  to set the path for HTTP request. Set the method to `GET`
4. ResponseEntity is meant to represent the entire HTTP response. You can control anything that goes into it: status code, headers, and body.


```java
package com.matt.movie.controller;


import com.matt.movie.model.SearchResponse;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import com.matt.movie.service.MovieSearchService;

@RestController
public class MovieSearchController {
	
	@Autowired
	MovieSearchService service;
	
	@RequestMapping(value = "/api/movie/search", method = RequestMethod.GET)
	public ResponseEntity<SearchResponse> getMovieTitles(@RequestParam String title) {
		SearchResponse s = new SearchResponse(service.findMoviesByTitle(title));
		if(s.getTitles().isEmpty()){
			return new ResponseEntity<>(s, HttpStatus.NOT_FOUND);
		}
		
		return new ResponseEntity<>(s, HttpStatus.OK);
	}
	

}

```

Next we create our service interface.  In general we want to code to interfaces instead of concrete implementation.  Why should we do this?

Coding to interfaces promotes loosly coupled code.  A key identifier of whether code is loosly coupled is to see if your code that calls a service knows anything about that service's implementation.  The only thing your calling code should know about your service is it's method signatures. 

MovieService

```java
public interface MovieSearchService {
	
	public List<Movie> findMoviesByTitle(String title);

}

```

MovieServiceImpl

Below you will find some key parts of our service:
1.  We wire up a restTemplate object to make http calls with.
2.  We use the UriComponentsBuilder class to to build out our url with which we pass our query parameters.
`UriComponentsBuilder builder = UriComponentsBuilder.fromHttpUrl(url)
		        .queryParam("Title", title);`
3.  The api provided by hacker rank returns a paginated list of movie results and after making our first query than we call the service again for each subsequent page and add the additional movies to the list of movies.
4. Once we have all of our movies we need to sort them in alphabetical (lexical) order.  To do this we can use the Collections framework method `Collections.sort`.  The sort method can take an array of strings for example of an array of objects as another example.  The sort method can take a comparator as a second argument in the case of sorting objects.  
Below we can see that I have created a Comparator called sortingByTitle and than we add that as the second argument to the Collections.sort method.  This will sort the method in place and returns void.
```java
Comparator<Movie> sortingByTitle =
				Comparator.comparing(Movie::getTitle);
		Collections.sort(titles, sortingByTitle);
```



```java
package com.matt.movie.service;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

import com.matt.movie.model.Movie;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpMethod;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Component;
import org.springframework.web.client.RestTemplate;
import org.springframework.web.util.UriComponentsBuilder;

import com.matt.movie.model.HRMovieResponse;

@Component
public class MovieSearchServiceImpl implements MovieSearchService {
	
	@Autowired
    private RestTemplate restTemplate;
	
	private static String url = "https://jsonmock.hackerrank.com/api/movies/search/";

	public List<Movie> findMoviesByTitle(String title){
		UriComponentsBuilder builder = UriComponentsBuilder.fromHttpUrl(url)
		        .queryParam("Title", title);
		HRMovieResponse response = httpGetByTitle(builder);
		List<Movie> titles = new ArrayList<>();
		for(int j = 1; j <= response.getTotalPages(); j++){
			 builder = UriComponentsBuilder.fromHttpUrl(url)
				        .queryParam("Title", title)
				        .queryParam("page", j);
			 HRMovieResponse resp = httpGetByTitle(builder);
			for (int i = 0; i < resp.getData().size(); i++){
				titles.add(resp.getData().get(i));
			}
		}
		Comparator<Movie> sortingByTitle =
				Comparator.comparing(Movie::getTitle);
		Collections.sort(titles, sortingByTitle);
		return titles;
		
	}

	private HRMovieResponse httpGetByTitle(UriComponentsBuilder builder) {
		HttpHeaders headers = new HttpHeaders();
		headers.set("Accept", MediaType.APPLICATION_JSON_VALUE);

		HttpEntity<?> entity = new HttpEntity<Object>(headers);

		ResponseEntity<HRMovieResponse> response = restTemplate.exchange(
		        builder.toUriString(), 
		        HttpMethod.GET, 
		        entity, 
		        HRMovieResponse.class);
		return response.getBody();
	}

}


```

Domain Objects

Movie Object
Our movie object represents an object with three fields title, imdbId, and year.  These fields are populated by the response from the hacker rank api we are calling.

```json
"titles": [
        {
            "Title": "Amazing Spiderman Syndrome",
            "Year": 2012,
            "imdbID": "tt2586634"
        }
    ]

```

A couple of things to note with this class is the jackson json annotations.  The properties that return in the json response from the hacker rank api are capitalized for "Tilte" and "Year".  We don't want to name our title and year properties with capitals so I added the `@JsonProperty` annotation so that we could map them appropriately when we unmarshal the response from json to java object.

Movie Class

```java
package com.matt.movie.model;

import com.fasterxml.jackson.annotation.JsonProperty;

import java.io.Serializable;

public class Movie {

	@JsonProperty("Title")
	private String title;
	@JsonProperty("Year")
	private int year;
	@JsonProperty("imdbID")
	private String imdbID;
    	

	public String getTitle() {
		return title;
	}
	public void setTitle(String title) {
		this.title = title;
	}
	public int getYear() {
		return year;
	}
	public void setYear(int year) {
		this.year = year;
	}
	public String getImdbID() {
		return imdbID;
	}
	public void setImdbID(String imdbID) {
		this.imdbID = imdbID;
	}

	public Movie(){}
	
	public Movie(String title, int year, String imdbID) {
		super();
		this.title = title;
		this.year = year;
		this.imdbID = imdbID;
	}
	@Override
	public String toString() {
		return "Movie [Title=" + title + ", Year=" + year + ", imdbID="
				+ imdbID + "]";
	}
}


```

A quick note for this class is that we use the `@JsonSetter` annotation as well to make sure we map the values appropriately when we unmarshal from json to java objects.

HRMovieResponse

```java

package com.matt.movie.model;


import com.fasterxml.jackson.annotation.JsonSetter;

import java.util.List;

public class HRMovieResponse {

	private int page;
	private int perPage;
	private int total;
	private int totalPages;
	private List<Movie> data;

	public HRMovieResponse(){}
	
	public HRMovieResponse(int page, int perPage, int total,
						   int totalPages, List<Movie> data) {
		super();
		this.page = page;
		this.perPage = perPage;
		this.total = total;
		this.totalPages = totalPages;
		this.data = data;
	}
	
	public int getPage() {
		return page;
	}
	public void setPage(int page) {
		this.page = page;
	}
	public int getPerPage() {
		return perPage;
	}
	@JsonSetter("per_page")
	public void setPerPage(int perPage) {
		this.perPage = perPage;
	}
	public int getTotal() {
		return total;
	}
	public void setTotal(int total) {
		this.total = total;
	}
	public int getTotalPages() {
		return totalPages;
	}
	@JsonSetter("total_pages")
	public void setTotalPages(int totalPages) {
		this.totalPages = totalPages;
	}
	public List<Movie> getData() {
		return data;
	}
	public void setData(List<Movie> data) {
		this.data = data;
	}
	
	@Override
	public String toString() {
		return "HRMovieResponse [page=" + page + ", per_page=" + perPage
				+ ", total=" + total + ", total_pages=" + totalPages
				+ ", data=" + data + "]";
	}
}


```

### Unit tests
Now that we have implemented our main source code we can add unit tests.

Under src/test/java we can add a unit test class called MovieSearchControllerTest.  This class will test that the controller returns values expected for success and not found.

```java
package com.matt.movie.controller;

import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.when;

import java.util.ArrayList;
import java.util.List;

import com.matt.movie.model.Movie;
import org.assertj.core.api.Assertions;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.MockitoJUnitRunner;
import org.springframework.http.ResponseEntity;

import com.matt.movie.service.MovieSearchService;



@RunWith(MockitoJUnitRunner.class)
public class MovieSearchControllerTest {

	@InjectMocks
	private MovieSearchController controller;
	
	@Mock
	private MovieSearchService service;
	
	@Before
    public void init() {
        MockitoAnnotations.initMocks(this);
    }

	@Test
	public void getMovieTitlesTestofSizeOne(){
		List<Movie> titles = new ArrayList<Movie>();
		Movie m = new Movie();
		m.setImdbID("id");
		m.setTitle("matt");
		m.setYear(2020);
		titles.add(m);
	    when(service.findMoviesByTitle(anyString())).thenReturn(titles);
	    ResponseEntity<List<Movie>> resp = controller.getMovieTitles("s");
	    Assertions.assertThat(resp).isNotNull();
	    Assertions.assertThat(resp.getBody()).size().isEqualTo(1);
	}
	
	@Test
	public void getMovieTitlesTestIsEmpty(){
		List<Movie> titles = new ArrayList<Movie>();
	    when(service.findMoviesByTitle(anyString())).thenReturn(titles);

	    ResponseEntity<List<Movie>> resp = controller.getMovieTitles("s");
	    Assertions.assertThat(resp).isNotNull();
	    Assertions.assertThat(resp.getBody().isEmpty());
	}
}


```

We also want to test our service with this class.

```java
package com.matt.movie.service;

import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.when;

import java.util.ArrayList;
import java.util.List;

import org.assertj.core.api.Assertions;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.ArgumentMatchers;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.MockitoJUnitRunner;
import org.springframework.http.HttpEntity;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.client.RestTemplate;

import com.matt.movie.model.HRMovieResponse;
import com.matt.movie.model.Movie;

@RunWith(MockitoJUnitRunner.class)
public class MovieSearchServiceImplTest {
	
	@InjectMocks
	private MovieSearchServiceImpl service;
	
	@Mock
	private RestTemplate restTemplate;
	
	@Before
    public void init() {
        MockitoAnnotations.initMocks(this);
    }

	@Test
	public void findMoviesByTitleTestofSizeTwo(){
		List<Movie> data = new ArrayList<Movie>();
		data.add(new Movie("Matt's test", 2019, "a234sdg"));
		data.add(new Movie("apple test", 2019, "a234ddg"));
		HRMovieResponse resp = new HRMovieResponse(1, 10, 1, 1, data);
		
		ResponseEntity<HRMovieResponse> response = new ResponseEntity<HRMovieResponse>(resp, HttpStatus.OK);
	    when(restTemplate.
	    		exchange(anyString(), 
	    				ArgumentMatchers.any(HttpMethod.class), 
	    				ArgumentMatchers.any(HttpEntity.class), 
	    				ArgumentMatchers.<Class<HRMovieResponse>>any())).thenReturn(response);

	    List<Movie> titles = service.findMoviesByTitle("a");
	    System.out.println(titles.get(0));
	    System.out.println(titles.get(1));
	    Assertions.assertThat(titles).isNotNull();
	    Assertions.assertThat(titles.size()).isEqualTo(2);
	}
	
}

```

### Integration tests
I wanted to add some simple integration tests for this application.  I just made a single successful call for integration tests which is written in python using the requests library.

```python
import requests
from asserts import assert_true, assert_equal, assert_raises

def main():
    print("Executing request...")
    print("Retrieveing movies that have walk in the title...")
    payload = {'title': 'walk'}
    r = requests.get('http://localhost:8080/api/movie/search', params=payload)

    assert_equal(861, r.text.count("\"")/2 + 1)
    assert_equal(200, r.status_code)
    print("Integration successful.")




if __name__ == "__main__":
    main()

print("Done")

```

### Adding Cacheing to the Movie API


#### What is Cacheing
Caching is normally used to improve performance on a system.  In this case we know that our information is static and that every search takes about 5 - 7 seconds to perform.  To improve the performance we can set up a Cache that saves the information from our previous searchs in a Map with a key-value of String - List<Movie>.  The key which is a String represents the search parameter and the value which is a List<Movie> represents our result for that search.  This will allow our application to match up the current search with previous searches to see if we already have results and return them instead of sending a call to the down stream system.

The cache we build today will allow us to store our results for a set amount of time before they are removed from the cache.  We will specifically be using [Ehcache](https://www.ehcache.org/) today.

Next I will run through the changes which we need to make to our code.

In our maven pom file we need to add the appropriate dependencies.
```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>
</dependency>
<dependency>
	<groupId>javax.cache</groupId>
	<artifactId>cache-api</artifactId>
</dependency>
<dependency>
	<groupId>org.ehcache</groupId>
	<artifactId>ehcache</artifactId>
	<version>3.7.1</version><!--$NO-MVN-MAN-VER$-->
</dependency>

```

In our MovieSearchAPI spring application class we need to add the caching annotation.

```java

@SpringBootApplication
@EnableCaching //Add the spring boot cache annotation
public class MovieSearchAPI {
	
	 public static void main(String[] args) {
	        SpringApplication.run(MovieSearchAPI.class, args);
	    }
	 
	 @Bean
	 public RestTemplate restTemplate(RestTemplateBuilder builder) {

	     return builder.setConnectTimeout(Duration.ofMillis(30000))
	      .setReadTimeout(Duration.ofMillis(30000)).build();
	 }

}
```

In our src/main/resources application.properties file we need to add the property for the caching xml file

```text
spring.application.name=movie-search-api

# Caching config 
spring.cache.jcache.config=classpath:ehcache.xml

```

We also need to add the xml file in the src/main/resources directory called ehcache.xml.  A couple items of note with this configuration:
1.  We create a persistence directory which will be generated at runtime by Ehcache.
2.  We set a cache template which describes how long the cache lasts until expiry.
3.  We create a heap and off heap space for storage of data
4.  We create a configuration for triggering logs based on events.
5.  We also configure our cache with an alias that we reference later in our service interface

```xml
<config
        xmlns:xsi='http://www.w3.org/2001/XMLSchema-instance'
        xmlns='http://www.ehcache.org/v3'
        xsi:schemaLocation="
            http://www.ehcache.org/v3 
            http://www.ehcache.org/schema/ehcache-core-3.7.xsd">

    <!-- Persistent cache directory -->
    <persistence directory="spring-boot-ehcache/cache" />

    <!-- Default cache template -->
    <cache-template name="default">
        <expiry>
            <ttl unit="seconds">60</ttl>
        </expiry>
    
        <listeners>
            <listener>
                <class>com.matt.movie.cache.CacheLogger</class>
                <event-firing-mode>ASYNCHRONOUS</event-firing-mode>
                <event-ordering-mode>UNORDERED</event-ordering-mode>
                <events-to-fire-on>CREATED</events-to-fire-on>
                <events-to-fire-on>EXPIRED</events-to-fire-on>
                <events-to-fire-on>EVICTED</events-to-fire-on>
            </listener>
        </listeners>

        <resources>
            <heap>1000</heap>
            <offheap unit="MB">10</offheap>
        </resources>
    </cache-template>
    
     <!-- Cache configuration -->
    <cache alias="retrieveMovieResultsCache" uses-template="default">
        <key-type>java.lang.String</key-type>
        <value-type>java.util.ArrayList</value-type>
    </cache>
    

</config>

```

We added a custom logger listener which you can see in the above xml configuration.  So now we need to add the associated java class called CacheLogger.  This logger class is triggered on various events configured in the above xml.  The specific events which trigger the logger are CREATED, EXPIRED, and EVICTED.
```java
package com.matt.movie.cache;

import org.ehcache.event.CacheEvent;
import org.ehcache.event.CacheEventListener;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class CacheLogger implements CacheEventListener<Object, Object> {
	  private final Logger LOG = LoggerFactory.getLogger(CacheLogger.class);

	public void onEvent(CacheEvent<? extends Object, ? extends Object> cacheEvent) {
		
		LOG.info("Key: {} | EventType: {} | Old value: {} | New value: {}",
		             cacheEvent.getKey(), cacheEvent.getType(), cacheEvent.getOldValue(), 
		             cacheEvent.getNewValue());

	}
	 
	 
}
```

Below we add the cachable annotation to the MovieSearchService interface.

```java
package com.matt.movie.service;

import java.util.List;

import com.matt.movie.model.Movie;
import org.springframework.cache.annotation.Cacheable;

public interface MovieSearchService {
	//Add the cachable annotation where the key is the String title which is our search parameter
	@Cacheable(value = "retrieveMovieResultsCache", key = "#title")
	public List<Movie> findMoviesByTitle(String title);

}
```

We also need to change our Movie POJO to implement the Serializable interface.  If we don't add this we will get an exception 

```text
org.ehcache.spi.serialization.SerializerException: java.io.NotSerializableException
```

Our Movie class is below:

```java
public class Movie implements Serializable {
...
}

```


### Ending remarks

I hope that this tutorial has been helpful in learning more about spring boot restful services, Comparators, cacheing, and testing.  I enjoyed putting this together so thanks for reading!

