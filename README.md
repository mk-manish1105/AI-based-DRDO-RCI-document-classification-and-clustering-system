# DRDO Document Classification and Clustering Pipeline

This project is designed to process, classify, and cluster a large collection of sensitive DRDO documents. It automatically classifies documents into **five strict security levels** based on weak supervision and then **clusters the Unclassified documents** for further analysis.

---

## 🔍 Objectives

- Automatically extract text from various document formats.
- Classify documents into 5 categories:
  - `Top Secret`
  - `Secret`
  - `Confidential`
  - `Restricted`
  - `Unclassified`
- Use keyword, TF-IDF, and embedding-based weak supervision for classification.
- Cluster only `Unclassified` documents using UMAP + HDBSCAN.
- Auto-tune clustering parameters for best quality grouping.
- Organize outputs for human review and decision-making.

---

## 🗂️ Folder Structure

```
DRDO_Document_Clustering/
│
├── main.py                         # Entry point (runs complete pipeline)
├── extract_texts.py               # Extracts text from PDFs, DOCX, PPTX, XLSX, images, etc.
├── embedding_pipeline.py          # Generates document embeddings using E5 model
├── cluster_param_search.py        # Searches best UMAP+HDBSCAN parameters
├── clustering_pipeline.py         # Final UMAP reduction + HDBSCAN clustering
├── cluster_output_manager.py      # Organizes clustered documents and keywords
├── tfidf_utils.py                 # Preprocessing + TF-IDF scoring logic
├── classify_documents.py          # Combines all scores to classify into 5 levels
├── phrase_utils.py                # Joins defense phrases (e.g., "missile launch" → "missile_launch")
├── keyword_seed_config.py         # Stores seed examples and keyword lists for each category
├── utils_text.py                  # Utility for category thresholds
│
├── models/                        # Offline transformer models (E5 + MiniLM for KeyBERT)
├── prj_data/                      # Input folder (store your documents here)
├── prj_output/                    # Output folder (classification + clusters)
├── nltk_data/                     # NLTK tokenizer data (sentence tokenizer)
└── offline_packages/              # Offline pip wheels (optional)
```

---

## 📦 Supported Input File Types

- `.pdf` (native and scanned OCR)
- `.docx`
- `.pptx`
- `.xlsx`, `.xls`
- `.txt`
- `.jpg`, `.jpeg`, `.png` (image OCR using Tesseract)

---

## 🧠 Classification Technique

Each document is scored per category based on:
- ✅ Embedding similarity with category seed sentences
- ✅ Exact keyword matches (after phrase joining)
- ✅ TF-IDF score for category-specific terms

Each category has a configurable threshold (`keyword_seed_config.py`), and the highest scoring label above its threshold is selected. If none exceed, document is marked **Unclassified**.

---

## 🔎 Clustering (Only for Unclassified)

- Generates embeddings for Unclassified documents using **E5-large-v2**.
- Applies **UMAP** for dimensionality reduction.
- Auto-tunes **HDBSCAN** clustering parameters using **silhouette score**.
- Clusters are saved into separate folders, named by **dominant keyword** using **KeyBERT**.

---

## ✅ How to Run

1. **Put your documents** into the `prj_data/` folder.
2. Make sure models are available offline inside `models/`.
3. Run the full pipeline:

```bash
python main.py
```

---

## 🧾 Outputs

- `prj_output/classification_scores.txt` → Detailed log of scores per file
- `prj_output/Top Secret/`, `Secret/`, etc. → Classified document folders
- `prj_output/Unclassified/Cluster_*/` → Clusters of unclassified files
- `unclassified_embeddings.npy` → Saved embedding matrix
- `clustered_file_list.txt` → Mapping of files to cluster IDs
- `cluster_labels.txt` → Cluster ID to folder name mapping (with keyword)

---

## 🛠️ Requirements (Offline Compatible)

Python 3.10.11 with the following libraries:
```
torch==2.1.2
transformers==4.35.2
sentence-transformers==2.4.0
scikit-learn==1.3.2
nltk==3.8.1
keybert==0.8.2
umap-learn==0.5.5
hdbscan==0.8.40
pandas==1.5.3
numpy==1.24.3
openpyxl==3.1.2
python-docx==1.2.0
python-pptx==1.0.2
PyMuPDF==1.22.5
pytesseract==0.3.10
Pillow==10.3.0
tqdm==4.66.1
joblib==1.5.1
lxml==5.4.0
psutil==7.0.0
```

---

## 💬 Notes

- All embeddings and TF-IDF are computed **offline**, with no internet required.
- All NLP models (E5 and MiniLM) are stored in the `models/` directory.
- OCR is automatically triggered for scanned PDFs or images.
- Designed for **50,000+ documents**, uses multiprocessing for speed.

---

## 👨‍💼 Created For

This project is specifically tailored for **DRDO scientists** and internal document triage teams to:
- Pre-process large volumes of sensitive data
- Apply semi-automated weak classification
- Cluster and explore large-scale unclassified documents
- Assist manual review by ranking, grouping, and organizing critical files

---

## ✅ Final Note

This project is fully tested, end-to-end integrated, offline compatible, and ready for real-world deployment in DRDO systems.