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


```bash
autotrain \
    --model_name_or_path facebook/bart-large-cnn \
    --train_file data/train.jsonl \
    --validation_file data/eval.jsonl \
    --output_dir output \
    --do_train \
    --do_eval \
    --per_device_train_batch_size 2 \
    --per_device_eval_batch_size 2 \
    --overwrite_output_dir \
    --predict_with_generate \
    --num_train_epochs 1 \
    --max_source_length 1024 \
    --max_target_length 128 \
    --save_steps 1000 \
    --logging_steps 1000 \
    --eval_steps 1000 \
    --warmup_steps 1000 \
    --learning_rate 3e-5 \
    --fp16 \
    --push_to_hub
```

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

