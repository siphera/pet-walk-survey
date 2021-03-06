# Pet walk survey

Learning Creating factory functions by spotting owners walking their Cats and Dogs.

See the app in action here [here](https://avermeulen.github.io/pet-walk-survey/) - this one is not using Factory Functions.

## Making code testable

There are a few things that makes the current version of the `pet-walk-survey` application hard to test.

The main things being **application logic and the DOM logic is to coupled** and **Global variables**

### The application logic and the DOM logic is too coupled. 

The code that get values from the screen elements and the logic calculations are too coupled. They are in the same function and there is no easy way to test the one without the other.

### Global variables

The global variables `catsSpottedCounter` and `dogsSpottedCounter` makes it impossible to create independent Unit Tests.

## Making code testable

To make code testable you need to split your DOM code from your application logic. To test the application logic you need to decouple/detangle it from the DOM code. You can unit test DOM code, but this is not our current focus.

Factory functions make it possible to control variable scope. And to expose some functions to control the variables.

Let's look at a factory function that helps to keep count how many apples were eaten.

```javascript
function AppleEater(name){
  
  var applesEaten = 0;
  function eat(){
    applesEaten++;
  }
  function eaten(){
    return applesEaten;
  }
  
  function username(){
    return name;
  }
  
  return {
    eat,
    eaten,
    username
  }
  
}
```

This is looks quite complicated for just incrementing a variable does it? Remember our out aim is testability currently.

Let's look into it:

```javascript

// let's create some apple eaters
var lindani = AppleEater('lindani');
var shannon = AppleEater('shannon');

// we can ask AppleEaters what they have eaten so far.
assert.equal(lindani.eaten(), 0);
assert.equal(shannon.eaten(), 0);

assert.equal(lindani.username(), 'lindani');
assert.equal(shannon.username(), 'shannon');


// An they can eat an apple
lindani.eat();

// Each AppleEater is independent from each oter
assert.equal(lindani.eaten(), 1);
assert.equal(shannon.eaten(), 0);
```

Note:

* Using the `AppleEater` factory function you can create different instances,
* each instance is independent from each other having it own `applesEaten` variable.

As a result the `AppleEater` is testable. 

If you want to create a screen with a list of AppleEaters your DOM code only need to interact with the Factory Function that is already testable and contains all the application logic. The DOM codes role is only to gather and display data.

## Making PetWalkSurvey testable

To make the PetWalkSurvey screens logic testable we need to create a Factory Function that can do four things:

* Record when a dog was walked
* Record when a cat was walked
* Answer how many dogs was walked
* Answer how many cats was walked

Internally it have two variables and it exposes 4 functions.

Start of by writing some Mocha unit tests - use TDD. The tests will initially fail as the Factory Function you are writing doesn't exist.

Write tests for all the scenarios like:

* if no dogs or cats were walked

```javascript
var petWalkSurvey = PetWalkSurvey();
assert.equal(petWalkSurvey.dogCount(), 0);
assert.equal(petWalkSurvey.catCount(), 0);
```

* if only a dog and no cat was walked

```javascript
var petWalkSurvey = PetWalkSurvey();
petWalkSurvey.catSpotted();
assert.equal(petWalkSurvey.dogCount(), 0);
assert.equal(petWalkSurvey.catCount(), 1);
```

* if only a cat and no dog was walked

```javascript
var petWalkSurvey = PetWalkSurvey();
petWalkSurvey.dogSpotted();
assert.equal(petWalkSurvey.dogCount(), 1);
assert.equal(petWalkSurvey.catCount(), 2);
```

* if a cat and a dog were walked

```javascript
var petWalkSurvey = PetWalkSurvey();

petWalkSurvey.dogSpotted();
petWalkSurvey.catSpotted();

assert.equal(petWalkSurvey.dogCount(), 1);
assert.equal(petWalkSurvey.catCount(), 1);
```

* if many cats and dogs were walked.

```javascript
var petWalkSurvey = PetWalkSurvey();
petWalkSurvey.dogSpotted();
petWalkSurvey.catSpotted();
petWalkSurvey.catSpotted();
petWalkSurvey.dogSpotted();
petWalkSurvey.dogSpotted();
petWalkSurvey.catSpotted();
petWalkSurvey.dogSpotted();
petWalkSurvey.dogSpotted();
assert.equal(petWalkSurvey.dogCount(), 5);
assert.equal(petWalkSurvey.catCount(), 2);
```
Initially all these tests should fail.

**Note:** Put your PetWalkSurvey Factory Functions code into a different JavaScript file than your DOM code. Otherwise you will get some undefined reference errors in the developer console even if your tests are passing.

Once you are happy with the factory function - link it to your DOM code. Remember that DOM code should just gather and display ata. All data processing should happen in the Factory Function.
