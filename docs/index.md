<p align="center">
    <a href="https://github.com/Novetta/adaptnlp"> <img src="https://raw.githubusercontent.com/novetta/adaptnlp/master/docs/img/NovettaAdaptNLPlogo-400px.png" width="400"/></a>
</p>

<p align="center">
<strong> A high level framework and library for running, training, and deploying state-of-the-art Natural Language Processing (NLP) models for end to end tasks.</strong>
</p>
<p align="center">
    <a href="https://circleci.com/gh/Novetta/adaptnlp">
        <img src="https://img.shields.io/circleci/build/github/Novetta/adaptnlp/master">
    </a>
    <a href="https://badge.fury.io/py/adaptnlp">
        <img src="https://badge.fury.io/py/adaptnlp.svg">
    </a>
    <a href="https://github.com/Novetta/adaptnlp/blob/master/LICENSE">
        <img src="https://img.shields.io/github/license/novetta/adaptnlp">
    </a>
</p>


AdaptNLP allows users ranging from beginner python coders to experienced machine learning engineers to leverage
state-of-the-art NLP models and training techniques in one easy-to-use python package.

Built atop Zalando Research's Flair and Hugging Face's Transformers library, AdaptNLP provides Machine
Learning Researchers and Scientists a modular and **adaptive** approach to a variety of NLP tasks with an
**Easy** API for training, inference, and deploying NLP-based microservices.

**Key Features**

  - **[Full Guides and API Documentation](https://novetta.github.io/adaptnlp)**
  - [Tutorial](https://github.com/Novetta/adaptnlp/tree/master/tutorials) Jupyter/Google Colab Notebooks
  - Unified API for NLP Tasks with SOTA Pretrained Models (Adaptable with Flair and Transformer's Models)
    - Token Tagging 
    - Sequence Classification
    - Embeddings
    - Question Answering
    - Summarization
    - Translation
    - Text Generation
    - <em> More in development </em>
  - Training and Fine-tuning Interface
    - Integration with Transformer's Trainer Module for fast and easy transfer learning with custom datasets
    - Jeremy's **[ULM-FIT](https://arxiv.org/abs/1801.06146)** approach for transfer learning in NLP
    - Fine-tuning Transformer's language models and task-specific predictive heads like Flair's `SequenceClassifier`
  - [Rapid NLP Model Deployment](https://github.com/Novetta/adaptnlp/tree/master/rest) with Sebastián's [FastAPI](https://github.com/tiangolo/fastapi) Framework
    - Containerized FastAPI app
    - Immediately deploy any custom trained Flair or AdaptNLP model
  - [Dockerizing AdaptNLP with GPUs](https://hub.docker.com/r/achangnovetta/adaptnlp)
    - Easily build and run AdaptNLP containers leveraging NVIDIA GPUs with Docker


## Quick Start

#### Requirements and Installation for Linux/Mac

Note: AdaptNLP will be using the latest stable torch version (v1.7 as of 11/2/20) which requires Python 3.7+. Please downgrade torch<=1.6 if using Python 3.6

##### Virtual Environment
To avoid dependency clustering and issues, it would be wise to install AdaptNLP in a virtual environment.
To create a new python 3.7+ virtual environment, run this command and then activate it however your operating
system specifies:

```
python -m venv venv-adaptnlp
```

##### AdaptNLP Install
Install using pip in your virtual environment:
```
pip install adaptnlp
```

If you want to work on AdaptNLP, `pip install adaptnlp[dev]` will install its development tools.

#### Requirements and Installation for Windows

##### PyTorch Install
PyTorch needs to manually installed on Windows environments. If it's not already installed, proceed to http://pytorch.org/get-started/locally to select your preferences and then run the given install command. Note that the current version of PyTorch we use relies on cuda 10.1.

##### AdaptNLP Install
Install using pip:
```
pip install adaptnlp
```

If you want to work on AdaptNLP, `pip install adaptnlp[dev]` will install its development tools.

#### Examples and General Use

Once you have installed AdaptNLP, here are a few examples of what you can run with AdaptNLP modules:

##### Named Entity Recognition with `EasyTokenTagger`

```python
from adaptnlp import EasyTokenTagger

## Example Text
example_text = "Novetta's headquarters is located in Mclean, Virginia."

## Load the token tagger module and tag text with the NER model 
tagger = EasyTokenTagger()
sentences = tagger.tag_text(text=example_text, model_name_or_path="ner")

## Output tagged token span results in Flair's Sentence object model
for sentence in sentences:
    for entity in sentence.get_spans("ner"):
        print(entity)

```
<details class = "summary">
 <summary>Output</summary>
```python
Span [1]: "Novetta"   [− Labels: ORG (0.9925)]
Span [7]: "Mclean"    [− Labels: LOC (0.9993)]
Span [9]: "Virginia"  [− Labels: LOC (1.0)]
```
</details>
##### English Sentiment Classifier `EasySequenceClassifier`

```python
from adaptnlp import EasySequenceClassifier 
from pprint import pprint

## Example Text
example_text = "This didn't work at all"

## Load the sequence classifier module and classify sequence of text with the multi-lingual sentiment model 
classifier = EasySequenceClassifier()
sentences = classifier.tag_text(
    text=example_text,
    model_name_or_path="nlptown/bert-base-multilingual-uncased-sentiment",
    mini_batch_size=1,
)

## Output labeled text results in Flair's Sentence object model
print("Tag Score Outputs:\n")
for sentence in sentences:
    pprint({sentence.to_original_text(): sentence.labels})

```
<details class = "summary">
<summary>Output</summary>
```python
    2020-08-31 02:21:25,011 loading file nlptown/bert-base-multilingual-uncased-sentiment


    Predicting text: 100%|██████████| 1/1 [00:00<00:00, 98.67it/s]

    Tag Score Outputs:
    
    {"This didn't work at all": [1 star (0.8421),
                                 2 stars (0.1379),
                                 3 stars (0.018),
                                 4 stars (0.0012),
                                 5 stars (0.0007)]}
```
</details>

##### Span-based Question Answering `EasyQuestionAnswering`

```python
from adaptnlp import EasyQuestionAnswering 
from pprint import pprint

## Example Query and Context 
query = "What is the meaning of life?"
context = "Machine Learning is the meaning of life."
top_n = 5

## Load the QA module and run inference on results 
qa = EasyQuestionAnswering()
best_answer, best_n_answers = qa.predict_qa(query=query, context=context, n_best_size=top_n, mini_batch_size=1, model_name_or_path="distilbert-base-uncased-distilled-squad")

## Output top answer as well as top 5 answers
print(best_answer)
pprint(best_n_answers)
```
<details class = "summary">
<summary>Output</summary>
```python
[OrderedDict([('text', 'Machine Learning'), ('probability', 0.9978840517692185), ('start_logit', 8.442423820495605), ('end_logit', 8.063008308410645), ('start_index', 0), ('end_index', 1)]), OrderedDict([('text', 'Machine'), ('probability', 0.0010938238341776795), ('start_logit', 8.442423820495605), ('end_logit', 1.2470508813858032), ('start_index', 0), ('end_index', 0)]), OrderedDict([('text', 'Machine Learning is the meaning of life.'), ('probability', 0.0007310516091540702), ('start_logit', 8.442423820495605), ('end_logit', 0.8440999984741211), ('start_index', 0), ('end_index', 6)]), OrderedDict([('text', 'Machine Learning is the meaning'), ('probability', 0.00020125385987753433), ('start_logit', 8.442423820495605), ('end_logit', -0.4458169639110565), ('start_index', 0), ('end_index', 4)]), OrderedDict([('text', 'Learning'), ('probability', 8.981892757199805e-05), ('start_logit', -0.8731728196144104), ('end_logit', 8.063008308410645), ('start_index', 1), ('end_index', 1)])]
```
</details>
##### Summarization `EasySummarizer`

```python
from adaptnlp import EasySummarizer

# Text from encyclopedia Britannica on Einstein
text = """Einstein would write that two “wonders” deeply affected his early years. The first was his encounter with a compass at age five. 
          He was mystified that invisible forces could deflect the needle. This would lead to a lifelong fascination with invisible forces. 
          The second wonder came at age 12 when he discovered a book of geometry, which he devoured, calling it his 'sacred little geometry 
          book'. Einstein became deeply religious at age 12, even composing several songs in praise of God and chanting religious songs on 
          the way to school. This began to change, however, after he read science books that contradicted his religious beliefs. This challenge 
          to established authority left a deep and lasting impression. At the Luitpold Gymnasium, Einstein often felt out of place and victimized 
          by a Prussian-style educational system that seemed to stifle originality and creativity. One teacher even told him that he would 
          never amount to anything."""

summarizer = EasySummarizer()


# Summarize
summaries = summarizer.summarize(text = text, model_name_or_path="t5-small", mini_batch_size=1, num_beams = 4, min_length=0, max_length=100, early_stopping=True)

print("Summaries:\n")
for s in summaries:
    print(s, "\n")
```

<details class="summary">
<summary>Output</summary>
```
Einstein was mystified invisible forces could deflect the needle . the second wonder came at age 12 when he discovered a book of geometry . Einstein became deeply religious at age 12 . 
```
</details>

##### Translation `EasyTranslator`
```python
from adaptnlp import EasyTranslator

text = ["Machine learning will take over the world very soon.",
        "Machines can speak in many languages.",]

translator = EasyTranslator()

# Translate
translations = translator.translate(text = text, t5_prefix="translate English to German", model_name_or_path="t5-small", mini_batch_size=1, min_length=0, max_length=100, early_stopping=True)

print("Translations:\n")
for t in translations:
    print(t, "\n")
```
<details class="summary">
<summary>Output</summary>
```
Das Maschinelle Lernen wird die Welt in Kürze übernehmen. 
Maschinen können in vielen Sprachen sprechen. 
```
</details>

## Tutorials

Look in the [Tutorials](tutorials) directory for a quick introduction to the library and its very simple
and straight forward use cases:

**NLP Tasks**

  1. [Token Classification: NER, POS, Chunk, and Frame Tagging](tutorials/1.%20Token%20Classification)
      - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/1.%20Token%20Classification/token_tagging.ipynb)
  2. [Sequence Classification: Sentiment](tutorials/2.%20Sequence%20Classification)
      - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/2.%20Sequence%20Classification/Easy%20Sequence%20Classifier.ipynb)
  3. [Embeddings: Transformer Embeddings e.g. BERT, XLM, GPT2, XLNet, roBERTa, ALBERT](tutorials/3.%20Embeddings)
      - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/3.%20Embeddings/embeddings.ipynb)
  4. [Question Answering: Span-based Question Answering Model](tutorials/4.%20Question%20Answering)
      - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/4.%20Question%20Answering/question_answering.ipynb)
  5. [Summarization: Abstractive and Extractive](tutorials/5.%20Summarization)
      - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/5.%20Summarization/summarization.ipynb)
  6. [Translation: Seq2Seq](tutorials/6.%20Translation)
      - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/6.%20Translation/translation.ipynb)

**[Custom Fine-Tuning and Training with Transformer Models](tutorials/Finetuning%20and%20Training%20(Advanced))**

 - Fine-tuning a Transformers Language Model
   - [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Novetta/adaptnlp/blob/master/tutorials/Finetuning%20and%20Training%20(Advanced)/Fine-tuning%20Language%20Model.ipynb)
  
Checkout the [documentation](https://novetta.github.io/adaptnlp) for more information.
  
## REST Service 

We use FastAPI for standing up endpoints for serving state-of-the-art NLP models with AdaptNLP.

![Swagger Example](https://raw.githubusercontent.com/novetta/adaptnlp/master/docs/img/fastapi-docs.png)

The [REST](https://github.com/Novetta/adaptnlp/tree/master/rest) directory contains more detail on deploying a REST API locally or with docker in a very easy and
fast way.
  
## Docker

AdaptNLP official docker images are up on [Docker Hub](https://hub.docker.com/r/achangnovetta/adaptnlp).

Images have AdaptNLP installed from source in developer mode with tutorial notebooks available.
 
Images can build with GPU support if NVIDA-Docker is correctly installed.

#### Pull and Run AdaptNLP Immediately
Simply run an image with AdaptNLP installed from source in developer mode by running:
```
docker run -it --rm achangnovetta/adaptnlp:latest
```
Run an image with AdaptNLP running on GPUs if you have nvidia drivers and nvidia-docker 19.03+ installed:
```
docker run -it --rm --gpus all achangnovetta/adaptnlp:latest
```

#### Build

Build docker image and run container with the following commands in the directory of the Dockerfile
to create a container with adaptnlp installed and ready to go

Note: A container with GPUs enabled requires Docker version 19.03+ and nvida-docker installed
```
docker build -t achangnovetta/adaptnlp:latest .
docker run -it --rm achangnovetta/adaptnlp:latest
```
If you want to use CUDA compatible GPUs 
```
docker run -it --rm --gpus all achangnovetta/adaptnlp:latest
```

## Contact

Please contact the author Andrew Chang at achang@novetta.com with questions or comments regarding AdaptNLP.

Follow  us on Twitter at [@achang1618](https://twitter.com/achang1618) and [@AdaptNLP](https://twitter.com/AdaptNLP) for
updates and NLP dialogue.

## License

This project is licensed under the terms of the Apache 2.0 license.
 
