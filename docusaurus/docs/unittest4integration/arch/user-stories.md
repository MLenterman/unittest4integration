---
sidebar_position: 25
---

# User Stories

## Quickly create testcases with an existing programming language with existing testing frameworks
Since loaded configurations are converted to Java using reflection, the developer could use a Java unit testing framework to create test cases. This allows the developer to leverage existing Java testing libraries and frameworks, such as JUnit (used in the Frank!Framework), to create and run unit tests. This approach provides a familiar environment for developers who are already accustomed to Java and its testing ecosystem.

The power of using a programming language for these tests is the flexibility of the language itself. Test cases can leverage loops and conditionals to test a large number of scenarios with minimal code duplication. This can lead to more maintainable and readable test cases, as well as easier debugging and error handling.

Since the Frank!Framework currently provides the Larva test framework, used to create integration tests, it is possible to test an adapter from start to finish using Larva. However, this approach requires the entire framework to be started. Larva scenarios also require the use of stubbing to simulate external dependencies, which can complicate the test setup and execution. Larva also has limited assertion capabilities, only by comparing the expected result with the actual result, which can make it difficult to pinpoint the exact cause of a failure, or in cases where a pipe has no output, it is impossible to assert the expected result to verify the correctness of the code.

## Create Franks by using a programming language
The Frank!Framework converts xml configurations to Java using reflection. Instead of writing xml configurations, the developer could write the same configurations in Java. This would allow the developer to leverage the power of a programming language, such as Java, to create more complex and dynamic configurations, while still adhering to the rules and structure of the Frank!Framework. This would enable the developer to create more flexible and reusable configurations, as well as take advantage of existing Java libraries and frameworks.
An adapter created using Java, could easily be integrated into a project, next to other adapters created using xml configurations. This would allow the developer to create a mix of xml and Java configurations, depending on the specific requirements of the project, allowing for a more flexible and adaptable approach to configuration management.
This has a potential downside, when custom code is used in an adapter, instead of the existing pipes provided by the Frank!Framework, since performance and maintainability of the adapter could be affected, as updates to the framework may not be compatible with the custom code, or improve the performance of code created without using the existing pipes.

## Reusable function blocks
Reusable function blocks can be created to encapsulate specific functionality. By combining multiple pipes into a single function block, the developer can create a more modular and reusable configuration. This approach allows for better separation of concerns, as each function block can focus on a specific piece of functionality, making it easier to maintain and test.
Caution should be taken when creating function blocks, as they can become too complex or tightly coupled. Complex function blocks can be difficult to reuse or configure, as each pipe within the block may have its own configuration requirements. Function blocks could lose out on the benefits of the flexibility of individual pipes.
Since pipes already encapsulate specific functionality, it is important to ensure that function blocks do not duplicate the functionality of existing pipes. Instead, they should focus on combining and orchestrating existing pipes to create a more cohesive and reusable piece of functionality.
For instance, a function block could handle api requests, combining multiple pipes to handle the request, process the response, and return the result. This would allow the developer to create a reusable function block that can be used across multiple adapters, reducing code duplication and improving maintainability, while allowing the developer to define how the request processed and how the response is created.

## Frank!Library to share reusable functionality between integrations.
Building on the concept of reusable function blocks, a Frank!Library could be created to share reusable functionality between different integrations. This library would contain a collection of function blocks, pipes, and other reusable components that can be easily integrated into different projects. Generic function blocks could be created to handle common tasks, such as data transformation, validation, or error handling, allowing developers to leverage existing functionality without having to reinvent the wheel.

## Virtual configurations???


## Start/Run larva scenario's without starting the framework
Currently, Larva scenarios require the entire Frank!Framework to be started, which can be time-consuming and resource-intensive. By allowing developers to run Larva scenarios without starting the framework, they could quickly test and validate their configurations without the overhead of the entire framework. This would enable faster feedback loops and more efficient development processes, as developers could focus on testing specific configurations or adapters without the need for a full framework startup. This could improve both the local development cycle and the CI/CD pipeline, as tests could be run faster and more frequently, without the need for extensive setup or teardown processes.
Such processes could be integrated into code editors and IDEs, allowing developers to run tests directly from their development environment, without having to switch contexts or wait for the framework to start. This would streamline the development process and make it easier for developers to validate their changes quickly and efficiently.

## Larva should have simple asserts for test steps.
Building on the idea of quick feedback loops, Larva scenarios should provide more functionality to assert the correctness of a configuration. Currently, Larva scenarios only allow for basic assertions by comparing the expected result with the actual result. This can be limiting, as it does not provide enough flexibility to verify the correctness of a configuration in detail.
Larva also does not provide to test only a subset of an adapter, such as a single pipe, or future function blocks, which can make it difficult to isolate and test specific pieces of functionality. This can lead to more complex and less maintainable test cases, as developers may need to create multiple scenarios to cover different aspects of a configuration. This could also effect maintainability, as changes to a single pipe or function block may require updates to multiple scenarios, whose asserts may not be relevant to the changes made.