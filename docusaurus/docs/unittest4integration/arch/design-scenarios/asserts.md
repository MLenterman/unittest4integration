---
sidebar_position: 20
---

# Asserts
A good unittest should test a small functional unit for a single specific behavior. A functional unit should ideally have unittests for every execution flow. This includes both the happy flow and all error situations.

## Current Situation
Currently it is only possible to compare the output of the test with an expected output. Not having ability to assert specifically on the result of the behavior you are testing for has a number of negative effects.

### Unable to test functional behavior
When a test can only assert on the output being a certain value, it is impossible to test behavior that does not produce any output at all or the output doesn't change as a result of the behavior to be tested.

An example of such scenario would be an adapter that takes a temperature as input. If the temperature is subzero, a freezingEvent will be sent to an arbitrary endpoint. Otherwise a sunnyEvent will be sent. In this scenario both events result in nothing more than a HTTP 200. 

### Developer Feedback
The most obvious one is the lack of feedback to the developer. When every test effectively becomes a regression test, the developer only knows that SOMETHING changed to cause the output to change, but not which behavior changed to cause the regression. As a comparison; This level of feedback is about as useful as a bugreport named: "Framework doesn't work anymore".

### Brittle & Expensive Testcases
The other extreme of not being able to assert specifically on a behavior, is that testcases becomes brittle, and as a result become expensive to maintain. When a single behavior has changed in an adapter, it should ideally result in only 1 testcase needing to changed.

An example would be an adapter that results in some datastructure with information about a user. Certain elements in the user datastructure can contain different values depending on conditions/behavior. A few testcases specifically test the behavior of the "name" element. This is done by comparing the output of the whole user with the expected ouput. When a new element is introduced or one of the other behaviors change, every single test testing the user datastructure will break, while this new element is completely irrelevant for the majority of the testcases.

>Note: Currently it is already possible to "filter" the output before it is compare or ignore certain parts of the output. However, the current options are tedious to use and are mostly focussed on XML. 

