<br/>
<h1 align="center">
    <img src="http://code.benco.io/icon-collection/azure-docs/blockchain-workbench.svg" alt="quotx logo" width=200>
    <br/>
    <i>QuotX</i>
    <br/>
</h1>
<h4 align="center">Quote eXtraction with NLP</h4>
<p align="center">
    <a href="#-introduction">Introduction</a> •
    <a href="#-installation">Installation</a> •
    <a href="#-how-to-use-quotx-">How to use <i>QuotX</i> ?</a> •
    <a href="#-how-quotx-works-">How <i>QuotX</i> works ?</a> •
    <a href="#-storage--channels">Storage & Channels</a> •
    <a href="#-test--cicd">Test & CI/CD</a>
</p>

_QuotX_ is an Named Entity Recognition (NER) bot that extracts predefined entities (such as bid/ask quote, price, notional or product maturity) from unstructured text data. The extracted information can be onboarded into trading management systems and provide valuable data to analysis platforms. _QuotX_ is based on the following <b>open source\*</b> platforms : <br> • [_Rasa_](https://github.com/RasaHQ/rasa "rasa github repository") an open source Machine Learning (ML) framework for automated text and voice-based conversations. <br> • [_Chatette_](https://github.com/SimGus/Chatette "chatette github repository") a Python program that generates training datasets for _Rasa_ given template files. <br> • [_Neo4j_](https://github.com/neo4j "neo4j github repository") a graph database that stores and manages data in a connected state, maintaining data relationships that deliver fast (NLP) queries and deeper context for analytics. <br> • [_Grichka_](https://gitlab.ca.cib/CMI/NON-LINEAR-RATES/archi-innovation/grichka-platform "grichka gitlab repository") an open, flexible
and NLP empowered chatbot that can integrate business use cases in a generic way.

> \* Open source is a collaborative and peer-to-peer system of sharing information, knowledge and lines of code. It is a rethinking of how to build and distribute software with transparency, integrability and extensibility at its core. Open source allows more freedom and helps to avoid vendor lock-in. Best of all, it is pretty affordable, and allows costs to scale gradually.

# <img src="http://code.benco.io/icon-collection/azure-patterns/command-line.svg" alt="quotx prompt" width=25> Introduction

A large percentage of financial data is unstructured. This pdf, email, chat or voice data has been much less accessible than structured data. But thanks to the democratization and open sourcing of ML tools during the last 5 years, new ways of providing and interacting with data have emerged. And this is where <b>NLP\*</b> comes into play :

> \* NLP is a field of linguistics and Machine Learning focused on understanding everything related to human language. The aim of NLP tasks is not only to understand single words individually, but to be able to understand the context of those words.

_QuotX_ uses NLP to capture key information from chats and participate in the automation of Request For Quote (RFQ) workflows. We believe that adding bots in chat rooms and negociation platforms can reduce time-to-price and provide market insights either in streaming mode (for signals and alerting) or in batched mode (for daily recaps and monthly data reconciliation).

<img src="https://raw.githubusercontent.com/lekse02/quotx/main/quotx-pillars.svg" alt="quotx entities" width=500>

# <img src="http://code.benco.io/icon-collection/azure-patterns/command-line.svg" alt="quotx prompt" width=25> Installation

<details><summary><b>1. Install Python</b></summary>
<p>

First, you will need to have [Python 3](https://www.python.org/downloads/ "python download") installed. For a Python data science platform you can install [Anaconda](https://www.anaconda.com/products/individual- "anaconda download") or [Miniconda](https://docs.conda.io/en/latest/miniconda.html "miniconda download"). _QuotX_ is compatible with Python version 3.x (>= 3.8). <br> Make sure the Microsoft VC++ Compiler is installed, so Python can compile any dependencies. You can get the compiler from [Visual Studio](https://visualstudio.microsoft.com/fr/visual-cpp-build-tools/ "c++ compiler download"). Download the installer and select VC++ Build tools in the list. <br> After the installation, check your Python environment :

```bash
python3 --version
pip3 --version
```

Create a new virtual environment by choosing a Python interpreter and making a .venv directory to hold it :

```bash
C:\quote-extractor> python3 -m venv ./.venv
```

Activate the virtual environment :

```bash
C:\quote-extractor> .\.venv\Scripts\activate
```

</p>
</details>

<details><summary><b>2. Install dependencies</b></summary>
<p>

Run :

```bash
(.venv) C:\quote-extractor> pip install -r requirements.txt
```

To install development dependencies, run :

```bash
(.venv) C:\quote-extractor> pip install -r requirements-dev.txt
(.venv) C:\quote-extractor> pre-commit install
(.venv) C:\quote-extractor> python -m spacy download en_core_web_md en
(.venv) C:\quote-extractor> python -m spacy link en_core_web_md en
```

> With pre-commit installed, the `black` and `doctoc` hooks will run on every `git commit`.
> If any changes are made by the hooks, you will need to re-add changed files and re-commit your changes.

</p>
</details>

<details><summary><b>3. Run the bot</b></summary>
<p>

If no `tar.gz` file is in the `./models` folder, you will have to train a model. To train a new model, run :

```bash
(.venv) C:\quote-extractor> rasa train --data data/nlu
```

> Be aware that the training phase can take several hours.

Set up your action server in one terminal window, listening on port 5056 :

```bash
(.venv) C:\quote-extractor> rasa run actions --port 5056
```

In another window, run the _Duckling_ server (for numerical entity extraction) :

```bash
docker run -p 8000:8000 rasa/duckling
```

Then to talk to the bot, run :

```
(.venv) C:\quote-extractor> rasa shell --debug
```

Note that `--debug` mode will produce a lot of output meant to help you understand how the bot is working under the hood. To simply talk to the bot, you can remove this flag. <br> You can also try out your bot locally using _Rasa X_ by running :

```
(.venv) C:\quote-extractor> rasa x
```

Send a basic quote extraction request to the bot (e.g. `3m10y 155/160`). <br> If everything goes well, you should receive the extracted entities 🚀

</p>
</details>

# <img src="http://code.benco.io/icon-collection/azure-patterns/command-line.svg" alt="quotx prompt" width=25> How to use _QuotX_ ?

<img src="https://raw.githubusercontent.com/lekse02/quotx/main/quotx-entities.svg" alt="quotx entities" width=550>

<details><summary><b>1. Predefined entities</b></summary>
<p>

_QuotX_ is trained to recognize and extract the following entities :

`source` <font size="2">_e.g. rate derivative, equity derivative_</font>

`time`

`counterparty`

`clearinghouse`

`color` <font size="2">_e.g. quotation, trading, confirmation, interest_</font>

`cms rate`

`swap rate`

`index rate`

`risk free rate` <font size="2">_e.g. ester, sonia, saron, sofr, tona_</font>

`expiry` · `tenor` · `forward` · `maturity`

`strike`

`underlying` <font size="2">_e.g. payer, receiver, straddle, strangle, collar, index, stock_</font>

`exchange`

`instrument`

`bid` · `ask` · `mid price` · `price`

`quote level`

`side` <font size="2">_i.e. buy, sell_</font>

`direction` <font size="2">_i.e. bid, offer, 2-way_</font>

`notional` · `indicative size` · `ratio`

`currency` <font size="2">_e.g. EUR, JPY, USD, GBP_</font>

`country`

`strategy` <font size="2">_e.g. spread, steepener, flattener, butterfly, triangle, box_</font>

`product` <font size="2">_e.g. swaption, [fwd] bond option, [fwd] [CMS] rate option, [fwd] rate swap, [fwd] basis swap, [CMS] FRA, index future/SSF, [index/equity] TRF, index dividend future/SSDF, [index future/SSF] roll, TRS, EFP, all in, cash close_</font>

`settlement` <font size="2">_i.e. cash, physical_</font>

`premium payment date` <font size="2">_i.e. spot, forward_</font>

</p>
</details>

<details><summary><b>2. Extraction modes</b></summary>
<p>

You can request extractions using :

`streaming mode` <font size="2">_the request contains only one sentence_</font>

`batched mode` <font size="2">_the request includes several sentences chronologically listed_</font>

</p>
</details>

<details><summary><b>3. Continuous training</b></summary>
<p>

Continuous training is the process of collecting and reviewing extraction requests and using those insights to improve the bot. Once a new trained model is in production, we measure its improvement, track when the bot fails and try to fix incorrectly extracted entities. <br> Before retraining and redeploying a new model, the following files can be modified :

`domain.yml` <font size="2">_the domain file including entities, slots, bot responses, actions and forms_</font>

`config.yml` <font size="2">_contains training configurations for the NLU pipeline and policy ensemble_</font>

`data/nlu/nlu.yml` <font size="2">_contains intent examples, synonyms, regex and lookup examples_</font>

`data/nlu/*/*.yml` <font size="2">_contains annotated training data_</font>

`data/templates/*/*.chatette` <font size="2">_contains Chatette templates for data augmentation_</font>

`data/rules/rules.yml` <font size="2">_contains rules mapping intents to extraction forms_</font>

`data/knowledge/knowledge.json` <font size="2">_contains defaulting entity values per user_</font>

`actions/*/*.py` <font size="2">_contains code for custom extraction, validation and rendering_</font>

`actions/templates/` <font size="2">_contains templates for basic and rich rendering responses_</font>

`tests/test_*.yml` <font size="2">_contains validation tests for annotated training data_</font>

`tests/test_*.py` <font size="2">_contains unit tests for rule-based extractions_</font>

</p>
</details>

<details><summary><b>4. Data augmentation</b></summary>
<p>

Data augmentation is the process of generating synthetic training data using techniques like synonym replacement, random insertion or deletion, bigram or n-gram swap. <br> We use [_Chatette_](https://github.com/SimGus/Chatette "chatette github repository") as a template engine to generate synthetic auto annotated broker quotes and avoid the time-consuming work of manual annotation.

```bash
[12:18:55](time)
[LF]{"entity": "color", "role": "interest"}
[10y10y]{"entity": "schedule", "role": "expiry_tenor", "group": "leg1"}
[p+10%]{"entity": "strike", "role": "strike_price", "group": "leg1"}
[21/]{"entity": "quote", "role": "bid", "group": "leg1"}
[[21]{"entity": "quote", "role": "mid_price", "group": "leg1"}]
[sc+](quote_level)
pls
```

To generate new training data, make the relevant changes in the `data/templates/*/*.chatette` model files. <br> Then run one of the `scripts/chatette/*/generator.py` data generator script. <br> The training data is generated in the corresponding `data/templates/*/*/output/` output folder and can be moved in the right `data/nlu/*/*/` nlu data folder. <br> To validate the generated data, run :

```bash
(.venv) C:\quote-extractor> rasa data validate
```

</p>
</details>

<details><summary><b>5. Model train-test</b></summary>
<p>

Once generated and validated, the synthetic data can be used to train a new model :

```bash
(.venv) C:\quote-extractor> rasa train --data data/nlu
```

`rasa train` will store the trained model in the `models/` directory. The name of the model by default is `<timestamp>.tar.gz`.

To create a train-test split of your NLU training data, run:

```bash
(.venv) C:\quote-extractor> rasa data split nlu
```

This will create a 80/20 split of train/test data by default in the `train_test_split/` directory.

To evaluate the model on your test data, run:

```bash
(.venv) C:\quote-extractor> rasa test nlu --nlu train_test_split/test_data.yml --out results/train_test_split
```

This will test your latest trained model on any NLU data defined in the `test_data.yml` file and will store any files created during the evaluation in the `results/train_test_split/` folder. The model evaluation metrics (precision, recall, f1-score and support) can be compared with older models metrics.

You can also unit test the trained model with a list of predefined quotes to make sure that the model makes no error when extracting the expected entities :

```bash
(.venv) C:\quote-extractor> rasa test nlu --nlu tests/test_nlu.yml --out results/tests
```

</p>
</details>

# <img src="http://code.benco.io/icon-collection/azure-patterns/command-line.svg" alt="quotx prompt" width=25> How <i>QuotX</i> works ?

<img src="https://raw.githubusercontent.com/lekse02/quotx/main/quotx-nlp.svg" alt="quotx nlp" width=600>

<details><summary><b>1. Breaking down language into discrete numerical parts</b></summary>
<p>

`todo`

</p>
</details>

<details><summary><b>2. Comprehending what is being said</b></summary>
<p>

`todo`

</p>
</details>

<details><summary><b>3. Determining the right response</b></summary>
<p>

`todo`

</p>
</details>

# <img src="http://code.benco.io/icon-collection/azure-patterns/command-line.svg" alt="quotx logo" width=25> Storage & Channels

<details><summary><b>1. Knowledge base</b></summary>
<p>

`todo`

</p>
</details>

<details><summary><b>2. Multi-channel communication</b></summary>
<p>

`todo`

</p>
</details>

# <img src="http://code.benco.io/icon-collection/azure-patterns/command-line.svg" alt="quotx prompt" width=25> Test & CI/CD

<img src="https://raw.githubusercontent.com/lekse02/quotx/main/quotx-services.svg" alt="quotx services" width=600>

<details><summary><b>1. Testing the bot</b></summary>
<p>

<br/>
<img src="https://raw.githubusercontent.com/lekse02/quotx/main/quotx-gitflow.svg" alt="quotx gitflow" width=600>

`todo`

</p>
</details>

<details><summary><b>2. Deploying the bot</b></summary>
<p>

<br/>
<img src="https://raw.githubusercontent.com/lekse02/quotx/main/quotx-elements.svg" alt="quotx elements" width=600>

`todo`

</p>
</details>
