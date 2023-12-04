# Data Preparation

In order to finetune the open sourced LLM, we need to follow the format they pre-trained.

### Data Format


=== "base model"

    <pre><code><s> Below is xxx:\n\n### Instruction:\n{instruction}\n###Response:\n{response}</s>
    </code></pre>
    
=== "instructed model"

    <pre><code><s> [SYS] Below is xxx: [\SYS]\n\n [INST]{instruction}[\INST]{response}</s>
    </code></pre>


### JSONL

We will introduce `autotrain` later, the best format we used so far is based on `autotrain` with `jsonl` format.


|            |      text       |  other     |   
|------------|-----------------|------------|
|example1    | `<s>{info}</s>` |            |         
|example2    | `<s>{info}</s>` |            |         


??? Show me what you get!

    All I can show is this:
    ```
    {"text": "<s>{info}</s>"}
    {"text": "<s>{info}</s>"}
    ```
    Please prepare something like this, and do not forget about `</s>` to allow the model stop itself.



