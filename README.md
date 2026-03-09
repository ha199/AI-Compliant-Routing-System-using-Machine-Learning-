
# 1. Project Overview

The **Complaint Auto-Routing System** is an AI/ML pipeline that automatically processes citizen complaints submitted as **text, audio, or video**, and performs four tasks:

1. Assign the complaint to the most suitable officer.
2. Predict the complaint priority (High / Medium / Low).
3. Estimate the time required to resolve the complaint.
4. Retrieve similar past complaints.

The system uses **machine learning models, semantic embeddings, and vector search** to make these predictions.

---

# 2. System Architecture

Explain this in GitHub.

```
User Complaint (Text / Audio / Video)
           │
           ▼
Multimodal Processing
(Audio → Speech-to-text)
(Video → Audio + Text extraction)
           │
           ▼
Text Preprocessing
(cleaning, normalization)
           │
           ▼
Embedding Generation
(Sentence Transformer)
           │
           ├── Officer Routing
           ├── Priority Classification
           ├── ETA Prediction
           └── Similar Complaint Search
```

Key idea:

The system converts complaints into **numerical vectors (embeddings)** and then uses ML models to make decisions.

---

# 3. Data Schema Design

Since no dataset was provided, the system defines schemas for **officers** and **complaints**.

## Officer Schema

Fields:

| Field      | Description            |
| ---------- | ---------------------- |
| officer_id | unique officer ID      |
| name       | officer name           |
| department | responsible department |
| skills     | officer expertise      |
| experience | years of experience    |
| region     | service location       |

Example:

```
{
 "id":1,
 "name":"Raj Sharma",
 "department":"water",
 "skills":"pipe leakage water supply plumbing",
 "experience":8,
 "region":"zone1"
}
```

These skills are used for **semantic matching**.

---

## Complaint Schema

Fields:

| Field           | Description                |
| --------------- | -------------------------- |
| complaint_id    | unique complaint ID        |
| text            | complaint description      |
| department      | expected department        |
| priority        | priority label             |
| resolution_days | historical resolution time |
| region          | complaint location         |

Example:

```
{
 "complaint_id":101,
 "text":"water pipe burst near hospital",
 "department":"water",
 "priority":"high",
 "resolution_days":3
}
```

---

# 4. Synthetic Dataset Generation

Because the assignment does not provide data, a **synthetic dataset** is created.

Purpose:

* simulate real complaints
* allow training ML models
* enable evaluation

Example complaints:

```
water pipe burst near hospital
electricity transformer failure
large pothole on highway
garbage not collected for days
```

Each complaint is assigned:

* department
* priority
* resolution time

This dataset is used for **training and testing the models**.

---

# 5. Multilingual Text Embedding

Text complaints are converted into **vector representations** using a transformer embedding model.

Tool used: SentenceTransformers.

Why embeddings?

Embeddings capture **semantic meaning** of text.

Example:

```
"water pipeline leakage"
"pipe burst in water supply"
```

Both produce **similar vectors**, allowing the system to detect similar complaints.

Embedding dimension:

```
384-dimensional vector
```

---

# 6. Priority Prediction Model

This module predicts the **urgency level** of a complaint.

Classes:

```
High
Medium
Low
```

Example:

| Complaint                | Priority |
| ------------------------ | -------- |
| Transformer exploded     | High     |
| Garbage collection delay | Medium   |
| Street light flickering  | Low      |

Model used:

```
RandomForestClassifier
```

Input features:

```
Complaint embeddings
```

Evaluation metrics:

* Accuracy
* Precision
* Recall
* F1 score

These metrics measure how well the classifier predicts priority levels.

---

# 7. ETA Prediction Model

The ETA model predicts **how many days it will take to resolve the complaint**.

This is a **regression problem**.

Example predictions:

| Complaint            | Predicted Days |
| -------------------- | -------------- |
| Water pipeline burst | 3 days         |
| Road pothole         | 5 days         |
| Garbage pickup       | 2 days         |

Model used:

```
RandomForestRegressor
```

Evaluation metric:

```
MAE (Mean Absolute Error)
```

MAE measures the average difference between predicted and actual resolution time.

---

# 8. Officer Routing System

Officer routing determines **which officer should handle the complaint**.

Two approaches were implemented:

### Department Classification

The model predicts the department responsible for the complaint.

Example:

```
Complaint: "electric transformer failure"

Predicted department → electricity
```

### Officer Selection

From that department, an officer is selected based on availability.

Example:

```
Electricity Department Officers:
- Amit Verma
- Rahul Singh

System assigns: Amit Verma
```

This ensures the complaint is routed to the **correct department expert**.

---

# 9. Similar Complaint Search

To help officers quickly resolve issues, the system retrieves **similar past complaints**.

This is implemented using vector search with FAISS.

Process:

```
New complaint
      │
      ▼
Generate embedding
      │
      ▼
Search FAISS index
      │
      ▼
Return top-k similar complaints
```

Example:

Query:

```
"water pipeline leaking"
```

Results:

```
water pipe burst near hospital
water leakage from main supply
```

---

# 10. Hybrid Search (Advanced Feature)

To improve retrieval accuracy, the system combines:

1. **Semantic search (vector similarity)**
2. **Keyword search using BM25**

Semantic search captures meaning, while keyword search captures exact terms.

Example:

Query:

```
transformer failure
```

Hybrid search ensures complaints containing **transformer** are also retrieved even if embeddings differ slightly.

---

# 11. Audio Complaint Processing

Citizens may submit complaints via audio recordings.

The system converts audio to text using the open-source model
OpenAI Whisper.

Pipeline:

```
Audio complaint
      │
      ▼
Speech-to-text
      │
      ▼
Text processing pipeline
```

This allows the system to handle **voice complaints**.

---

# 12. Video Complaint Processing

Video complaints are processed by extracting:

1. Audio → speech-to-text
2. Key frames → possible text detection

Steps:

```
Video
   │
   ├ extract audio
   └ extract frames
```

Extracted text is then sent through the same NLP pipeline.

---

# 13. Inference Pipeline

The inference pipeline performs real-time predictions.

Steps:

```
User submits complaint
        │
        ▼
Embedding generation
        │
        ├ Priority prediction
        ├ ETA estimation
        ├ Officer routing
        └ Similar complaint search
```

Example output:

```
Complaint: water pipeline leaking near school

Assigned Officer: Raj Sharma
Department: Water

Priority: High

Estimated Resolution Time: 3 days

Similar Complaints:
1. water pipe burst near hospital
2. main pipeline leakage
```

---

# 14. Evaluation Framework

The system is evaluated using standard ML metrics.

Priority model:

```
Accuracy
Precision
Recall
F1 Score
```

ETA regression:

```
Mean Absolute Error (MAE)
```

Routing model:

```
Classification accuracy
```

Similarity search:

```
Top-K retrieval results
```

These metrics help measure **model performance and reliability**.

---

# 15. Repository Structure

Explain this clearly on GitHub.

```
complaint-routing-ai
│
├ data
│
├ models
│
├ training
│   train_priority.py
│   train_eta.py
│
├ retrieval
│   faiss_index.py
│   hybrid_search.py
│
├ routing
│   officer_router.py
│
├ multimodal
│   speech_processing.py
│   video_processing.py
│
├ inference
│   pipeline.py
│
└ README.md
