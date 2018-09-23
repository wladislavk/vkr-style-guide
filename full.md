Preface
=======

This style guide was created with the purpose of unification of coding
style across members of development teams that deal with low-concurrency
single-threaded application software. This guide is highly opinionated
and is not intended to cover all possible coding methods and patterns.

The initial version of this guide was written specifically for PHP,
however it may be used for any object-oriented language with mandatory
or optional static typing, e.g. Java or TypeScript. It can also be used
for languages with dynamic typing, e.g. Javascript or Python, albeit
with a few changes. All examples are in PHP, unless otherwise stated.

Most patterns set in this guide may be used with other types of
software, e.g. high-concurrency frontend frameworks that support
service-oriented architecture. It is assumed by default that the code is
not intended for re-use, some patterns may differ for reusable code.

This guide aims to set logical patterns rather than language-specific
details, so it purposefully omits editing technicalities such as
indentation, placement of whitespaces or newline characters. Developers
are encouraged to use whatever editing standard is considered the most
popular in the ecosystem of the language they use.

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
"SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in
[RFC 2119](http://tools.ietf.org/html/rfc2119).

General principles
==================

The code MUST NOT be copy-pasted, ever. Use decoupling, extension or
polymorphism.

The least privilege principle MUST be honored. All methods and
properties MUST be private by default. Public properties MUST NOT be
used outside data services. If a class is abstract, its methods and
properties still MUST be private, not protected, unless they are
actually used by children.

All SOLID principles MUST be honored.

One line of code SHOULD NOT include more than one statement. Longer
code with shorter lines is easier to read and debug.

## Eloquency

Unused code MUST be deleted. If a coder is sure that the code will be
used in the nearest future but has to commit to VCS while the code is
not used, they SHOULD place a TODO comment above the code.

Public methods without public calls MUST be made private. Hierarchies
with single implementation SHOULD be flattened. Conditions that are
always true or always false MUST be deleted. Methods that return a value
that never gets used by their callers MUST return `void` instead.

# Naming conventions

## Abbreviations

Class, method or variable names MUST NOT be abbreviated or shortened
unless the meaning is perfectly clear. How to determine is the meaning
perfectly clear or not? If you can say the abbreviation out loud and you
will be understood, then it's perfectly clear. For example, if you
pronounce USA as in 'yoo-es-ey', you will be understood, so it's not
necessary to write 'United States Of America'. In software development,
examples of perfectly clear abbreviations and shortenings are 'DB' for
database and 'repo' for repository.

However, names SHOULD be terse and SHOULD NOT be more than 30 characters
long. An exception is made for test classes and methods because they are
never called directly.

## Prefixes and suffixes

`Abstract` prefix MUST be used for abstract classes.

`Interface` or `-able` suffix MUST be used for interfaces.

`Trait` suffix MUST be used for traits.

`Exception` suffix MUST be used for exception classes.

`Service`, `-er` or `-or` suffix SHOULD be used for services.

`Data` suffix MAY be used for data services.

`Factory` suffix SHOULD be used for factories.

Class names SHOULD be nouns or noun phrases, while method names SHOULD
be verbs or verb phrases.

If there is a choice whether to use singular or plural word for class,
singular SHOULD be used, with the exception of words that are used
mostly in plural, such as 'data'.

# Code blocks

The code MUST NOT have more than two levels of code blocks, not counting
classes and named methods. A code block is defined as a conditional
(including exception-catching conditionals), a loop or a lambda. If
there is a need for creating third level of code block, extract method
or class.

Classes and methods
===================

# Classes

Every file that does not produce output and does not contain a template
MUST have one and only one class or interface.

Classes MUST be namespaced.

A class MUST NOT be longer than 300 lines in total. A class SHOULD NOT
be longer than 200 lines in total. Exceptions: data services with lots
of properties and no logic, and classes with extensive annotations.

## Static classes

If a class has at least one static method, all other methods MUST be
static in order to evade confusion.

Generally, static classes SHOULD NOT be created, but there are some
notable exceptions when a static class creates more terse syntax for
very common operations.

A static class MUST NOT contain any logic and MUST reside in a dedicated
namespace. Static classes cannot be properly unit-tested, so they SHOULD
have a corresponding non-static service that would contain all of the
class's logic.

## Final classes

In general, it SHOULD be assumed that every non-abstract class is final.
However, `final` keyword is dangerous. It MAY be used in controllers,
static classes and data services. A regular service MUST NOT use `final`
unless it is guaranteed that it never gets injected into another
service, and is only required from controllers. Otherwise, it will be
hard to mock it in a unit test.

## Interfaces and traits

Interfaces are of little use in application code that is not intended
for re-use. The need for interface may mean one of the two things:
1) we have a highly decoupled module that can be moved to a library of
its own,
2) we are trying to implement a hierarchy but we are doing it the wrong
way so our topmost class does not have a public implementation.

Interfaces SHOULD NOT be used outside of reusable code, but there is one
exception: immutable collections of contexts SHOULD be declared
interfaces for clarity.

A trait is a collection of method that can be inserted into a class.
Traits, as interfaces, SHOULD NOT be used in non-reusable code. If a
trait is created for whatever reason, it MUST have a corresponding
interface to provide the possibility of type-hinting.

# Methods and properties

A method MUST NOT be longer than 50 lines, excluding docblock. A method
SHOULD NOT be longer than 30 lines, excluding docblock.

## Type safety

If the language provides the possibility to type-hint, type hinting
SHOULD be used whenever possible. All arguments and all return types,
including `void`, SHOULD be type-hinted. Docblocks SHOULD NOT be
understood as a replacement for type hints.

There are several exceptions to this rule. `null` MAY be used with any
argument type or return type, most languages provide null-optional
type hints.

It is RECOMMENDED to use special prefix `T` for method names and
argument names in case if either argument type or return type is
generic by design. Generic arguments and methods provide replacement
for overloading in languages that do not support it.

In the absence of both the type hint and the `T` prefix it is assumed
that the coder forgot the type hint. However, lack of type hint MAY
happen by necessity if the method overrides a method of parent class
from the framework or from a third-party library.

Collection type safety SHOULD be provided. A collection is type-safe
if all its elements have the same type. In case of collections,
elements of type `null` SHOULD NOT be used.

## Boolean arguments

Boolean arguments SHOULD NOT be used. Methods with boolean arguments
SHOULD be refactored in one of the following ways: move the condition
to the caller, or create an additional method.

## Boolean returns

If a method returns a boolean, it SHOULD be named as a yes/no question,
for example, prefixes `is` or `has` MAY be used. Booleans MUST NOT be
used as a replacement for exception handling, nor is boolean `false` a
replacement for `null`.

Constants
=========

All "magical" strings and numbers MUST be wrapped into constants.

All constants MUST be wrapped inside classes or interfaces.

String literals SHOULD be wrapped into constants with following
exceptions: date and time strings, database queries and string literals
that are commonly used by standard library functions. Empty strings
SHOULD NOT be wrapped. There SHOULD NOT be more than 10 string literals
per class, not counting empty strings, outside test code.

Numbers SHOULD be wrapped into constants, unless their meaning is
obvious: e.g. 365 in the context of dates, 0 as an initial value of an
integer variable etc.

Constants MAY be placed into any class that uses them, or, if they have
application-wide use and can be easily grouped, a constant interface MAY
be created.

However, having too many constant collections that get re-used often is
a potential problem that MAY indicate the need to move them into
database.

Operators
=========

Some operators and keywords, although present in many programming
languages, are unnecessary and SHOULD NOT be used.

## ELSE operator

An `else` operator signals that the method is too long and should be
divided into smaller methods. All `else` operators SHOULD be swapped for
`return` operators. The only place where an `else` operator is
appropriate is template code where it is impossible to return early.

## ELSEIF / ELSE IF / ELSIF / ELIF operator

An `elseif` operator is undesirable for the same reason as `else`
operator. Sequences of `elseif`'s SHOULD be replaced by `switch`'s
wherever appropriate. There is a single case where `elseif` MAY be used
outside of template code - in factories, together with `instanceof`
operator, as it is impossible to use `switch` in this case:
```
if ($foo instanceof Bar) {
} elseif ($foo instanceof Baz) {
}
```

## SWITCH, CASE and DEFAULT operators

`switch/case` operators are just `elseif`'s with better syntax. They
SHOULD be used in factories and templates and SHOULD NOT be used
anywhere else. `return` rather than `break` SHOULD be used inside `case`
body, therefore `default` operator SHOULD NOT ever be used outside
templates.

## Ternary operator

Ternary operator is nothing more than `else` in disguise, besides, it
usually violates "one statement per line" principle. Ternary operators
SHOULD NOT be used outside of templates.

## CONTINUE and BREAK operators

Both `continue` and `break` operators inside cycles are signals for
methods that are too long, therefore they SHOULD NOT be used, use
`return` instead.

## WHILE and DO WHILE operators

Both of these are dangerous because they can lead to infinite cycles,
therefore they SHOULD NOT be used, use `foreach` and `for` instead.

## INSTANCEOF operator

Outside of factories, using `instanceof` means that a polymorphic
hierarchy is missing. `instanceof` SHOULD NOT be used outside of
factories, and if there is no simple way to get rid of it, then
refactoring is due.

Exceptions
==========

All errors MUST be handled using the language exception mechanism.

Custom code SHOULD NOT throw pre-defined language or framework
exceptions, create custom exception sub-types instead. If a custom
non-controller code expects a non-custom exception and then wants to
re-throw it, it still SHOULD use a custom exception, not the one that
was caught.

The majority of exception-handling code SHOULD lie in the controller or
in a special service created just for that purpose. The
exception-handling code SHOULD differentiate between custom and generic
exceptions.

All exceptions that are mentioned in docblock annotations MUST be caught
somewhere in the code, presumably in the controller, except for runtime
exceptions, that MUST NOT be caught and are intended to display generic
errors to the client. If a coder wants to throw an uncatchable-by-design
exception, they MUST do it by creating a custom runtime exception class.

`try` operators MUST extend over as few lines of code as possible.
Ideally `try` operators SHOULD NOT extend over more than one line.

Templates
=========

Templates are pieces of code that are intended to be displayed to the
user in any form. Templates are usually written in a domain-specific
language, but even if this is not the case, they are to be treated as
a separate entity that SHOULD be decoupled from the rest of the code as
much as possible.

This guide does not concern itself with templates, because template
coding uses different sets of rules.

Templates MAY contain logic, however their logic is different from the
main application logic. Templates MUST NOT contain any logic that is
either too complex or is not directly related to the presentation. If
the form of presentation is determined based on a complex condition,
this condition SHOULD be evaluated and saved into a boolean variable
before being passed to the template.

All language strings that are directly shown to the user SHOULD be
understood as parts of template logic and SHOULD NOT be referred to
outside of templates to provide easier i18n. The exception from this
rule is generic exception messages that MAY be passed straight to the
user in case of runtime exception.

Comments and docblocks
======================

# Comments

Comments SHOULD be used sparingly. Good code documents itself, and is
also documented by tests, therefore comments SHOULD NOT be the primary
means of providing low-level documentation. The primary usage of
comments is for:

- documenting non-evident lines of code
- documenting the code that is required to override quirks or bugs found
in the framework or in third-party libraries
- TODO comments that outline incomplete or quirky code that will be
fixed in the future.

A name is always better than a comment, so in case when the code cannot
be made readable (e.g. regular expressions), it is RECOMMENDED to wrap
it into a one-line private method.

Commented out code MUST NOT be committed under any circumstances.

# Docblocks

Docblocks MUST be present for every class method except methods that
neither take arguments nor return a value. Also, docblocks MUST be
present for every class property.

There are certain methods, that can return any subtype of a given
supertype, but only one type per call, for example a method that
receives class name as an argument and returns an object of that class.
If this is the case, every call to such method inside a method body MUST
contain its own docblock line, e.g.
```
/** Foo|null $foo */
$foo = Foo::find($id);
```

All docblocks MUST contain all possible types of properties,
arguments and return values. If an argument, property or return value is
an array, docblocks SHOULD specify its element types, such as
`string[]`, rather than just `array`. Special type `mixed` SHOULD NOT be
used, except for generics. If a type is nullable, use `type|null`
format.

`throws` annotations MUST be used on a method, if:
1) the method throws an exception and it is uncaught in the same method,
or
2) one of the method's callees throws an exception and it is uncaught
neither in the callee nor in the method, which applies to callees in
vendor packages as well, but
3) if an exception is a runtime exception, the method MUST NOT use
`throws` annotation.

Types of classes
================

# Controllers

The word "controller" here should not be understood exclusively in terms
of the MVC pattern. Any kind of code that acts as an entry point to the
application as a whole or one of its parts and deals with external input
and output, is a "controller", as far as this document is concerned.
Common examples of controllers include middleware classes, console
commands and frontend components.

Controllers SHOULD be skinny, which means 'as short as possible'.
A controller normally MUST NOT have conditional logic, with the
exception of a simple conditional that results in immediate early
return, such as
```
try {
    $myService->doSomething();
} catch (MyException $e) {
    return ApiResponse::error($e->getMessage());
}
```

Controllers SHOULD NOT contain loops.

Controllers MUST NOT contain private or protected methods. All public
methods MUST be called only from a source that is external to the
application code, e.g. from the framework or straight from the OS.

Controllers MAY use private or protected properties populated by a
dependency injection mechanism.

Since controllers are not unit-testable, they MAY contain static calls
or unwrapped filesystem calls.

# Services

All development SHOULD be service-oriented which means that services
SHOULD contain all or nearly all of the application's business logic.

All of the following concerns *plain services* that are to be
distinguished from *data services*, that are described in the following
chapter.

Outside unit tests, services MUST be initialized within a special
class that is called *service container*. These containers are usually
provided by frameworks. Any other class MUST inject services, not
initialize them.

## Isolation

A service MUST be either a "plain old" class (not extending anything),
or a part of a simple hierarchy. A service MUST NOT extend a framework
class and SHOULD NOT extend a library class. A service SHOULD NOT inject
any framework dependency. In case a class from the framework is needed,
it is RECOMMENDED to import the part of the framework that contains
that class as a separate library via the package manager.

All objects of the request-response cycle are considered to be parts of
the framework, so manipulation with these objects MUST happen inside
controllers.

## Statelessness

A service MUST be stateless which means that it MUST NOT keep anything
on its properties, except for the data defined in constructor that does
not change during the application's lifetime. Statelessness prevents
temporal coupling (public methods may be called in any order) and
ensures that a service works in the same way whether it is marked as a
singleton or not. In other words, we MUST handle each service as if it
were a singleton, even if it is not.

## Public methods

A service SHOULD have a single public method. This ensures that the
service follows the Single Responsibility Principle and is easy enough
to test. The name of the public method SHOULD correspond with the name
of the service, but it SHOULD be a verb phrase, e.g. a service named
`ConditionRetriever` SHOULD have a public method named
`retrieveConditions`. The service MUST NOT have more than three public
methods and MUST NOT have any setter methods to keep stateless.

Public methods SHOULD NOT be called in protected or private context,
as doing so indicates that a class should be extracted.

## Few dependencies

A service MUST NOT have more than five dependencies on other objects,
and SHOULD NOT have more than three dependencies on other objects to
follow the Single Responsibility Principle and be easily testable.
A service MAY have additional non-object dependencies such as global
config parameters.

## Simple constructor

Constructors and all their private callees SHOULD stay simple and
logic-less to provide easier testing.

## Service constants

A service MUST NOT define any public constants. If a constant is used
by more than one class / hierarchy, it MAY be placed either into a
controller or a data service or a separate interface.

## Service hierarchies

Polymorphic hierarchies SHOULD be used to prevent code duplication and
provide cleaner and easy-to-test code. A hierarchy SHOULD be created
when:
1) there is some code duplication among different methods of a service;
2) there are similarly-named methods calling a single method from the
same service or another service.

Every hierarchy MUST have an abstract class at its top. All hierarchy
members, including abstract classes, MUST reside under a common
namespace that is dedicated exclusively to the members of this
hierarchy.

Every abstract class MUST have at least one non-abstract public
method, if only a constructor. Abstract public methods SHOULD NOT be
used, as they point to violations of the hierarchy-wide Single
Responsibility Principle. All abstract methods SHOULD be defined as
protected. For the same reason it is NOT RECOMMENDED to define public
methods in non-abstract hierarchy members.

Unless the application is intended for re-use, hierarchies SHOULD NOT be
created 'just in case'. If there is just one implementation in the
present, it should be refactored into a hierarchy as soon as the second
implementation appears, but not earlier.

## Factories

A factory is a class that returns objects of other classes. Every
hierarchy needs a factory. A factory SHOULD be created in one of two
cases:
1) a case of polymorphism where an abstract method of a certain
hierarchy needs to be called and the implementation is chosen based on
some condition;
2) all implementations of a particular hierarchy need to be used in a
sequence.

Factories MAY reside in a namespace of their own or use the hierarchy
namespace. Certain operators such as `switch`, `elseif` and `instanceof`
MAY be used in factories but SHOULD NOT be used in other non-template
code.

## Database access services

Database access services, also known as repository classes, are plain
services that communicate with ORM by forming database queries and
executing them. Unlike regular services, database access services
contain big number of calls to ORM methods that are hard to mock, they
also tend to contain hidden conditions in form of complex queries.

Database access services SHOULD be moved into a separate namespace
and SHOULD NOT be unit-tested. They MUST NOT contain conditionals;
any conditions MUST be moved to their callers. The purpose of database
access services is mostly to call ORM methods, however in some
situations ORM methods, especially those that do not deal with
building database queries, MAY be placed in regular services.

Database access services SHOULD be treated as regular services in other
ways, e.g. they MUST be injected, not initialized, and SHOULD be
understood as singletons.

# Data services

A data service is a class that is mostly used to keep some data. Data
services are not unit-testable and MUST NOT contain business logic.
Model classes in the MVC architecture are a case of data services,
however a model is intended to be persistent, while a non-model data
service is volatile.

Models SHOULD be named as singular versions of respective DB tables.

Data services MUST be located in namespaces that are separate from plain
services and controllers. Data services SHOULD NOT be injected as
constructor dependencies, all instances of a data service SHOULD be
created via `new` keyword. Therefore, data services SHOULD NOT be mocked
in a unit test.

Every field of a data service MUST have its own docblock, MUST be
type-safe (not counting nulls) and should be initialized with a default
value. If a field of a data service is a collection, it SHOULD have all
elements of the same type.

There MAY exist a hierarchy of data services should the need arise.

## Transforming non-enumerated collections

A non-enumerated collection is a composite type of data that allows to
refer to its elements using arbitrary keys and does not implement
whitelist approach to these keys. Examples of non-enumerated collections
include PHP associative arrays, Javascript object literals, Python
dictionaries, "standard class" objects etc.

Non-enumerated collections SHOULD NOT be used except in following cases:
1) as a class constant, since in some languages enumerated objects
cannot be marked constants;
2) if a framework or a third-party library accepts it as an argument;
3) if a framework or a third-party library returns it on call to one
of its methods.

In all other cases, data services SHOULD be used.

In the latter two cases, it is RECOMMENDED to create a data service
with public transformer methods that transform it into a
non-enumerated collection or transform the collection back into the
service object and call these methods immediately before passing the
collection to a third-party method, or immediately after retrieving
the collection from such method. Whenever these transformer methods
require logic, they SHOULD be moved to separate non-data service
classes.

## Getters and setters

Sometimes, a common operation needs to be performed on an element before
it is added to a data service. Also, using methods rather than public
properties gives more convenient syntax to the caller and provides
methods to better ensure type-safety. Therefore, getters and setters MAY
be added to a data service.

Once a field contains a getter, it MUST contain a setter, and vice
versa. Once a field contains a getter and a setter, it MUST be declared
private or protected.

Setters SHOULD return "this" pointer, as it allows chaining of setters.

Database manipulation
=====================

This style guide does not deal with the database architecture; however,
certain rules are used whenever working with persistent storage, no
matter what type of database is being used.

One model class per table SHOULD be created. One database access
service class per table SHOULD be created, given that any custom code
is needed to deal with this table.

Custom repositories and pieces of logic on the database side, such as
SQL views, functions and stored procedures SHOULD NOT be used, unless
proven that their use leads to performance gain, as they decrease
readability and testability of the code.

Queries SHOULD be as simple as possible unless proven that complex query
will give notable performance gain. Dividing long queries is beneficial
both for testability and readability, and sometimes even gives better
performance.

Queries SHOULD NOT contain operations that can be labelled as 'logical'
because this logic is much easier to test if it is contained in
programming code and perhaps it would perform better in code too. This
applies to conditionals, string manipulation and type casting.

In general, SQL functions SHOULD NOT be used, except for:
- basic aggregate functions such as `SUM`, `COUNT` and `AVG`
- datetime functions.

SQL `LIKE` operator is always a performance hit and it SHOULD be avoided
if possible.

Raw SQL SHOULD NOT be used in general, because it bypasses any safety
mechanisms and abstraction layers that ORMs provide. Besides, ORMs tend
to give better performance. If raw SQL cannot be avoided for some
reason, ANSI-compatible syntax SHOULD be preferred.

Testing
=======

# Unit tests

A unit test is a test that is completely isolated from its environment.
All classes that contain business logic MUST be unit-tested. Classes
that are eligible for unit-testing MUST reside in separate namespaces.
Unit tests for these classes MUST be located either in the same
directory as their SUT classes, or at the same path and namespace
relative to tests root as where SUT classes are relative to production
code root.

Testable classes MUST include all plain services and SHOULD NOT include
controllers, wrappers, test cases, and data services.

The general rule regarding the tests is that all conditionals MUST be
unit-tested with a notable exception of controller conditionals that
return response immediately. Therefore, non-unit-testable classes SHOULD
NOT contain any other conditionals.

All examples below assume that xUnit architecture is used.

## Number of test methods

The number of test methods per each test class can be calculated
precisely based on these rules:

1) one test method MUST call a public SUT method one time only;
2) each conditional outside loop MUST have its own test method;
3) conditionals inside loop MAY be tested in a single method by creating
collection elements with different properties;
4) however, conditionals with `return` operators in their bodies MUST
have their own test methods even if inside loops;
5) each exception thrown MUST have its own test method, both inside
and outside loops;
6) if a conditional contains several conditions, it SHOULD be treated as
if there are several conditionals, so `if (a && b)` is the same as
`if (a) { if (b) {}}`;
7) every public method MUST be tested, even if it doesn't have
conditionals;
8) hidden conditionals SHOULD be covered by tests, e.g. `|` operator
in regular expressions.

## Number of assertions

Any test method MAY have any number of assertions.

Tests MUST assert that:
1) A certain value is returned, if any;
2) A certain exception is thrown, if any, including both its type and
its message.

Tests SHOULD assert that:
1) a public method of another class is called with certain arguments;
2) if a collection is returned, all its fields have certain data.

Collections MAY be asserted using a single assertion or one assertion
per element.

Standard library functions and framework helper functions SHOULD be
trusted, there is no need to test their results.

## Testing hierarchies

Three approaches MAY be used when testing hierarchies.

1) Mock the abstract class while keeping all its methods, then test it
separately from its children.
2) In case if one child is very small, test abstract class conditions
inside that child.
3) In case if all classes are small, test all hierarchy classes inside
the same test class.

## Mockable and non-mockable classes

A unit test MUST mock its mockable dependencies and SHOULD NOT mock its
non-mockable dependencies. A mockable dependency is any class that
cannot be perceived as a simple collection of elements. Therefore,
data services SHOULD NOT be mocked.

## Test wrappers

Some plain functions from standard lib or frameworks try to connect to
an external resource, e.g. filesystem or HTTP request headers. Also,
some third-party libraries include static calls to external APIs that
cannot be called dynamically. Rather than mocking these calls, test
wrappers SHOULD be used in such cases.

A test wrapper is a class that exists solely as a decorator for calling
non-class functions or static methods. A test wrapper MUST NOT contain
any logic. A test wrapper SHOULD be injected via a constructor. Test
wrappers SHOULD reside in their own namespaces. One test wrapper MAY
be used for wrapping more than one call, if calls are of similar nature.

## Setup method in xUnit

A class under test SHOULD be initialized only once per unit test in its
setup method and saved into a test class property. If the class under
test has any constructor dependencies that need to be mocked, all calls
to mocking methods SHOULD be done immediately before class
initialization in the setup method. If there is any other code in
the setup method, it SHOULD be executed before class initialization.

## Separation of mocks

All mocking methods SHOULD be separated from the main test code into
separate methods and/or classes. If mock-related methods are contained
in the same class as tests, they SHOULD be located below setup, teardown
and tests per se.

## Using callbacks for mocked methods

Callbacks SHOULD be used for mocked methods whenever there is a shadow
of doubt over the possibility of changing expected return value or
arguments at runtime.

For example, this code
```
$mock->shouldReceive('foo')->andReturn($this->returnValue);
```
SHOULD need to be rewritten as follows:
```
$mock->shouldReceive('foo')->andReturnUsing(function () {
    return $this->returnValue;
});
```

The mock callback methods MAY either be lambdas or use the `Callback`
suffix.

# Functional tests

Functional tests are to be understood as a replacement of unit tests for
controllers. Unlike unit tests, they do not contain mocks and run on a
real database. Functional tests SHOULD use testing mechanisms provided
by the framework.

Functional tests SHOULD test the following:
- application response code
- application response payload
- changes to external data sources, e.g. database or filesystem
- actions that cannot be tested otherwise, such as sending emails.

The guidelines for determining the number of test methods for a unit
test apply to functional tests as well: each controller conditional with
early return MUST be tested.

If middleware is used, it MUST be tested as well, middleware MAY be
tested on a very simple controller or a special dummy controller.

If a third-party API is called, and this API does not provide any test
credentials, a bridge SHOULD be created together with a dummy
implementation that conforms to the API documentation.

Sometimes a condition cannot be tested functionally. For example, a
third-party service throws an exception that needs to be caught and
an error response provided, however the real-life application can
never throw that exception. In this case, a runtime exception
SHOULD be re-thrown in place of error response.

To perform functional tests, a test database MUST be created and
populated. It is not recommended to re-create the database on each test
run, as this will impact the performance of tests. Coders MUST ensure
that every change to the database that happens during a functional
test is fixed immediately afterwards. In other words, test behavior
MUST NOT depend on the order of execution of test classes or test
methods.

In xUnit architecture, the clean-up code MUST be located in the
teardown method, rather than the setup method, test methods or the class
destructor.

# Acceptance tests

Acceptance testing solves three problems:
1) eases up communication between development team and company
management by clearly showing which features are implemented and in what
way;
2) allows to test borderline interactions that are hard to test
otherwise, such as Ajax calls;
3) allows to test poorly written code that is hard to test otherwise.

New acceptance tests MUST be created for every new feature, with the
following exceptions:
1) the feature is completely technical and does not change user
experience, i.e. moving some data to a new DB table or a change in
algorithm for generation of reports;
2) the feature changes user experience in a way that depends on
subjective human perception, such as: CSS changes, animation, HTML
changes for "tidying up" look and feel of a webpage etc.;
3) the feature changes user experience in a way that is hard to test
because of technical limitations, such as parsing of `window` object by
headless browsers.

New acceptance tests MUST be created for any large-scale migrations of
the code, even if they do not lead to changes in user experience.

There are several approaches to acceptance testing, and this guide does
not specify which one to use. However, there are important points that
are to be observed no matter which approach and technology is used.

Acceptance tests MUST be written in a way that allows them to be
executed on any host. Acceptance tests MUST NOT rely on any data that
is considered private to the application.

## Feature specifications

Feature specifications SHOULD be understood as a separate entity from
the tests themselves. Feature specifications MUST be as simple and
readable as possible, MUST boil down to a simple sequence of commands
and MUST NOT contain conditionals.

Feature specifications MUST NOT be technical, meaning that they cannot
include anything that is not easily understandable to an average end
user. For this reason, database changes and specifics of network
protocol messages MUST NOT be tested directly, the only thing that may
be tested is the impact these changes have on user experience.

## External resources and networks

In case if acceptance tests modify the database, they MUST restore it
to the previous state in the same way as functional tests. In some
cases, it MAY lead to creating new controllers used exclusively for
acceptance testing.

If network protocols are used, it is RECOMMENDED for acceptance tests
to send a custom header with every request so that the main application
understands that the request came from acceptance tests. It is needed
to provide security and mocking, when appropriate.

## Third-party APIs

Work with third-party APIs SHOULD be treated as follows: failures in a
third-party API MUST NOT impact tests, as the test scope is isolated to
the application itself. However, a mechanism MAY be created that would
allow to run tests against the real API, should the need arise.

## What to test and what not to test

Unlike unit tests and functional tests, acceptance tests do not conform
to any strict standard regarding the scope of testing. The general rule
is: test for the things that are perceived to be important.

In general, only the "good" user stories are tested. It means that paths
of user interaction that are expected to result in failures, stay
outside of test scope. "Bad" stories SHOULD be tested in parts of the
application that relate to security (authentication and authorization)
or are known to be prone to bugs.
