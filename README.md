# COPA-SSE
Repository for [**COPA-SSE: Semi-Structured Explanations for Commonsense Reasoning**](https://arxiv.org/abs/2201.06777).


![Crowdsourcing protocol](crowdsourcing_protocol.png)


COPA-SSE contains crowdsourced explanations for the [Balanced COPA](https://balanced-copa.github.io/) dataset, a variant of the [Choice of Plausible Alternatives (COPA)](https://people.ict.usc.edu/~gordon/copa.html) benchmark.
The explanations are formatted as a set of triple-like common sense statements with ConceptNet relations but freely written concepts.


## Data format

`dev-explained.jsonl` and `test-explained.jsonl` each contain Balanced COPA samples with added explanations in `.jsonl` format. The question ids match the original questions of the development and test set, respectively.

Each entry contains:
- the original question (matching format and ids)
- `human-explanations`: a list of explanations each containing:
    - `expl-id`: the explanation id
    - `text`: the explanation in plain text (full sentences)
    - `worker-id`: anonymized worker id (the author of the explanation)   
    - `worker-avg`: the average score the author got for their explanations
    - `all-ratings`: all collected ratings for the explanation
    - `filtered-ratings`: ratings excluding those that failed the control
    - `triples`: the triple-form explanation (a list of ConceptNet-like triples)

### Example entry:
```
id: 1, 
asks-for: cause, 
most-plausible-alternative: 1,
p: "My body cast a shadow over the grass.", 
a1: "The sun was rising.", 
a2: "The grass was cut.", 
human-explanations: [
    {expl-id: f4d9b407-681b-4340-9be1-ac044f1c2230, 
     text: "Sunrise causes casted shadows.", 
     worker-id: 3a71407b-9431-49f9-b3ca-1641f7c05f3b, 
     worker-avg: 3.5832864694635025, 
     all-ratings: [1, 3, 3, 4, 3], 
     filtered-ratings: [3, 3, 4, 3], 
     filtered-avg-rating: 3.25, 
     triples: [["sunrise", "Causes", "casted shadows"]]
     }, ...]
```

## Aggregated versions

`graphs.pkl` contains aggregated versions of the triples for each question in a dictionary format with COPA question ids as the key.

Each entry contains a list of edges, each being a tuple of (u, v, {'rel': relation, 'weight': weight}). Similar nodes were connected or merged with `relatedto`, depending on the cosine similarity between their `SentenceTransformer` embeddings. The weight is the average score of the explanation the edge originated from (summed if multiple), or 1.0 if the edge was automatically generated.
* Note: not all graphs are (weakly) connected. 


### Example entry:
```
1: [('sunrise', 'casted_shadows', {'rel': 'causes', 'weight': 3.25}),
  ('sunrise', 'sun', {'rel': 'relatedto', 'weight': 1.0}),
  ('casted_shadows', 'the_shadow', {'rel': 'relatedto', 'weight': 1.0}),
  ('sun_rising', 'bringing_light', {'rel': 'hasproperty', 'weight': 4.25}),
  ('sun_rising', 'a_sun_raising', {'rel': 'relatedto', 'weight': 1.0}),
 ...
]
```

## Citation
Thank you for your interest in our dataset! If you use it in your research, please cite:
```
@InProceedings{copa-sse:LREC2022,
  author    = {Brassard, Ana  and  Heinzerling, Benjamin  and  Kavumba, Pride  and  Inui, Kentaro},
  title     = {COPA-SSE: Semi-structured Explanations for Commonsense Reasoning},
  booktitle      = {Proceedings of the Language Resources and Evaluation Conference},
  month          = {June},
  year           = {2022},
  address        = {Marseille, France},
  publisher      = {European Language Resources Association},
  pages     = {3994--4000},
  url       = {https://aclanthology.org/2022.lrec-1.425}
}

```
