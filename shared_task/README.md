
# Baseline Model and Evaluations for W-NUT 2020 Shared Task

Check the shared task official website at: [http://noisy-text.github.io/2020/extract_covid19_event-shared_task.html](http://noisy-text.github.io/2020/extract_covid19_event-shared_task.html).

### Run baseline

Here are the steps for running our logistic regression baseline on a single task: to predict if a given chunk could answer ``who is tested positive" question in tested positive event category.

0. Please follow [instructions](http://noisy-text.github.io/2020/extract_covid19_event-shared_task.html#format) to get tweet contents and use proper NLP tools to tokenize all tweets.

1. Prepare the training instances in the following format: each line contains a tuple of (`orginal_tweet_content`, `current_candidate_chunk`, `tokenized_tweet_with_candidate_chunk_masked`, `golden_chunk`, `training_label`).

For example, in the first training instance, we replace `Tom Hanks` with `<Q_TARGET>` in the tokenized tweets. As `Tom Hanks` appears in the `golden_chunk`, training label for this instance is `1`. In the second training instance, as `both` is not annotated as a correct chunk, training lable is `0`.

```
[('Tom Hanks and his wife have both tested positive for the Coronavirus.',
 'Tom Hanks',
 '<Q_TARGET> and his wife have both tested positive for the Coronavirus .',
 ['Tom Hanks', 'his wife'],
 1)
('Tom Hanks and his wife have both tested positive for the Coronavirus.',
 'both',
 'Tom Hanks and his wife have <Q_TARGET> tested positive for the Coronavirus .',
 ['Tom Hanks', 'his wife'],
 0)
]
```

2. Store data organized in above format to a .pkl file.

```angular2

## organize your data into above format
data_reorganized = []
for each_line in data_original:
    data_reorganized.append((orginal_tweet_content,
                             current_candidate_chunk,
                             tokenized_tweet_with_candidate_chunk_masked,
                             golden_chunk, training_label))

## store reorganized data into a dictionary, using the subtask name as key
data_dict = {}
# as currently we consider "name" subtask, we use "name" as key
data_dict['name'] = data_reorganized

## store file in .pkl format, save_in_pickle() function in shared_task.utils
save_in_pickle(data_dict, YOUR_PATH_TO_STORE_DATA.pkl)
```

3. Run baseline. `-t` is a just a flag for printing the current task name in the log file, which is not used in the actual code. `-st` is the name for the subtask (it is used as a key to extract corresponding training instances in `data_dict`). `-o` is the path to store evaluation results.

```
python shared_task/LR_baseline.py -d YOUR_PATH_TO_STORE_DATA.pkl 
                                  -t tested_positive_name -st name
                                  -o results/lr_baseline/tested_positive_name
```

You could run our baseline model on other subtasks by adding training instances into `data_dict`. For example, you could use `data_dict['age']` to store training instances for `age` slot. Then you could run above code by replacing `name` with `age`.

### Evaluation

Evaluations are performed by using `get_TP_FP_FN(data, prediction_scores)` function in `shared_task/utils.py`. `data` should be in format outlined in Step 1 above. `prediction_scores` are the scores generated by the system.

By calling `get_TP_FP_FN()` you could easily evaluate your model performance on a single subtask. We will also be releasing an evaluation script for ranking your systems on all subtasks.

