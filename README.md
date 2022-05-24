# Topic Modelling with Healthcare Spark NLP

A description of the project is published in:

- [Medium blog]().


## Table of Contents
* [General Information](#general-information)
* [Spark NLP Library](#spark-nlp)
* [The Data](#data)
* [The Pipeline](#pipe)
* [Results](#results)
* [Files](#files)
* [Specifications](#specs)
* [References](#refs)
<!-- * [License](#license) -->

## General Information

Topic models are unsupervised statistical models that aim to uncover hidden topics in which the corpus is embedded. In this post we show how we can use the Healthcare NLP version of John Snow Labs Spark NLP to identify topics for research and information gathering in an area adjacent to healthcare, in our case veterinary medicine. We can achieve this quite easily using the pretrained models and pipelines available in this library.

## Spark NLP Library

<img src="preliminary/imgs/sparknlp.png" width="600" height="200">

Spark NLP is a library created by the [John Snow Labs](https://www.johnsnowlabs.com/) company. It is written in Scala, built upon Apache Spark ML and dedicated to natural language processing tasks. It is scalable (it can be used for distributed applications) and it is developed to provide a unified solution for all NLP tasks. Spark NLP is consistently recognized as the most widely used NLP library in the enterprises.

Spark NLP has two versions: open source and enterprise. The [open source version](https://www.johnsnowlabs.com/spark-nlp/) is a full NLP library that provides production-grade, scalable, and trainable versions of the latest research in natural language processing. The [enterprise version](https://www.johnsnowlabs.com/spark-nlp-health/) is licensed and it is dedicated to solving real world problems in the healthcare domain. It is highly customizable and it provides solutions based on state-of-the-art algorithms.

## The Data

The dataset consists of about 1000 items returned by Google Scholar after querying *equine colic*. The raw dataset has 4 columns:
- *Title*,
- *TruncAbstr* - part of the abstract or description displayed by Google,
- *AugmTitle* - combined text of the title and the truncated abstract,
- *Info* - authors, publication details.

## The Pipeline

The pipeline consists of a combination of annotators from both the open source and the licensed versions of Spark NLP. The text corpus is supplied to the pipeline as a Spark dataframe. At each stage, each annotator receives an input of one or more columns, and transforms the dataframe by adding a new column.

<img src="preliminary/imgs/ner_pipeline_large.png" width="840" height="400">

The raw `text`, which in our case consists of augumented titles (combined title and truncated abstract for more information gathering), is fed into `DocumentAssembler()` that transforms it into `document` type. The `SentenceDetectorDL` breaks it into `sentences`, and the `Tokenizer` breaks it further down into relevant `tokens`. The `StopWordsCleaner` removes all the instances of the words 'colic' and 'equine'. We have now the `clean_tokens`. The `WordEmbeddingsModel` creates 200 dimensional vectors as `word_embeddings` for the pairs of `sentence` and `clean_tokens`. These three columns are then fed to the `MedicalNerModel` that identifies and labels the relevant `ner_med` chunks. The entities are relabeled by the `NerConverterInternal` which now returns the `ner_chunk`. Finally, the output of the `NerModel` goes through the `ChunkKeyPhraseExtractor` that returns one relevant key phrase for each augumented title.

## Results

#### The original labels

The pretrained NER model `ner_medmentions_coarse` has the ability to recognize 109 entities, 42 of each are identified in our corpus:

<img src="preliminary/imgs/distribution_original.png" width="800" height="600">

Sample key phrases for three different entities indicate that the model performs quite well in identifying the topics in most of the items:

<img src="preliminary/imgs/phrases_original.png" width="800" height="160">
&nbsp;

#### The new labels

The new labels, obtained by grouping together some of the original entities while leaving others unchanged.

<img src="preliminary/imgs/ner_new_labels_nonums.png" width="600" height="260">
&nbsp;

Several examples of key phrases for some of these entities are given below:

<img src="preliminary/imgs/phrases_new.png" width="800" height="160">

***

To this end we visualize wordclouds based on key phrases for selected entities for the two approaches.

- Original entities recognized by the Spark NLP pretrained model:

<img src="preliminary/imgs/wordclouds_original.png" width="800" height="200">

&nbsp;

- New entities obtained by combining the original ones:

<img src="preliminary/imgs/wordclouds_new.png" width="800" height="200">


## Files


    ├──LICENSE
    ├──README.md         <- The top-level README for developers.
    │
    ├──dataPreprocessing.ipynb     <- Jupyter notebook.
    ├──googleDataRetrieval.ipynb   <- Jupyter notebook.
    ├──graphsResults.ipynb         <- Jupyter notebook.
    ├──Healthcare_SparkNLP_Equine_Data_Exploration.ipynb  <- Google Colab notebook.
    ├──Healthcare_SparkNLP_Equine_Dataset.ipynb           <- Google Colab notebook.
    |          
    |── data              <- Datafiles.
    │  
    ├── images            <- Generated graphics to be used in reporting.
    │  
    ├── requirements.txt  <- File for reproducing the local environment.
    |
    ├── References.md     <- List of sources used in the project.
    |
    ├── .gitignore        <- Files to be ignored by Git.
    └──

## Specifications

For the Google Colab notebooks, all the requiremenets can be installed directly by running the notebook cells. A Healthcare Spark NLP license is required to run these two notebooks.
- `Healthcare_SparkNLP_Equine_Data_Exploration.ipynb` - contains the code and all the intermediary exploratory steps needed to build the main pipeline,
- `Healthcare_SparkNLP_Equine_Dataset.ipynb` - is a shorter version of the above notebook that contains only the final pipeline and results, it is the main notebook for the project.

Regarding the Jupyter notebooks, the neccessary packages are listed in  `requirements.txt`.
- `googleDataRetrieval.ipynb` - code to scrape data from the Google Scholar search,
- `dataPreprocessing.ipynb` - preprocess and save the datasets,
- `graphsResults.ipynb` - visualizations and analysis of the results.

## References

Various links and references are given in the Medium post. Also, a comprehensive list can be found in
- `References.md`
