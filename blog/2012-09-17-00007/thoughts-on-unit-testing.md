# Thoughts on Unit Testing
A unit test is a piece of code written by a developer for a developer. The intention of this piece of code is to prove that the code does what the developer intents to do ([Pragmatic Unit Testing in C#](http://www.pragmaticprogrammer.com/titles/utc2/)). Unit tests are not substitutes for requirement gathering or QA, they are an additional tool that developers can use to produce better systems.


## But why would I write such a piece code?

A lot of developers complain that when they write a unit test for the first time they fell kind of silly, like there is no value in writing a test to prove a piece of code that they just wrote. For example, let's say that you write a new method to add a record to the database and then you write the unit test to verify this code (e.g. the record is in fact added to the database.) Suppose you spend half your morning writing this code and then its unit test. Well *off course* that when you write the test it will pass -- you just wrote the darn code!

What a lot of developers don't realize is that they do gain a whole lot when they write unit tests:

1. Once you write the test you can run it and test your object directly without having to fire your application, login, launch the customer screen, click new, populate some data, hit save, and the look in the database to verify if it was saved. Once this test is available to you just need to run it and let it do these steps for you automatically. It’s amazing how much time you will save on the long run plus not to mention that you don’t need to manually repeat those redundant steps every time you want to test the customer class.

2. A few days later, when you come to this code for what ever reason you will have a verifiable piece of code to tell you what the code is supposed to do. The code might *not* look as familiar to you as it did a few days/weeks/months ago. Yet, you will have a verifiable piece of code that tells you what it used to do.

3. If something accidentally breaks this code (e.g. a new constrain the database prevents a NULL value in a field that used to allow NULLS) you will have a way to know that this piece of code that used to work now it doesn’t. You won’t need to wait for a user to fire the application and tell you that he is having problems saving new customers. You will know sooner rather than later.


## Did you say I can unit test against a database?

If you've read some of the classic literature on unit tests you are probably thinking that I've just committed some horrible sins just by saying that my unit test was for adding a record to the database. Aren't unit tests supposed to be independent of external components like databases? Isn't it a big no-no talking about unit tests and databases on the same sentence?

The truth is that there are two camps on this issue. One camp says that unit tests should not have dependencies on external components. The other camp says that there is not such thing, that all code has dependencies. As you can guess, I am on the second one.

The way I see it is that since most of us write business applications that do pretty much database access (reading, creating, updating, and deleting) let's just bite the bullet and write unit tests that rely on the database. After all, a lot of the problems that happen in these applications *are* related to the database (e.g. changes to the data structures and stored procedures, connectivity issues, et cetera.)

Now, you do pay a price for writing tests against a database. For example your unit tests will be slower than if they don't hit a database, changes in the database will break your unit tests even though the problem might not be in your code but in the database itself. But even with that, I think it's worth doing it that way.


## To mock or not to mock, that is the question

Martin Fowler has a great article called [Mocks Aren’t Stubs](http://www.martinfowler.com/articles/mocksArentStubs.html). In this article he describes the differences between these two types of helpers (mocks and stubs) and the ramifications that come when using them.

Fowler starts by pointing out that there are in fact two main differences between mocks and stubs. One is that *stubs are used for state verification* while *mocks are used for behavior verification*. The second difference is the whole approach to Test Driven Development that each of these techniques brings to the table.

I highly recommend his article for any one interested in understanding why and when to use mock objects.


## But I already test my code, how is this different from what I do?

The truth is that most likely you already do something very similar to unit testing. Most developers write little code snippets, SQL scripts, and test utilities to find problems and verify their code as they write it. However, we usually do this rather informally and we treat this kind of code as throw away code.

So instead of throwing away our test code with unit testing we make it part of the <em>real </em>code. We keep it under source control, we run it on a constant basis, and we maintain it to reflect changes to the application code.


## How should I start?

Start simple. Do small tests first, even if they just test the obvious (e.g. a record was added to the database after you execute your Save method.) 

Read some of the literature on unit testing, in particular check the first chapters of [Test Driven Development: By Example](http://www.amazon.com/Test-Driven-Development-Addison-Wesley-Signature/dp/0321146530)  (TDD) and [Pragmatic Unit Testing in C#](http://www.pragmaticprogrammer.com/titles/utc2/) 

Although I recommend the book on TDD to get familiar with the ultimate goal of unit testing and the rational behind it, I don't recommend that you try to start doing TDD right of the bat. Get comfortable with the concept and then experiment with the different styles of writing tests.