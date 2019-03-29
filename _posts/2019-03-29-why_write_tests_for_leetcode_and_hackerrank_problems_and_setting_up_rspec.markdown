---
layout: post
title:      "Why write tests for Leetcode and HackerRank problems & Setting up RSPEC "
date:       2019-03-29 18:46:54 +0000
permalink:  why_write_tests_for_leetcode_and_hackerrank_problems_and_setting_up_rspec
---


Since graduating from the Flatiron School’s Online Full Stack Web Developer Program I have been doing a slew of Leetcode and HackerRank problems in order to keep my coding skills sharp as I job hunt and figure out what my next personal project will entail.  

In this post I’ll describe why I concluded that it would be best for me to write my own tests and walk you through setting up your own tests for Ruby code with RSpec. 

## Why I decided to write my own tests
The text editors on Leetcode and HackerRank are  . . . fine.  They give you a place to type in your solution and will show you its result near the expected output so you can compare.  

But, they provide no way to introspect on what your code is doing along the way.  You can’t drop in a debugger (for JavaScript) or a binding.pry (for Ruby) to pause the execution of your code and poke around a bit in the context of the pause.  You can’t even sprinkle console.log( ) or puts statements throughout the code to print out different values in context and see if they’re what you’d expect.  This can make finding a solution more frustrating than necessary as all you can do is re-run code after each change to see how the overall output has been affected. 

Frustrated with the provided text editors, I moved on to working out my solutions in online editors like [CodeSandbox]( https://codesandbox.io/) where I could draft my algorithm and print the result of calling it into the console to compare to the answer provided by Leetcode or HackerRank.  CodeSandbox also let me pepper my algorithm with console.log( ) statements to find out more about what my code was doing in context. 

However, I couldn’t save anything.  So anytime it took more than one sitting to figure out a solution I would have to re-write the input values and logging the result to reestablish my pseudo test suite.  Worse, though, was that I would loose my current draft of the algorithm.   Sure, it was incorrect, but it had lots to teach me about what I had already tried and how I was thinking about (and perhaps misunderstanding) the problem.  In addition, CodeSandbox tries to run your code anytime you stop typing.  This caused me trouble when I was trying to set up conditional loops, because the editor would fall into an infinite loop and freeze up before I could write code that would stop the loop after a manageable number of iterations. 

Unsatisfied, I found myself nostalgic for the Flatiron School’s labs where there were clear tests I needed to make pass, I could use various tools to stop my code mid-execution and look around, and I could save my work and comments locally and on GitHub.  Then I realized I could give myself all of that functionality by creating my own repositories with tests mirroring the example input and output provided by Leetcode and HackerRank.  Since then, I’ve been turning Leetcode and HackerRank problems into my own labs and solving them locally before submitting them. 

## Setting up RSpec Tests
Let’s walk through how you could go about creating your own lab by setting up directory with some tests.  I’ve been writing my algorithms in Ruby lately, so we’re going to work with the RSpec testing framework. 

First, create a new directory to hold the lab.
`mkdir  my-new-lab`

Next, if you haven’t yet, install the RSpec gem.
`gem install rspec`

Next, inside the new project directory, run:
`rspec --init`

From here on it’ll be helpful if we have an example problem to build our lab around. Let’s write tests for and solve a challenge that asks us to take an input of an array of integers and modify it in place by moving all the ones in the array to its end.  So, if the input was [4, 1, 5, 6, 1, 7, 0], we’d want our algorithm to return the same array object, but have it look like this: [4, 5, 6, 7, 0 , 1, 1]. 

Inside the spec folder, create a new file named after whatever you plan to call the file that’ll hold the code we’re going to test followed by _spec.
`touch my_methods_spec.rb`

Now, open that file in your preferred text editor and add the following to the spec file:
` require_relative '../my_methods.rb'`

This ensures that our spec has access to the code it’s testing.

Next, let’s create that file that’ll hold the code we want to test. Make sure to move out of the spec directory and create the file in your root directory.
`touch my_methods.rb`

Now we’re ready to write a test! Back in the spec file (`spec/my_methods_spec.rb`) we want to describe how we expect the method we’ll write to behave. We do this with a describe block. 
```
describe "#move_ones" do

end
```

We follow the `describe` method with a string describing what the tests held in the describe block relate to.  In this case, the tests pertain to a method we’re going to call `move_ones`.

Great, now let’s add some tests for `move_ones`, examples in RSpec parlance, with the `it` method. Each it block will create one test or example.  This means that when you run the tests and get that nice RSpec output saying “# examples, # failures” each it statement will correspond to one of the examples the output is reporting on.  And, just like `describe`, we can follow each `it` with a string description.  With `it`, the string will describe what behavior the example is testing. 

For this lab, let’s set up two examples.  Let’s have our first example test that the method’s output is an array like the one passed in as input, but with all its ones at the end and our second example test we modified the original array in place.

```
describe "move_ones" do
  it "returns an array with all the ones at the end" do

  end 

  it "modifies the original array in place" do

  end
end
```

To run this test, we run rspec and then the path to the spec file in the command line.
` rspec spec/my_methods_spec.rb`

If we run the spec now, we’ll get something like this:
```
..

Finished in 0.00541 seconds (files took 0.20439 seconds to load)
2 examples, 0 failures
```

Both of our examples are passing because we haven’t given them any criteria with which to assess our code.  Let’s add some criteria by adding some expectations, i.e. some expressions about how we expect the code to behave. 

To do this, we’ll use the `expect().to` syntax and a couple different matchers.  The value we pass to `expect` is the value we’re assessing in this example.  We’ll follow `expect().to` with a matcher that tells RSpec how we want to compare the value we pass to `expect` with the value we pass to the matcher.  

```
describe "move_ones" do
  array = [4, 1, 5, 6, 1, 7, 0]

  it "returns an array with all the ones at the end" do
    expect(move_ones(array)).to eq([4, 5, 6, 7, 0 , 1, 1])
  end 

  it "modifies the original array in place" do
    expect(move_ones(array)).to be(array)   
  end
end
```

In the first example we used the `eq()` matcher to indicate that we expect the result of `move_ones(array)` to be equal to the array `[4, 5, 6, 7, 0 , 1, 1]`.  This example ensures that our method does that thing we want, i.e. it moves all the ones in an array to the end. 

In the second example we used the `be()` matcher to indicate that we expect the result `move_ones(array)` to be the same array object passed to `move_ones`.  This example ensures that our method modifies the array in place rather than creating and returning a new array.

Check out all the matchers built in to RSpec [here]( https://github.com/rspec/rspec-expectations). 

Awesome, we have our RSpec tests! If we run the test suite from the command line again, we’ll get soemthing like this:

```
FF

Failures:

  1) #move_ones returns an array with all the ones at the end
     Failure/Error: expect(move_ones(array)).to eq([4, 5, 6, 7, 0 , 1, 1])
     
     NoMethodError:
       undefined method `move_ones' for #<RSpec::ExampleGroups::MoveOnes:0x00007fe7e702c548>
     # ./spec/my_methods_spec.rb:6:in `block (2 levels) in <top (required)>'

  2) #move_ones modifies the original array in place
     Failure/Error: expect(move_ones(array)).to be(array)
     
     NoMethodError:
       undefined method `move_ones' for #<RSpec::ExampleGroups::MoveOnes:0x00007fe7e7024fc8>
     # ./spec/my_methods_spec.rb:10:in `block (2 levels) in <top (required)>'

Finished in 0.00482 seconds (files took 0.19223 seconds to load)
2 examples, 2 failures

Failed examples:

rspec ./spec/my_methods_spec.rb:5 # #move_ones returns an array with all the ones at the end
rspec ./spec/my_methods_spec.rb:9 # #move_ones modifies the original array in place
```

Here’s where RSpec shines. Not only does it provide a very well formatted description of which examples failed, but it also gives us descriptive error messages that will help us figure out what’s going wrong in our code.  Right now, we’re getting the error ` NoMethodError: undefined method 'move_ones'` for both examples.  This is because we haven’t written a `move_ones` method in `my_methods.rb` yet. 

Hop into `my_methods.rb` and create a `move_ones` method.

```
def move_ones(array)

end
```

Re-run our tests and you should get new errors for each example.  Woo! New errors mean new information.  The RSpec output shouldn’t be complaining that it can’t find `moves-ones`, but instead it should be upset that `move_ones` didn’t return an array of the values it was expecting and it didn’t return the array that was passed into it. 

Stop reading here and try to write your own `move_ones` so that all the examples in the test suite we’ve built pass.  You can do it!

### My `move_ones`
If you’re curious, here’s how I went about writing my own `move_ones`. 

```
def move_ones(array)
  ones = 0
  array.each {|elem| ones +=1 if elem == 1}

  array.delete(1)

  ones.times do 
    array.push(1)
  end

  array
end
```

I created a counter called ones to keep track of how many ones I found in the array.  Then I used the `each` method to walk through the array and access each of its elements.  If the element had a value of 1, I added one to my count of ones in the array.  After I had counted how many ones were in the array, I deleted all the ones from it.  Then as many times as there were ones in the original array, I added a one to the end of it with the `push` method. Lastly, I returned the altered array.


I hope this post helped you understand why I chose to write my own tests when working on HackerRank and Leetcode problems and how to go about setting up your own RSpec tests. 
