# World Bank Document Analysis Pipeline

This repository contains a series of scripts for scraping, processing, and analyzing World Bank documents using natural language processing and AI classification techniques. :contentReference[oaicite:0]{index=0}&#8203;:contentReference[oaicite:1]{index=1}

## Pipeline Overview

The workflow consists of four main stages:

1. **Data Collection:** Scraping document metadata from the World Bank portal  
2. **Text Extraction:** Downloading raw text content from documents  
3. **Text Preprocessing:** Cleaning and preparing text data for analysis  
4. **Policy Classification:** Using AI to classify documents by policy areas :contentReference[oaicite:2]{index=2}&#8203;:contentReference[oaicite:3]{index=3}

## Scripts

> **Note:** Each script in this repository is thoroughly documented with comments on every line of code.

1. **01_Getting_URLs.ipynb**  
   Scrapes the World Bank documents portal using Selenium to:  
   - Paginate through document listings  
   - Extract metadata (project name, link, report number, etc.)  
   - Export data to CSV for further processing :contentReference[oaicite:4]{index=4}&#8203;:contentReference[oaicite:5]{index=5}

2. **02_Retrieve_TXT.ipynb**  
   Processes the document URLs collected in the previous step:  
   - Reads the CSV of document URLs  
   - Navigates to each detail page (handling pop-ups and alerts)  
   - Downloads raw text content  
   - Saves individual files (format: `Report_[ID]_RAW.txt`)  
   - Logs any errors encountered during the process :contentReference[oaicite:6]{index=6}&#8203;:contentReference[oaicite:7]{index=7}

3. **03_Pre_Process_TEXT.ipynb**  
   Prepares the raw text files for analysis:  
   - Loads raw `.txt` reports  
   - Applies extractive compression  
   - Tokenizes and lemmatizes text using spaCy  
   - Removes stopwords and punctuation  
   - Extracts operation IDs via regex  
   - Writes cleaned texts to disk (format: `ID_P[ProjectID]_[ReportID].txt`) :contentReference[oaicite:8]{index=8}&#8203;:contentReference[oaicite:9]{index=9}

4. **04_MODEL.ipynb**  
   Performs AI-based policy classification:  
   - Loads preprocessed reports  
   - Loads "universe" of policy topics (categories: health and education)  
   - Constructs prompts for the OpenAI API  
   - Scores and classifies each report by policy area  
   - Gathers supporting evidence for classifications  
   - Saves results as a JSON file (`classification_results_test2.json`) :contentReference[oaicite:10]{index=10}&#8203;:contentReference[oaicite:11]{index=11}

## File Formats

### Input Files

- **Raw text files:** `Report_44351_RAW.txt`  
- **Processed text files:** `ID_P101471_44351.txt`  
- **Universe files:**  
  - Original: `UNIVERSE_Prior_Actions_DESCRIPTION.txt`  
  - Processed: `UNIVERSE_Prior_Actions_PROCESSED.txt` :contentReference[oaicite:12]{index=12}&#8203;:contentReference[oaicite:13]{index=13}

### Output Files

- **Classification results:** `classification_results_test2.json`  
- **Validation webpage:** `index.html` :contentReference[oaicite:14]{index=14}&#8203;:contentReference[oaicite:15]{index=15}

## AI Classification Prompt

The following prompt is used with OpenAI GPT-3.5 Turbo for document classification:

```python
prompt = (
    "You are an expert in policy analysis and classification. Below is the universe description:\n"
    f"{universe_summary}\n\n"
    "Now, consider the following policy report text:\n\n"
    f"{report_text}\n\n"
    "Your task is to classify this report by determining which topic it is most related to, providing:\n"
    "  1. A similarity score between 0 and 1 (0 means no relation; 1 means very strong relation).\n"
    "  2. Specific sentences or excerpts from the report that are directly relevant to each policy area.\n"
    "  3. A detailed explanation of why that score was assigned, explicitly referencing the evidence.\n"
    "If the report does not clearly address a policy area, assign a score near 0 and explain why.\n"
    "Return your answer as a JSON object in the following structure:\n"
    '{\n'
    '  "matched_topic": "Name of topic",\n'
    '  "policy_scores": {\n'
    '      "Policy Area Name": score, ...\n'
    '  },\n'
    '  "evidence": {\n'
    '      "Policy Area Name": ["relevant excerpt 1", "relevant excerpt 2", ...], ...\n'
    '  },\n'
    '  "explanation": "Detailed explanation with references to the evidence for each policy area."\n'
    '}\n'
    "Ensure the JSON is properly formatted."
)
``` 

## Validation

The results of the classification can be viewed in the included `index.html` file, which provides a web interface for exploring the analysis outcomes. :contentReference[oaicite:18]{index=18}&#8203;:contentReference[oaicite:19]{index=19}
