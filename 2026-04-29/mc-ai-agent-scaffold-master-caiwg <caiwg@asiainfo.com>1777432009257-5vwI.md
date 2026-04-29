# Markcwg-GLM-AI 代码评审.
### 😀代码评分：70
#### 😀代码逻辑与目的：
代码逻辑主要是对AI代理的配置进行测试，包括对图片内容的描述。新增了一个测试方法来测试多模态输入，即将图片作为输入进行内容描述。

#### 🤔问题点：
1. 新增的测试方法 `test_multi_media` 中，直接从类路径读取图片文件，但没有处理可能出现的 `IOException`。
2. 代码中使用了 `blockingGet` 和 `blockingForEach`，这可能会造成线程阻塞，特别是在异步操作中。
3. 代码中使用了 `@Value` 注解来注入资源，但未对资源进行有效性检查。
4. 代码中使用了 `MediaType.IMAGE_PNG_VALUE`，但没有检查其是否正确。

#### 🎯修改建议：
1. 在读取图片文件时，应该捕获并处理 `IOException`。
2. 对于 `blockingGet` 和 `blockingForEach`，可以考虑使用 `subscribe` 和 `blockingLast` 来替代，以避免线程阻塞。
3. 在注入资源时，应该检查资源是否为 `null`。
4. 检查 `MediaType.IMAGE_PNG_VALUE` 的有效性。

#### 💻修改后的代码：
```java
@Test
public void test_multi_media() throws IOException {
    AiAgentRegisterVO aiAgentRegisterVO = applicationContext.getBean("100003", AiAgentRegisterVO.class);

    String appName = aiAgentRegisterVO.getAppName();
    InMemoryRunner runner = aiAgentRegisterVO.getRunner();

    Session session = runner.sessionService().createSession(appName, "img_01").subscribeOn(Schedulers.io()).blockingGet();
    Flowable<Event> events = runner.runAsync(session.userId(), session.id(),
        Content.fromParts(
            Part.fromText("给我描述一下这张图片的内容"),
            Part.fromBytes(resource.getContentAsByteArray(), MediaType.IMAGE_PNG_VALUE)
        ));

    List<String> res = new ArrayList<>();
    events.subscribeOn(Schedulers.io()).blockingLast().blockingForEach(event -> res.add(event.stringifyContent()));

    log.info("多模态测试结果:{}", JSON.toJSONString(res));
}
```

#### 🌟代码中的优点：
1. 使用了 `@Resource` 和 `@Value` 注解来注入依赖，提高了代码的可读性和可维护性。
2. 使用了 `SpringBootTest` 注解来启动测试环境，方便进行集成测试。