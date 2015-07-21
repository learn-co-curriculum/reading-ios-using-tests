# Running Tests

## Objectives:

1. Grasp the importance of testing in software development.
2. Know the names of the testing frameworks you'll be using (Specta and Expecta).
3. Locate the unit test files within an Xcode project.
4. Comprehend the goals of each test.
5. Run the test suite (`⌘``U`) and interpret the results of a test.

## The Case For Testing

From this point forward, most of the labs you'll be going through will include a group of test files. These are identifiable in your project navigator pane as the files in the <#ProjectName#>Tests group with the suffix "Spec". They'll be useful to you for recognizing when you've successfully completed the assignment of lab. 

#### Why We Use Tests

While it's true that the testing suites integrate with Learn.co so that we, your instructors, can track an overview of your progress, the tests are provided to you as a student to facilitate your learning in the labs. Tests are the most precise way for us to explain to you in detail what the individual goals of a lesson are. They also allow you to see for yourself when you've satisfied the material.

Keep in mind, though, that 100% completion on tests is neither required nor expected. The tests are important and you should work hard to get them to pass. However, don't allow yourself to get hung up on getting one last test or two to pass before moving on. Forward momentum is key—if you feel satisfied that you've learned the material and grasp the concepts, keep on going. *Don't let a few failed tests get you discouraged.*

#### Why Developers Use Tests

The ability to read (and eventually to write) tests is a skill that is fundamental to software development. Developers must have reasonable assurance that their software will behave as expected before it gets deployed to the consumer. Would you ship software that does math incorrectly? [Intel did in 1994.](http://www.computerworld.com/article/2515483/enterprise-applications/epic-failures--11-infamous-software-bugs.html?page=3) Would you write guidance software for a space probe that doesn't track units of measure? [NASA did in 1998.](http://www.computerworld.com/article/2515483/enterprise-applications/epic-failures--11-infamous-software-bugs.html) 

Testing is required because **programming is hard**. It's finding the path to success through the route of "10,000 ways that won't work" (attributed [Thomas Edison](https://en.wikiquote.org/wiki/Thomas_Edison)). Without testing the boundaries of failure of the applications that we develop, we can't be certain of their boundaries of success.

## Specta & Expecta

The testing frameworks that we use in the Mobile Development Labs are [Specta](https://cocoapods.org/pods/specta) and [Expecta](https://cocoapods.org/pods/Expecta). These are third-party libraries that we integrate into the labs using the CocoaPods dependency manager. You'll learn all about using CocoaPods later. For now, just recognize that anywhere in the labs or repositories that you see "Pod", it's referring to CocoaPods.

#### Use The `*.xcworkspace` From Now On

When you open your first lab that includes a testing suite, you'll notice that in addition to the `*.xcodeproj` file that houses the lab itself, the lab's directory also houses an `*xcworkspace` file. An `*.xcworkspace` is a container for multiple `*.xcodeproj` files. This is relevant because CocoaPods sets up an additional project folder to handle its third-party framework files. In the case of our preferred testing frameworks, these are Specta, Expecta, and Swizzlean (though you may also see OCHamcrest and OCMock). 

**Note:** *You must open the* `*.xcworkspace` *file in order to run the tests. The compiler will generate a slew of errors if you try running an* `*.xcodeproj` *that depends on CocoaPods.*

#### Why Specta & Expecta?

Most simply—because we like them.

Additionaly, Specta is built on top of Xcode's native testing suite XCTest so it offers native integration to Xcode workflows. Specta also supports asynchronous testing which is useful for applications that connect to the internet or otherwise incorporate multithreading (we'll encounter these scenarios later in the course). Expecta is a "matcher framework" that in our opinion offers the best readibility of testing syntax.

## Reading The `Spec` Files

Comprehending the content of a test file is a skill in itself. We're going to run through the anatomy of a `Spec` file so you'll have a better understanding of what you're looking at.

#### The Import Headers

A `Spec` file begins with import headers just like our Cocoa Touch Classes:

```objc
#import <Foundation/Foundation.h>
#import <Specta/Specta.h>
#define EXP_SHORTHAND   // this sets the Expecta shorthand
#import "Expecta.h"

#import "FISComment.h"  // the name of our class to test
```

You'll notice that the class we're testing, along with any of the classes it depends upon, will be included in the `#import` headers. This is so our `Spec` file has access to the class that's it's meant to test and its dependencies.

#### Implementation Layout

The body of the test file is similar to the body of the `*.m` files in your project.

  * The `SpecBegin(<className>)` and `SpecEnd` are wrappers similar to the `@implementation` and `@end` in your class files and the compiler will complain if these are interrupted or missing.

  * The `it(NSString *name, ^{ … });` blocks are the actual test containers with a string that describes specifically what the test does.

  * The `describe(NSString *name, ^{ … });` blocks are wrappers that prepend a string to the displayed name of all the tests (or `it` blocks) within it. These `name` strings in the `describe` and `it` blocks are used to self-document what each test is intended to check for and what its scope is.

  * The `beforeAll(^{ … });`, `beforeEach(^{ … });`, `afterAll(^{ … });`, and `afterEach(^{ … });` blocks are used for setting up (or resetting) the instance variables to test by and the instances of the test class on which to run the tests. Keep in mind that `beforeAll` and `afterAll` only get dispatched once for all of the tests that they contain, but `beforeEach` and `afterEach` get newly run for every `it` test block.

You'll notice that our labs (in most cases) use `beforeEach` rather than `beforeAll`. This is important because of the asynchronous nature of the tests—there's no set order in which they will run. So, using `beforeEach` will define all of the variables at the beginning of every test `it` block rather than just once for the entire unit test.
 
#### Reading The Description Blocks

The strings inside the `describe` and `it` blocks are responsible for the names of the tests that appear in the test navigator pane next to the red or green lights.

![](https://curriculum-content.s3.amazonaws.com/ios/ios-using-tests/testingLights.png)

The name of this first highlighted test

```objc
test_FISComment__designated_initializer__sets_all_properties_to_submitted_values
```
can be distinguished right from the `Spec` file and can help you identify the content of the specific test that is your immediate goal.

The first `describe` block following the `SpecBegin()` typically contains only the name of the class being tested. The next sentence element is typically a method or property name selected for a group of tests. In this case, our test is targeting the "designated initializer" method.

```objc
SpecBegin(FISComment)

describe(@"FISComment", ^{
    // __block variable declarations
    beforeEach(^{
        // variable definitions
    });
    describe(@"designated initializer", ^{
        it(@"sets all properties to submitted values", ^{
            // test statements go here
        });
    });
});
SpecEnd
```

Then, within the describe block, the `it` block contains the final piece of the description followed by the actual testing conditions. 
 
#### Identifying The Test Objects

Reading a unit test is a little bit more complex than simply reading the `expect(...)` lines, though it is within these statements that the `Spec` file actually calls the methods and checks the result.

Let's take a look at this `FISCommentSpec` file once it's been filled in enough to test the designated initializer:
 
```objc
SpecBegin(FISComment)
describe(@"FISComment", ^{

    __block NSString   *commentID;
    __block NSString   *content;
    __block FISComment *comment;
    
    beforeEach(^{
    
        commentID = @"commentID";
        content   = @"content";
        comment = [[FISComment alloc] initWithCommentID:commentID
                                                content:content];
    });
    describe(@"designated initializer", ^{
        it(@"sets all properties to submitted values", ^{
           expect(comment.commentID).to.equal(commentID);
           expect(comment.content  ).to.equal(content  );
        });
    });
});
SpecEnd
```

You'll notice that the `FISComment` object and the two strings to be passed into the initializer are declared right at the start and then defined in the `beforeEach` block. The `it` block has been filled out with the Expecta shorthand to check that the properties on the `FISComment` object match the values that were submitted to the initializer in the `beforeEach` block.

#### Interpreting The Expecta Shorthand

The readability of the conditional statements is responsible for much of Expecta's popularity and are written in three parts:

  * The conditional will always begin with an `expect(x)` which typically encapsulates the variable being tested. In this case, each of the individual properties of the `FISComment` object are being accessed. 

  * The `.to` is actually just a syntatic sugar that improves readability and acts a placeholder for when the inversion specifier (written as either `.toNot` or `.notTo`) is needed.
  
  * The match specifier, in this case `.equal(y)` which encapsulates the variable or value being compared to, and describes the nature of the comparison.

You'll mostly see straightforward match specifiers used in the unit tests in labs. You can refer to [Expecta’s documentation](https://github.com/specta/expecta) for information on all of the matchers built into the framework.

## Engaging Test Failures

The practice of Test-Driven Development (TDD) doesn't shy away from test failures, but rather dives right into them. It's an important check on the test itself that it will initially fail—if it always passes, then it isn't actually testing anything. This is the origin of the "Red, Green, Refactor" mantra of TDD. "Red" means that the tests should fail initially, and only evaluate to "Green" once the actual code being tested has been written and performs correctly. "Refactor" relies on the accuracy of the test to improve the code being worked on without breaking its functionality. By providing the tests to you already written, we are, in effect, setting you up at the beginning of the "Green" stage of this cycle. (We'll teach you how to compose appropriate tests for your code later in the course.)

**Note:** *To run your application with the tests, use the shortcut command* `⌘``U`.

#### Interpreting The Results

Tests which pass will conclude with a friendly check-mark inside a green diamond displayed next to the test's name in the test navigator pane. Failed tests will show an "x" within a red diamond instead, the details of which can be reviewed through the code editor or through the result navigator pane.

![](https://curriculum-content.s3.amazonaws.com/ios/ios-using-tests/testingCodeEditor.png)

*Test failure details displayed in the Code Editor.*

![](https://curriculum-content.s3.amazonaws.com/ios/ios-using-tests/testingResultsPane.png)

*Test failure details displayed in the Result navigator.*

When a Specta test fails, a red highlight will appear over the `expect(...)` line followed by a banner that will read out why the test failed. The nature of the discrepancy here can give you valuable insight about how to tweak your code.

*Part of this reading was adapted by Mark Edward Murray from his blog post [Introduction to Specta/Expecta](https://medium.com/@MarkEdwardMurray/introduction-to-specta-expecta-edb20c331226).*