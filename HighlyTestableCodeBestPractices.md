## key criteria for writing highly testable code

### 0x01

- seperation of concerns
- object-oriented code design
- loose coupling/dependency injection
- elimination of globals
- api name conventions
- clear code and documentation

#### best practices for clear code authorship are followed, at a minimum:

1. Public classes, properties, methods, adn parameters should be documented using a standard code documentation format;
2. Variables and parameters should be clear and understandable, with no abbreviations, truncations, or non-standard terminology that will cause the meaning of the code to be lost or distorted;
3. Code comments should be provided for potentially confusing or odd-looking code, describing why (not what) the code is doing;

**Refrences:**[Testable code best practices](https://www.sitepen.com/blog/2014/07/11/testable-code-best-practices/)

### 0x02

#### 2.1 terminologies

test driven design, pair-programming, and code-review

#### 2.2 

##### Problems of Dirty Code Base

Develping new software isn't just about to accomplish bussiness requirments within the given time frame.

> **NOTE:**这直接影响其它人能否顺利地阅读你写的代码，或团队新人融入的速度；

##### Isn't unit test enough？

- quality of the tests
- coverage of the tests

> **NOTE:** Unit tests are tools that help you to test your code whether it behaves the way you want or not. It does nothing else.
> 
> Having unit tests does not prove that you followed TDD(test driven design) practices.

##### unit testing vs test driven design

Unfortunately, many developers still do not know or do not think about the difference. The definition of testing methodologies differ greatly between two designs. As I mentioned earlier, 「Unit Testing」 is the `practice` of testing your own code to understand whether it behaves the way you want or not. On the other hand, 「Test Driven Design」is the `discipline` of using **Unit Testing** when you design the software. This small difference actually makes big impact on your software design.

> **NOTE:** First rule of writing testabale code to me is having a good dependency management  whthin the code base, or at least having the least possible dependencies in your object/module relations.