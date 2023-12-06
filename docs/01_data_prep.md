# Data Preparation

In order to finetune the open sourced LLM, we need to follow the format they pre-trained.

### Data Format


=== "base model"

    ```
    <s> Below is xxx:\n\n### Instruction:\n{instruction}\n###Response:\n{response}</s>
    ```
    
=== "instructed model"

    ```
    <s> [SYS] Below is xxx: [\SYS]\n\n [INST]{instruction}[\INST]{response}</s>
    ```


### JSONL

We will introduce `autotrain` later, the best format we used so far is based on `autotrain` with `jsonl` format.


|        |      text       |  prompt    |   
|--------|-----------------|------------|
|example1| `<s>{info}</s>` | <prompt1>  |         
|example2| `<s>{info}</s>` | <prompt2>  |         


??? "Show me what you get!"

    All I can show is this:
    ```
    {"text": "<s>{info}</s>"}
    {"text": "<s>{info}</s>"}
    ```
    Please prepare something like this, and do not forget about `</s>` to allow the model stop itself.


### Case study

We will use `axs` and `CNN dailymail` as examples to show how we collect data to finetune the model here.

=== "axs"
    
    AXS is our internel toolbox, but we don't have good documentation for it yet. Thanks to LLM, we are exploring the possibility of using LLM as a smart assistant to help us to speed up the process of learning how to use AXS.


=== "CNN dailymail"

    CNN dailymail is a dataset for summarization. We will use it as an example to show how we collect data to finetune the model here.

#### Data Preparation 

Follow the steps below to prepare your data:

##### Step 1: Clone the Repository and install dependencies

=== "axs"
    
    Clone the repository
    ```bash
    git clone https://github.com/krai/axs.git
    ```

=== "CNN dailymail"

    Training dataset
    ```bash
    git lfs install
    git clone https://huggingface.co/xihajun/cnn_dailymail
    cd cnn_dailymail
    git lfs pull
    ```

    Evaluation dataset
    ```bash
    git clone https://github.com/mlcommons/inference.git --depth 1
    cd inference/language/gpt-j/
    pip install -r requirements.txt
    pip install rouge-score
    ```


??? example "Shell"
    <iframe width=800px, height=500 frameBorder=0 src="https://tmate.io/t/tbsJMJN3Fau3sjzppVBX3KqMt"></iframe>

##### Step 2: Download and prepare the dataset

=== "axs"
    
    - [ ] TODO: add the steps to prepare the dataset
        - [ ] commit messages
        - [ ] hand written notes
        - [ ] logs
        - [ ] automation scripts


=== "CNN dailymail"

    Prepare the training dataset with the `<s></s>` format (10K samples)

    ```bash
    cd cnn_dailymail
    python3 filter_faster.py --input train_output.jsonl --output train_output_formatted.jsonl
    mkdir 10k
    head -n10000 train_output_formatted.jsonl > 10k/10k.jsonl
    ```
    
    Download Evaluation dataset to `data` folder

    ```bash
    cd inference/language/gpt-j/
    python3 download_cnndm.py
    ```

