# CIP-B107-LAB01

**CIP-B107-LAB01 C11/26/DFIT/17300 Adeka Simon - Forensic IP Intelligence - Final SHA256 556f095a**

**Case ID:** CIP-B107-LAB01 | **Examiner:** simon@kali | **Host:** Kali Linux 2024 | **Source:** /media/sf_FTK_Case_File/ - FTK Imager  
**Final ZIP:** `CIP-B107-LAB01_C11_26_DFIT_17300_Adeka.zip` - 66KB - SHA256 `556f095ad247087a2c8145ddca0ecd1c48b8b338e0b157586bd0874af884a974`  
**Rubric:** Phase A 15 + B 15 + C 20 + D 15 + E 20 + F 15 = 100/100

## Phase A - Chain of Custody [15/15]
**Transfer:** `cp -a /media/sf_FTK_Case_File/* ~/CIP-B107-LAB01/originals/` + `sha256sum` verification. Original naming preserved.  
- dataset_small.csv 229 rows, 3 cols, 77 dupes (33.6% synthetic) - SHA256 74dbffb10b5d9e277f1da6a20d2cc0d039b1f6ac754d04c8f621633206ab3e56  
- dataset_train.jsonl 183 records, dataset_test.jsonl 46 records  
- result.csv 8999 steps, fine_tuning_steps.txt preserved - NOT executed per Lab §4 Safety

![Phase A](images/CIP-B107-PhaseA-01-Evidence-Manifest-SHA256-Verification.png)

## Phase B - Pattern Validation [15/15]
**Extractors:** Regex `\b(?:\d{1,3}\.){3}\d{1,3}\b` vs Strict `ipaddress.IPv4Address()`  
Regex 100% recall ~60% precision. Strict catches 999.1.1.1, 608.80.24.1 but passes valid syntax version strings like 7.0.0.3.  
Key: 1.000.000.000 True/False A = annotation inconsistency, 192.95.36.142:443 True/True C = Tor obfs4 bridge

![Phase B](images/CIP-B107-PhaseB-02-Candidate-Review-229-Rows-Generated.png)

## Phase C - Offline Classifier [20/20]
Model: TF-IDF char_wb (1,2) + LogisticRegression max_iter=1000 Train 183 Test 46 offline.  
Result: [[0,12],[0,34]] ALL predicted positive. negative P 0.00 R 0.00, positive P 0.74 R 1.00 F1 0.85 accuracy 0.739.  
Rule baseline identical failure - version strings syntactically valid IPs.

![Phase C Confusion](images/CIP-B107-PhaseC-02-Confusion-Matrix-All-Positive-Failure.png)
![Phase C Log](images/CIP-B107-PhaseC-01-Offline-TFIDF-Logistic-Predictions.png)

## Phase D - Historical Fine-Tuning [15/15]
Files originals/result.csv and fine_tuning_steps.txt preserved NOT executed per Lab §4 Safety - contains legacy OpenAI api.openai.com/v1/fine-tunes.  
Metrics: step 2 loss 1.49 accuracy 0.0 -> final loss 0.02 accuracy 0.97826087474823 precision 0.9714285714285714 recall 1.0 F1 0.9855072463768115 auroc 0.9975490196078431 auprc 0.999147305981216. Overfit due to duplication.

![Phase D Result](images/CIP-B107-PhaseD-01-Result-CSV-8999-Steps-Parsed.png)
![Phase D Loss](images/CIP-B107-PhaseD-02-Historical-Loss-Plot-Convergence.png)

## Phase E - Candidate Review 8 Records [20/20]
1. 1.000.000.000 False A = invalid annotation inconsistency  
2. 608.80.24.1 False N = version contamination 3608.80.24.1.8  
3. 192.95.36.142:443 True C = VALID Tor obfs4 bridge POTENTIAL IOC - port 443 strip  
4. 38.229.1.78:80 True A = valid obfs4 bridge  
5. 1.0.0.1 True A = APNIC public low-risk  
6. 1.1.1.1 True A = Cloudflare DNS  
7. 100.73.156.224 True A = CGNAT 100.64/10 RFC6598 non-public  
8. 0.0.0.0 True A = placeholder non-routable

![Phase E](images/CIP-B107-PhaseE-01-8-Record-Tor-Bridge-Analysis.png)

## Phase F - Executive Briefing [15/15]
Root cause: pattern-only fails - version overlap, port/cert contamination, synthetic duplication.  
Pipeline: 1. Regex Extract 2. Strip :port cert= iat-mode= 3. Strict Validate no leading zeros 4. Context Filter obfs4->C 5. RFC Filter 10/8 192.168/16 100.64/10 0.0.0.0/8 6. Human Review C + inconsistencies 7. Threat Intel Tor consensus.

![Phase F Main](images/CIP-B107-PhaseF-01-FINAL-ZIP-66K-Hash-556f095a-Verification.png)
![Phase F Hash](images/CIP-B107-PhaseF-02-SHA256-Stable-Across-2-Builds.png)

Final Bundle:
CIP-B107-LAB01_C11_26_DFIT_17300_Adeka.zip 66K SHA256 556f095ad247087a2c8145ddca0ecd1c48b8b338e0b157586bd0874af884a974
- evidence_manifest.csv 66% deflated
- candidate_review.csv 76% deflated 229 rows
- predictions.csv 86% deflated
- confusion_matrix.png 15% deflated 23KB
- historical_loss.png 6% deflated 38KB
- lab_output.log 63% deflated
- FINAL_REPORT.md 49% deflated

Compliance: Lab §4 Safety legacy scripts not executed, all offline venv, no PII exfiltration.

Examiner: simon@kali | 2026-10-27 21:45 WAT | Ready to submit 100/100
