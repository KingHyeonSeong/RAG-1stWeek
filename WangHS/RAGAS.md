# RAGAS
Ragas is a library that provides tools to supercharge the evaluation of Large Language Model (LLM) applications. It is designed to help you evaluate your LLM applications with ease and confidence.

---

## Components of a Prompt Object
1. **Instruction** - A fundamental element of any prompt, the instruction is a natural language directive that clearly describes the task the Language Model (LLM) should perform. This is specified using the instruction variable within the prompt object.
2. **Few-Shot Examples** - These examples are specified using the examples variable in the prompt object. Each example consists of an input and its corresponding output, which the LLM uses to learn the task.
3. **Input Model** - Every prompt expects an input to produce an output. This is a Pydantic model that outlines the structure of the input, enabling validation and parsing of the data provided to the prompt.
4. **Output Model** - Like the input model, the output model is a Pydantic model that defines the structure of the output, facilitating validation and parsing of the data produced by the LLM.

---

## SingleTurnSample

SingleTurnSample represents a single-turn interaction between a user, LLM, and expected results for evaluation. It is suitable for evaluations that involve a single question and answer pair, possibly with additional context or reference information.

## MultiTurnSample

MultiTurnSample represents a multi-turn interaction between Human, AI and optionally a Tool and expected results for evaluation. It is suitable for representing conversational agents in more complex interactions for evaluation. In MultiTurnSample, the user_input attribute represents a sequence of messages that collectively form a multi-turn conversation between a human user and an AI system. These messages are instances of the classes HumanMessage, AIMessage, and ToolMessage

---

# Metrics

A metric is a quantitative measure used to evaluate the performance of a AI application. Metrics help in assessing how well the application and individual components that makes up application is performing relative to the given test data. They provide a numerical basis for comparison, optimization, and decision-making throughout the application development and deployment process. Metrics are crucial for:

---

1. **Component Selection**: Metrics can be used to compare different components of the AI application like LLM, Retriever, Agent configuration, etc with your own data and select the best one from different options.
2. **Error Diagnosis and Debugging**: Metrics help identify which part of the application is causing errors or suboptimal performance, making it easier to debug and refine.
3. **Continuous Monitoring and Maintenance**: Metrics enable the tracking of an AI application’s performance over time, helping to detect and respond to issues such as data drift, model degradation, or changing user requirements.

---

## Context Recall

Context Recall measures how many of the relevant documents (or pieces of information) were successfully retrieved. It focuses on not missing important results. Higher recall means fewer relevant documents were left out. In short, recall is about not missing anything important. Since it is about not missing anything, calculating context recall always requires a reference to compare against.

### LLM Based Context Recall

LLMContextRecall is computed using **user_input**, **reference** and the **retrieved_contexts**, and the values range between 0 and 1, with higher values indicating better performance. This metric uses reference as a proxy to reference_contexts which also makes it easier to use as annotating reference contexts can be very time consuming. To estimate context recall from the reference, the reference is broken down into claims each claim in the reference answer is analyzed to determine whether it can be attributed to the retrieved context or not. In an ideal scenario, all claims in the reference answer should be attributable to the retrieved context.

### Non LLM Based Context Recall

NonLLMContextRecall metric is computed using **retrieved_contexts** and **reference_contexts**, and the values range between 0 and 1, with higher values indicating better performance. This metrics uses non llm string comparison metrics to identify if a retrieved context is relevant or not. You can use any non LLM based metrics as distance measure to identify if a retrieved context is relevant or not.

---

## Factual Correctness

FactualCorrectness is a metric that compares and evaluates the factual accuracy of the generated **response** with the **reference**. This metric is used to determine the extent to which the generated response aligns with the reference. The factual correctness score ranges from 0 to 1, with higher values indicating better performance. To measure the alignment between the response and the reference, the metric uses the LLM for first break down the response and reference into claims and then uses natural language inference to determine the factual overlap between the response and the reference. Factual overlap is quantified using precision, recall, and F1 score, which can be controlled using the mode parameter.

- **True Positive(TP)** = Number of claims in response that are present in reference
- **False Positive(FP)** = Number of claims in response that are not present in reference
- **False Negative(FN)** = Number of claims in reference that are not present in response

```
Precision = TP / (TP + FP)
Recall = TP / (TP + FN)
F1 Score = 2 * Precision * Recall / (Precision + Recall)
```

### Controlling the Number of Claims

Each sentence in the response and reference can be broken down into one or more claims. The number of claims that are generated from a single sentence is determined by the level of atomicity and coverage required for your application.

1. **Atomicity** - refers to how much a sentence is broken down into its smallest, meaningful components. It can be adjusted based on whether you need highly detailed claims or a more consolidated view
2. **Coverage** - refers to how comprehensively the claims represent the information in the original sentence. It can be adjusted to either include all details or to generalize the content

### Practical Application

- Use **High Atomicity and High Coverage** when you need a detailed and comprehensive breakdown for in-depth analysis or information extraction.
- Use **Low Atomicity and Low Coverage** when only the key information is necessary, such as for summarization.