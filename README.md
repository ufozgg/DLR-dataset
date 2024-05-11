# DLR-dataset

DLR-dataset is an open-source dataset based on the judicial information retrieval dataset for the Chinese legal system, LeCaRD~\cite{ma2021lecard}. 

## 1. Base Dataset

The base dataset is in Chinese and includes 107 criminal cases, each providing 100 reference judgments as candidate judgments. In this dataset, the candidate judgments all come from the judgments published on the Judgement Document Network, and the query cases are extracted from the "judgment analysis process" paragraph in the judgments published on the Judgement Document Network, where the court's findings are recognized.

## 2. Dataset Structure

The structure of our dataset is as follows. Extended information includes the full text of the judgment document, the prediction result of the case, and the vector from BERT encoding, etc.

```
Dataset
├── Query Case 1
│   ├── Query Case ID
│   ├── Query Case Text
│   ├── Case Label
│   │   ├── User 1 Sorted Case List
│   │   ├── ...
│   │   └── User 8 Sorted Case List
│   ├── Extended Information
│   └── Candidate Documents
│       ├── Document 1
│       │   ├── Three Participants' Relevance Labels for Case 1
│       │   ├── ...
│       │   ├── Three Participants' Relevance Labels for Case K
│       │   └── Extended Information
│       ├── ...
│       └── Document N
├── ...
└── Query N
```

## 3. Annotation Method

The annotation method is divided into two stages:

### 3.1 

Given the huge challenge of annotating 100 query cases, we have screened them. For each query case, we retrieve the top 30 candidate judgments with the highest relevance from 100 candidate judgments using the BM25 algorithm. Specifically, we first use THULAC (Tsinghua University Chinese Lexical Analyzer) for word segmentation, and then use the BM25 algorithm to calculate the relevance score between the query case and each candidate case. The construction of the DLR dataset is divided into two stages. First, we annotate the relevance between the query case and the potential case. Then, based on the results of the first step, we annotate the relevance of each query case-case-candidate case triplet. We invited 8 experienced legal practitioners as our annotators. They were asked to read the case description and the candidate case set (CCS) of the query case, then select the case related to the query case and rank them according to the degree of relevance (allowing ties). For example, given a candidate case set ${I_1, I_2, I_3, I_4, I_5, I_6}$, an annotator might produce a relevance ranking list similar to $I_2=I_3>I_1>I_5=I_6$, where $I_4$ is ignored because he thinks $I_4$ is not related to the query case. Each case was annotated by 8 annotators separately, their work was done independently, without communication.

### 3.2 

We only ask annotators to annotate some triplets. We only let the annotator annotate the triplet $(Q_i, I_k, d_{i,j})$ when the following three conditions are met:

- 1. The probability of observing the case in the query user's intention $P(I_k|Q_i)>0$ (annotated in step 1, i.e., at least one subject selected the case);
- 2. Document $d_{i,j}$ is relevant to query $Q_i$ (we based on the labels obtained in LeCaRD\cite{ma2021lecard});
- 3. Document $d_{i,j}$ is relevant to case $I_k$ (selected from 272 subtopics).

For condition 3, we used the same screening process as in step 1. We combined the top 5 relevant cases extracted by the case prediction model and the case set extracted by regular expressions as the cases related to document $d_{i,j}$. In the judicial field, language use is very rigorous, especially the expression of the case in criminal law is unique. Therefore, it is reasonable to match with regular expressions. In this annotation process, we recruited nine annotators, including the previous eight (plus one), all of whom are legal practitioners with rich legal knowledge. We randomly divided these nine annotators into three groups. Each group's task is to annotate all tasks related to 35 (or 36) queries (i.e., $(Q_i,*,*)$). We collect all the annotation results of the triplet $(Q_i, I_k, d_{i,j})$ from the three annotators. We asked the annotators to assign a four-level relevance label to each triplet $(Q_i, I_k, d_{i,j})$ (i.e., \textit{most relevant}, \textit{more relevant}, \textit{generally relevant}, and \textit{irrelevant}). Then, we use the median of the labels within each group as the final relevance label for the triplet $(Q_i, I_k, d_{i,j})$ in subsequent experiments.

## 4. More Details

For more details, please refer to the paper [Result Diversification for Legal Case Retrieval](https://dl.acm.org/doi/abs/10.1145/3624918.3625319).
