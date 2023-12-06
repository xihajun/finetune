# LLM Finetune

Once you prepared your data, you can start to finetune the model. Let's assume that you have prepared your data in the following format:

```json
{"text": "<s>{info}</s>"}
{"text": "<s>{info}</s>"}
```
and you have saved it as `train.jsonl` and `eval.jsonl` in the `data` folder.

## Prepare the environment

Huggingfaces' `autotrain` makes our life easier and have a great support from the github community. We will use it to finetune our model.

=== "Install autotrain"

    ```bash
    pip install autotrain-advanced
    ```

=== "Use Docker to run autotrain"

    ```bash
    docker pull xihajun/autotrain-advanced
    docker run -it --rm -v $(pwd):/workspace xihajun/autotrain-advanced
    ```



??? example "Shell"
    <iframe width=800px, height=500 frameBorder=0 src="https://tmate.io/t/tbsJMJN3Fau3sjzppVBX3KqMt"></iframe>


## Fine-tune the model


=== "usage"

    ??? "Optional"
        - If you want to push your model to the hub, you need to create a token from [here](https://huggingface.co/settings/token).
        - wandb is a great tool to track your training process. You can create an account from [here](https://wandb.ai/authorize).
        
    ```bash
    autotrain llm \
        --train \
        --model mistralai/Mistral-7B-v0.1 \
        --project-name finetuned_model \
        --data-path training_data/ \
        --text-column text \
        --lr 0.002 \
        --batch-size 16 \
        --epochs 30 \
        --block-size 1024 \
        --model_max_length 2048 \
        --warmup-ratio 0.1 \
        --lora-r 16 \
        --lora-alpha 32 \
        --lora-dropout 0.1 \
        --weight-decay 0.01 \
        --gradient-accumulation 4 \
        --fp16 \
        --use-peft \
        --use-int4 \
        --push-to-hub \
        --token <your token> \
        --repo-id xihajun/cnn_10k_0002_30_16_32_0.1_0.01 \
        --target-modules q_proj,v_proj \
        --log wandb \
        --save_total_limit 10 \
        --save_strategy epoch
        --merge-adapter
    ```

=== "more details"

    <div class="annotate" markdown>


    > autotrain llm 
    
    
    
    --train --model mistralai/Mistral-7B-v0.1 --project-name mix_2000_autotrain_llm --data-path training_data/ --text-column text --lr 0.002 --batch-size 16 --epochs 30 --block-size 1024 --model_max_length 2048 --warmup-ratio 0.1 --lora-r 16 --lora-alpha 32 --lora-dropout 0.1 --weight-decay 0.01 --gradient-accumulation 4 --fp16 --use-peft 
    > --use-int4 

    > --push-to-hub 

    > --token (1) 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 
    
    --repo-id xihajun/cnn_10k_0002_30_16_32_0.1_0.01 --target-modules q_proj,v_proj --log wandb --save_total_limit 10 --save_strategy epoch

    </div>

    1.  :man_raising_hand: Your token



??? example "Shell"
    <iframe width=800px, height=500 frameBorder=0 src="https://tmate.io/t/tbsJMJN3Fau3sjzppVBX3KqMt"></iframe>



----

## Self Assessment

??? Question "What is a Data Management Plan?"

    **Important**: A data management plan (DMP) is now required aspect of publicly funded research.

    DMPs are short, formal, documents outlining what types of data will be used, and what will be done with the data both during and after a research project concludes.

??? Question "True or False: When science project funding ends, the data should end with it"

    !!! Success "False"

        Data live on after a project ends.

        Ensuring that data have a full lifecycle where they can be (re)hosted and made available after a project ends is critical to open science and reproducible research

    !!! Danger "Maybe"

        Sometimes destroying data is part of the life cycle of data - this may be required if data are sensitive and could be used unethically in the future, beyond the control of the original investigator team. 

??? Question "True or False: FAIR and CARE data principles are the same"

    !!! Success "False"

        The CARE principles were created in order to help guide and answer when and how applying FAIR data principles to soverign indigenous-controlled data should be done and when it should not. 

