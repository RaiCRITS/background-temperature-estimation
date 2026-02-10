# Background Temperature ($T_{bg}$) Estimation for LLMs
This repository contains jupyter notebooks showing the estimation protocol described in the paper: [Introducing Background Temperature to Characterise Hidden Randomness in Large Language Models](https://openreview.net/forum?id=bz0he4bARF). 

All the details about the protocol implemented in this repository and additional analysis can be found in the aforementioned paper.

## Repository Structure and Usage 
The estimation workflow is divided into three sequential steps. While the notebooks use specific models (SmolLM, Llama, Mistral) and metrics as a concrete example of the paper's framework, the protocol is fully extensible to other models and datasets.
1. **Generate Reference Distributions** (`1_create_references.ipynb`) This notebook collects outputs from idealized, locally run reference models over a fine-grained grid of temperatures (e.g., 0.0 to 1.0).
   - Usage: Run this first to generate the `references.json` baseline.
   - Current Setup: Uses SmolLM3-3B, Llama-3.2-3B-Instruct, and Mistral-7B-Instruct-v0.3 on the first 3o questions of the TruthfulQA dataset (validation split).
2. **Collect System Under Test Data** (`2_answers_*.ipynb`)These notebooks collect $N$ independent generations from the target model (System Under Test) at nominal temperature $T=0$.
   - Usage: Run the notebook corresponding to your provider (or adapt one) to generate answers and save in a json file.
   - Examples Included: `2_answers_claudesonnet4.ipynb` (claude-sonnet-4 via AWS Bedrock); `2_answers_gpt41nano.ipynb` (gpt-4.1-nano via Azure); `2_answers_grok3mini.ipynb` (grok-3-mini via Azure OpenAI).
3. **Estimate $T_{bg}$** (`3_estimate_Tbg.ipynb`) This notebook calculates the estimate for the Background Temperature by comparing the variability of each system under test considered against the reference distributions.
   - Usage: Update the tested_dict variable with your JSON files and run the cells. The output is a CSV containing the $T_{bg}$ estimates.
   - Methodology: It computes variability metrics on the answers and finds the reference temperature that minimizes the Kolmogorov-Smirnov (K-S) distance to the target system.
   - Metrics Included: *MEMF*: Maximum Exact Match Fraction; *AvgLev*: Average Normalized Levenshtein Distance; *AvgLCS*: Average Longest Common Subsequence Distance.

**Note on Extensibility**: The variability metrics, reference models, sstems tested and prompts provided are examples following the paper's experiments. You are encouraged to enrich the analysis by testing additional models, enriching the reference models set and adding custom metrics (e.g., semantic similarity) or using domain-specific prompt sets.

## Citation
If you use this repository or the $T_{bg}$ methodology, please cite:
```
@article{
anonymous2025introducing,
title={Introducing Background Temperature to Characterise Hidden Randomness in Large Language Models},
author={Anonymous},
journal={Accepted to Transactions on Machine Learning Research},
year={2025},
url={https://openreview.net/forum?id=bz0he4bARF},
note={Under review}
}
```
