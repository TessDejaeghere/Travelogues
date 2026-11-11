# Travelogues

This README details the **Metadata**, **Data**, **Annotations and annotation guide** and the **Notebooks** created for the methodological research on travelogues.
We evaluated different methodologies to apply aspect-based sentiment analysis to this literary-historical dataset. 

## Metadata 

The metadata for the entire corpus is split per collection, and can be downloaded via our [Drive folder](https://drive.google.com/drive/folders/17hqPMR-gi2fg1TbuBzBLrt-xcu-_1MO7?usp=sharing).

* **Biodiversity Heritage Library** | BHL_merged.csv
* **Travelogues Project** | TP_merged.csv
* **Italian Travelogues** | IT_merged.csv
* **Gutenberg Project** | GB_merged.csv
* **DBNL** | DBNL_merged.csv

Each .CSV-file contains the following columns:

* ID (new ID for processing the data)
* language (language the text is written in)
* title (title of the book)
* author (author of the book)
* date_published (year the book was published)
* Original_ID (original ID from the source. These are also the names of the text files in the gathered corpus.)
* no_of_character (number of characters)
* no_of_tokens (number of tokens as processed by SpaCy)
* OCR_quality (quality of the OCR according to the [Garbageness Score](https://ryanfb.xyz/etc/2015/03/16/automatic_evaluation_of_ocr_quality.html).


## Data (travelogues corpus)

Texts gathered are named according to the Original_ID column.

### **Biodiversity Heritage Library** 

The [BHL corpus](https://drive.google.com/drive/folders/1cig-uR5W7YILuDiVZLTOUnS1mkG-utQE?usp=drive_link) is published on our Drive due to its size. 
The dataset is split according to the key words used to scrape the texts (explor, journe, excurs, travel, expeditie, reis, trip). 
The texts contain a multitude of languages (Dutch, English, French, German, Portuguese, Latin, ...). 
The code used to scrape this data from the API is published in our **Notebooks** folder.

### **Gutenberg Project Travelogues**

The [Gutenberg corpus](https://drive.google.com/drive/folders/1HrFVVYageLbpl3tcDcajousWPVj-JnPx?usp=drive_link) is published on our Drive.
The texts are in both English and French.

### **DBNL dataset**

The [DBNL](https://drive.google.com/drive/folders/1-1uY54VHEr1XEGDglm-42K_NLeA3ip3j?usp=drive_link) is published on our Drive.
It contains all texts requested from the [DBNL website](https://www.dbnl.org/) that are related to travel. 
The texts are all in Dutch.

### **Italian Travels dataset**

The Italian Travels dataset can be gathered from the [project "Today we Have Passed with the Ancients...": Visions of Italy between XIX and XX century ](https://sites.google.com/view/travelwritingsonitaly/home?authuser=0).
Files are available in .TEI and .TXT.

### **German Travelogues Project dataset**

The German Travelogues Project dataset can be gathered from their [GitHub repository](https://github.com/travelogues/travelogues-corpus). More information on the corpus can be found on [their website](https://www.travelogues-project.info/).


## Annotations

We created an annotated dataset comprising texts in English, Dutch, German and French which were annotated for biodiversity-related aspects and their associated sentiment. 
The [annotated dataset](https://drive.google.com/file/d/1ebv8IeBg4fmuEcVnKrp3GqhCy2-wLp3F/view?usp=sharing) is published on our Drive.
The aspects annotated are further detailed in the **annotation_guide.PDF**. Sentiment-bearing words are annotated on a 1 (very negative) to 5 (very positive)-point scale. Sentiment was also annotated on the level of the sentence.
The .ZIP-file **_Annotations.zip_** contains the annotated files in UIMA CAS XMI (XML 1.1), and can easily be parsed using the [Inceptalytics API](https://github.com/catalpa-cl/inceptalytics).

The following aspects were considered: 

* PERSON
* LOCATION
* ORGANISATION
* FAUNA
* FLORA
* BIOME
* HUMAN_LANDFORM
* NATURAL_LANDFORM
* NATURAL_PHENOMENON
* WEATHER
* MYTH

## Notebooks

Four notebooks are cleaned up and made available for reuse and further adaptation to specific use-cases within DH.
These notebooks were developed to apply aspect-based sentiment analysis to the English subset of our annotated data.
All notebooks detail two steps: 1) aspect extraction and 2) sentiment analysis.
Aspect extraction is evaluated by turning the annotations into BIO-labels and then using the dependency **Nervaluate**. 
Sentiment analysis is evaluated on the gold standard annotations.

### Rule-based ABSA

Rule_based_ABSA.ipynb for aspect extraction and sentiment analysis.

**Correction (2026).** The published version mapped the mean SenticNet score (range -1 to 1) onto 0-1 with a sigmoid before cutting it into the five labels at 0.2 / 0.4 / 0.6 / 0.8. A sigmoid maps [-1, 1] onto roughly [0.27, 0.73], so labels 1 and 5 could never be predicted. The notebook now rescales the score linearly, (x + 1) / 2. See the note above the evaluation cells in the notebook for the recomputed scores.

### Machine learning-based ABSA

1. ML_based_ABSA_aspects.ipynb (aspect extraction).
2. ML_based_ABSA_feature_extraction_classification.ipynb (extracting MacBERT and BERT embeddings as features and use the embeddings in a set of classifiers).

### Generative LLM-based ABSA
Prompt-based workflow based on the [mistralai/Mixtral-8x7B-Instruct-v0.1](https://huggingface.co/mistralai/Mixtral-8x7B-Instruct-v0.1) model. 
Perform aspect extraction and sentiment analysis respectively.

GENLLM_based_ABSA.ipynb 

### Scraping texts from the Biodiversity Heritage Library API
Code to scrape the BHL website based on keywords.

Scraping_BHL.ipnyb 

## Corrections after publication

The paper (LT4HALA 2024) was re-examined for Tess Dejaeghere's PhD thesis. The following errors were found; the thesis reports the corrected numbers.

* **Rule-based sentiment mapping** (`Rule_based_ABSA.ipynb`): the sigmoid mapping made labels 1 and 5 unreachable; fixed in the notebook (see above). Published: macro F1 0.19, weighted F1 0.32 (1-5 scale). Corrected: macro F1 0.23, weighted F1 0.33 (1-5) and macro F1 0.42, weighted F1 0.48 (1-3).
* **Mixtral 3-class evaluation** (`GENLLM_based_ABSA.ipynb`): gold labels and predictions were collapsed to three classes with different mappings (gold {1} / {2, 3} / {4, 5}, predictions {1, 2} / {3} / {4, 5}). With one shared mapping ({1, 2} / 3 / {4, 5}) the macro F1 is 0.56 instead of the published 0.42.
* **Reported metrics**: the paper's sentiment table gave weighted F1 for the rule-based system and macro F1 for Mixtral in one column.
* **Machine-learning sentiment** (`ML_based_ABSA_feature_extraction_classification.ipynb`): the BERT/MacBERTh embeddings were extracted from the pre-trained models; they were not fine-tuned, although the paper says they were.
* **API keys**: the notebooks now read the Hugging Face token (`HF_TOKEN`) and the BHL API key (`BHL_API_KEY`) from environment variables.
