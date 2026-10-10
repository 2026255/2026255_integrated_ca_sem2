# Evaluation Protocol Choice Outweighs Neural Architecture Choice in Network Intrusion Detection

Integrated CA1, MSc in Data Analytics, CCT College Dublin
Modules: Advanced Data Analytics and Big Data Storage and Processing
Student ID 2026255

## Research question

Does the choice of evaluation protocol affect reported NIDS performance more than
the choice of neural architecture?

## Summary

Three neural architectures were trained across three evaluation protocols on the
full CSE-CIC-IDS2018 corpus of 16,233,002 network flows, processed through an
Apache Spark lakehouse on a machine with four cores and 7.9 GB of memory.

Among the two supervised architectures the protocol effect on binary F1 was
0.0384 against an architecture effect of 0.0181, a ratio of 2.1, with a seed
standard deviation of 0.0027.

## Key findings

| Finding | Value |
|---|---|
| Protocol effect against architecture effect | 2.1x |
| Exact duplicate records in the corpus | 4,243,106 (26.14%) |
| Single node pandas ceiling | 5,245,725 of 16,233,002 rows (32.3%) |
| Leaky features in permutation importance | ranked 2nd and 3rd of 79 |
| False positive rate, naive to corrected protocol | 0.084 to 0.023 |
| Unseen attack family recall, MLP | 0.9718 to 0.4956 |
| Parquet against CSV footprint | 2.39 GB against 6.41 GB |

Four defects in the dataset and processing chain were found to fail silently,
producing plausible results with no error raised.

## Repository layout

```
notebooks/
  01_BigData.ipynb               Spark lakehouse, bronze to gold, storage and
                                 query measurements, protocol table construction
  02_models_experiments.ipynb    MLP, 1D CNN and autoencoder across three
                                 protocols, permutation importance, zero day test
results/                         Measurement output as JSON
Research Papers/                 The 14 cited papers in PDF
2026255_Integrated_CA.docx       Report
Appendix.docx                    AI declaration, Appendix A and B
```

## Not in this repository

`datasets/` and `lakehouse/` are excluded. The raw corpus is 6.41 GB and the
derived lakehouse is 4.6 GB, both beyond what GitHub accepts. The datasets are
supplied through the Google Drive link given in the report, as the assessment
brief requires.

The raw data can be fetched directly from the Canadian Institute for
Cybersecurity:

```
aws s3 sync --no-sign-request --region ca-central-1 \
  "s3://cse-cic-ids2018/Processed Traffic Data for ML Algorithms/" ./datasets
```

## Reproducing

Requires Python 3.10, JDK 17, PySpark 3.5.9 and PyTorch 2.13 CPU. On Windows,
Spark also needs `winutils.exe` and `hadoop.dll` with `HADOOP_HOME` set, and
`PYSPARK_PYTHON` pointing at the interpreter.

```
py -3.10 -m pip install pyspark==3.5.9 pyarrow torch pandas scikit-learn psutil
```

Run `01_BigData.ipynb` first. It builds the lakehouse and writes the three
protocol tables that `02_models_experiments.ipynb` consumes. The first notebook
takes roughly an hour on four cores. The second takes about two hours for the
full grid at three seeds per cell, or around forty minutes at one seed.

## Dataset

CSE-CIC-IDS2018, Communications Security Establishment and the Canadian Institute
for Cybersecurity. Ten capture days, 16,233,002 flows, 80 CICFlowMeter features,
15 class labels.

Sharafaldin, I., Lashkari, A.H. and Ghorbani, A.A. (2018) 'Toward generating a
new intrusion detection dataset and intrusion traffic characterization', in
*Proceedings of the 4th International Conference on Information Systems Security
and Privacy (ICISSP)*. SCITEPRESS, pp. 108-116.
