---
sidebar_position: 20
---

# Mocking External Resources
Adapter should preferably be small and be responsible for a single piece of functionality. In the future there will hopefully be another level of abstraction
possible that is smaller than an adapter and would encapsulate a "function block". Unit testcases would then primarily be applied on those, while testcase for adapters
would likely fall under integration tests.

## Design Goals
- Should be easy to use and only require generic and basic knowledge about programming languages.
- Easy to replace a reference to a local resource such as files, stylesheets, etc. with either another file or inline content.
- Mocks should default to the original pipes/sender behavior and only change the necessary things.
- Testcases should be able to run in parallel in isolation from each other.

### Mocking of filesystem resources
Adapter under test:
```xml title="RestSender_ArchiveDoc Adapter"
<Adapter
    name="RestSender_ArchiveDoc">
    <Receiver>
        <JavaListener
            name="RestSender_ArchiveDoc"
            serviceName="ibis4test-RestSender_ArchiveDoc" />
    </Receiver>
    <Pipeline>
        <Exits>
            <Exit name="EXIT" state="SUCCESS" />
        </Exits>

        <FixedResultPipe
            name="readFile"
            filename="RestListener/pdf/sample.pdf"
            storeResultInSessionKey="fileContent">
            <Forward name="success" path="setFileName" />
        </FixedResultPipe>

        <PutInSessionPipe
            name="setFileName"
            sessionKey="fileContentName"
            value="sample.pdf">
            <Forward name="success" path="callRest" />
        </PutInSessionPipe>

        <SenderPipe
            name="callRest">
            <HttpSender
                methodType="POST"
                url="${web.protocol}://${web.host}:${web.port}${web.contextpath}/rest/doc"
                postType="FORMDATA"
                sharedResourceRef="SharedHttpSession">
                <Param name="key01" value="every cloud has a silver lining" />
                <Param name="key02" sessionKey="originalMessage" />
                <Param name="file" sessionKey="fileContent" />
            </HttpSender>
            <Forward name="success" path="text2Xml" />
        </SenderPipe>

        <Text2XmlPipe
            name="text2Xml"
            xmlTag="id" useCdataSection="false">
            <Forward name="success" path="writeFile" />
        </Text2XmlPipe>

        <LocalFileSystemPipe
            name="writeFile"
            action="write" filename="${testdata.dir}/rest/id.xml">
            <Forward name="success" path="text2Xml2" />
        </LocalFileSystemPipe>

        <Text2XmlPipe
            name="text2Xml2"
            xmlTag="filename" useCdataSection="false">
            <Forward name="success" path="EXIT" />
        </Text2XmlPipe>
    </Pipeline>
</Adapter>
```

```java
public class SetResultaatAndStatusTest extends TestBase {
    public Adapter adapter;

    public void Setup(){
        adapter = configurations["ConfigUnderTest"].adapters["RestSender_ArchiveDoc"];
    }

    @Test
    public void testSetResultaatAndStatus() {
        // Arrange
        Message<String> message = new Message("value");
        
        // Act
        result = adapter.run(message);

        // Assert
    }

    public void Teardown() {
    }
}
```