---
sidebar_position: 20
---

An adapter should preferably be small and responsible for a single piece of functionality. In the future another level of abstraction, smaller than an adapter, may be introduced to encapsulate a "function block". Unit test cases would then primarily target these function blocks, while adapter test cases would more appropriately fall under integration testing.

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
