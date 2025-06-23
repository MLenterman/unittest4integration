---
sidebar_position: 20
---

# Basic Execution Of Adapter
Adapter should preferably be small and be responsible for a single piece of functionality. In the future there will hopefully be another level of abstraction
possible that is smaller than an adapter and would encapsulate a "function block". Unit testcases would then primarily be applied on those, while testcase for adapters
would likely fall under integration tests.

### Text as input and assert that output is a specific text value
Adapter under test:
```xml title="ConditionalReplace Adapter"
<Adapter
    name="ConditionalReplace">
    <Receiver
        name="ConditionalReplace">
        <JavaListener
            name="ConditionalReplace"
            serviceName="ConditionalReplace"/>
    </Receiver>

    <Pipeline>
        <Exits>
            <Exit name="EXIT" state="SUCCESS"/>
            <Exit name="ERROR" state="ERROR"/>
        </Exits>
        
        <XmlIfPipe
            name="ErrorOnEmptyInput"
            getInputFromFixedValue="&lt;dummy/&gt;"
            // highlight-next-line
            xpathExpression="string-length($Input) = 0">
            <Param name="Input" sessionKey="originalMessage"/>
            <Forward name="then" path="ERROR" />
            <Forward name="else" path="Continue" />
        </XmlIfPipe>

        <XmlIfPipe
            name="ContinueAlphabetWhenInputEqualsAbc"
            getInputFromFixedValue="&lt;dummy/&gt;"
            // highlight-next-line
            xpathExpression="$Input = 'abc'">
            <Param name="Input" sessionKey="originalMessage" />
            <Forward name="then" path="ConcatDefgToInput" />
            <Forward name="else" path="EXIT" />
        </XmlIfPipe>
        
        <XsltPipe
            name="ConcatDefgToInput"
            getInputFromFixedValue="&lt;dummy/&gt;"
            // highlight-next-line
            xpathExpression="concat($Input, 'dfeg')">
            <Param name="Input" sessionKey="originalMessage" />
            <Param name="employeeId" sessionKey="employeeId" />
        </XsltPipe>
    </Pipeline>
</Adapter>
```

```java title="ConditionalReplace Adapter Unittests"
public class ConditionalReplaceTest extends TestBase {
    public Adapter adapter;

    @Override
    public void Setup() {
        adapter = configurations["ConfigUnderTest"].adapters["ConditionalReplace"];
    }

    @Test
    public void EmptyMessageShouldResultInErrorState() {
        // Arrange
        Message message = Message.Empty;

        // Act
        result = adapter.run(message);

        // Assert
        assertEquals(AdapterState.Error, result.state());
    }

    @Test
    public void InputValueAbcShouldResultInAbcdegf() {
        // Arrange
        Message message = new MessageBuilder()
            .withValue("abc")
            .build();

        // Act
        result = adapter.run(message);

        // Assert
        assertEquals("abcdefg", result.asString());
    }

    @Test
    public void InputValueOtherThanEmptyOrAbcShouldResultInInputValue() {
        // Arrange
        Message message = new MessageBuilder()
            .withValue("something other than abc")
            .build();

        // Act
        result = adapter.run(message);

        // Assert
        assertEquals(message.asString(), result.asString());
    }
}
```
