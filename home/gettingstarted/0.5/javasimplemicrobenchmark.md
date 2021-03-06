---
layout: docsdefault05
title: Simple benchmark for java users
permalink: /simplemicrobenchmarkforjava/index.html

smversion: 0.5
partof: getting-started
num: 2
outof: 9
---


Lets assume we want to write and run a simple microbenchmark which tests a method that does operations on a list.
This section shows the basics of how to do this.


## Preparatory steps

ScalaMeter requires at least JRE 7 update 4 and Scala 2.10 to be run.

1. Make sure you have at least JRE 7 update 4 installed on your machine.<br/>
[Download](http://www.java.com) the latest version or update an existing one.

2. Make sure you have at least Scala 2.10 installed on your machine.<br/>
[Download](http://www.scala-lang.org/downloads) and install Scala 2.10 if you don't have a newer version.

3. Go to the [download](/home/download/) section and download the latest release of ScalaMeter.

4. Create a new project and a new file named `ListBenchmark.java` in your editor.


## Implementing the microbenchmark

A ScalaMeter represents java performance tests with the `JavaPerformanceTest` abstract class -- to implement a
java performance test, we have to extend this class or another class extending `JavaPerformanceTest`.

    import org.scalameter.QuickBenchmark;

    class public class ListBenchmark
    extends Quickbenchmark {

      // multiple tests can be specified here

    }

The `QuickBenchmark` abstract class is a subclass of `JavaPerformanceTest` which configures the tests to simply run and output them in the terminal.

A test in a `JavaPerformanceTest` is defined by an inner class which implements the `Using` interface. This interface helps to define the correct methods for a java performance test.


    import org.scalameter.QuickBenchmark;
    import org.scalameter.javaApi.*;
    import java.util.LinkedList;

    class public class ListBenchmark
    extends Quickbenchmark {
        ListTest implements 
		org.scalameter.javaApi.Using<LinkedList<Integer>, LinkedList<Integer>> {
        
        }
    }


Most benchmarks need input data that they are executed on.
To define input data in a clean and composable manner ScalaMeter supports data generators
represented by the `Gen` interface. To access these generators in Java, ScalaMeter offers different objects of type `JavaGenerator`.

For now, we create a `RangeGen` generator and then create a `JavaGenerator<LinkedList<Integer>>` from it.


    import org.scalameter.QuickBenchmark;
    import org.scalameter.javaApi.*;
    import java.util.LinkedList;
    
    class public class ListBenchmark
    extends Quickbenchmark {
        ListBench implements 
		org.scalameter.javaApi.Using<LinkedList<Integer>, LinkedList<Integer>> {
            public JavaGenerator<Integer> generator(){
                JavaGenerator<Integer> range = new Range("Range", 1, 21, 4);
                return new Collections(range).lists();
            }
        }
    }


`RangeGen` creates a generator which generates integers the range from `1` to `21` in steps of `4`.
The `Collections` class allows to create other generator types from an Integer generator. Here, we created a `JavaGenerator<LinkedList<Integer>>` using the `lists()` method.
The generator must have the same type as the first type in `Using`. Here, it is `LinkedList<Integer>`.

Now, we define the method that will be tested. It will take as parameter an element of type `LinkedList<Integer>` and is called `snippet`.


    import org.scalameter.QuickBenchmark;
    import org.scalameter.javaApi.*;
    import java.util.LinkedList;

    public class ListBenchmark
    extends QuickBenchmark {
        public class ListBench implements 
		org.scalameter.javaApi.Using<LinkedList<Integer>, LinkedList<Integer>> {
            public JavaGenerator<LinkedList<Integer>> generator(){
                JavaGenerator<Integer> range = new RangeGen("Range", 1, 21, 4);
                    return new Collections(range).lists();
            }
            
            public LinkedList<Integer> snippet(LinkedList<Integer> in){
                LinkedList<Integer> ret = new LinkedList<Integer>();
                for(int i = 0; i < in.size(); i++){
                    Integer a = in.get(i);
                    ret.add(a+1);
                }
                return ret;
            }
        }
    }


To run the test, you can use [SBT](http://www.scala-sbt.org/) build tool. 
Simply use the command `sbt test`.
See the section [SBT integration](/home/gettingstarted/0.5/sbt/) for details.

You can also add methods `public void beforeTests()` and `public void afterTests()` in the using block to do some operations like a connection to a server before the tests begin.
If you have operations to do before and after each test, you can define methods `public void setup(T t)` and `public void teardown(T t)`, with `t` being an element of type `T` generated by your generator.


