# Evaluating Models

Reference: https://github.com/krai/mistral7b_cnndailymail/tree/main/scripts/evaluation

## Evaluation Metrics

### ROUGE

ROUGE is a tool for evaluating automatic summarization and machine translation software in natural language processing. The name ROUGE, in turn, is an acronym for Recall-Oriented Understudy for Gisting Evaluation.

### BLEU

BLEU (bilingual evaluation understudy) is an algorithm for evaluating the quality of text which has been machine-translated from one natural language to another. Quality is considered to be the correspondence between a machine's output and that of a human: "the closer a machine translation is to a professional human translation, the better it is" – this is the central idea behind BLEU.

### METEOR

METEOR (Metric for Evaluation of Translation with Explicit ORdering) is a metric for the evaluation of machine translation output. It is based on the harmonic mean of unigram precision and recall, with recall weighted higher than precision. It also has features that penalize output that is "too good" (i.e. over-generated).

### Case study

=== "CNN Dailymail"


    | MODEL_NAME                               | Command                                                                                                    |
    |------------------------------------------|------------------------------------------------------------------------------------------------------------|
    | 01-ai/Yi-6B                              | <pre><code>python summarization_evaluator.py --model_name 01-ai/Yi-6B --data_file data/cnn_eval.json --num 100 --batch_size 5</code> </pre>              |
    | xihajun/cnn_10k_0.001_30_16_32_0.1_0.01 | <pre><code>python summarization_evaluator.py --model_name xihajun/cnn_10k_0.001_30_16_32_0.1_0.01 --data_file data/cnn_eval.json --num 100 --batch_size 5</code></pre>  |
    | EleutherAI/gpt-j-6b                      | <pre><code>python summarization_evaluator.py --model_name EleutherAI/gpt-j-6b --data_file data/cnn_eval.json --num 100 --batch_size 5</code></pre>      |


=== "axs"

    TBC


