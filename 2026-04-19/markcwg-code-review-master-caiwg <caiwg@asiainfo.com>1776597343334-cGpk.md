# Markcwg-GLM-AI 代码评审.
### 😀代码评分：80
#### 😀代码逻辑与目的：
该代码片段是用于生成一个包含代码审查指令的提示信息，以便提供给AI进行代码审查。这个提示信息被添加到ChatCompletionRequestDTO的Prompt中，以便AI能够理解代码审查的要求和格式。

#### ✅代码优点：
- 代码结构清晰，易于理解。
- 使用了静态代码块来初始化提示信息，避免了对类实例化时的干扰。

#### 🤔问题点：
- 提示信息过长，可能会导致性能问题，特别是在高延迟的网络环境下。
- 提示信息中包含了大量的注释和格式说明，这可能会分散AI对实际代码审查内容的注意力。

#### 🎯修改建议：
- 将提示信息拆分为多个部分，以减少单个请求的大小。
- 仅包含必要的格式说明和代码审查要求，避免过多的注释。

#### 💻修改后的代码：
```java
public class OpenAiCodeReviewService extends AbstractOpenAiCodeReviewService {
    private static final long serialVersionUID = -7988151926241837899L;

    {
        List<ChatCompletionRequestDTO.Prompt> prompts = new ArrayList<>();
        prompts.add(new ChatCompletionRequestDTO.Prompt("user", "你是一位资深编程专家，请根据以下git diff记录，对代码做出评审。"));
        prompts.add(new ChatCompletionRequestDTO.Prompt("user", "模板中的变量内容解释："));
        prompts.add(new ChatCompletionRequestDTO.Prompt("user", "变量1是给review打分，分数区间为0~100分。"));
        // 添加其他必要的格式说明
        prompts.add(new ChatCompletionRequestDTO.Prompt("user", diffCode));
        add(prompts);
    }
}
```