# Certifying Counterfactual Bias in Large Language Models

## Introduction
LLMCert-B is a novel framework for certifying counterfactual bias in large language models (LLMs). Formal certification differs from benchmarking due to the following reasons - 
1. **Limited test cases**. Benchmarking consists of evaluating several but limited number of test cases. Due to its enumerative nature, benchmarking can not scale to prohibitively large numbers of prompts that can elicit bias from LLMs. Adversarial attacks identify only few worst-case examples, which do not inform about the overall biases from large input sets. Quantitative certification, on the other hand, gives information about LLM behaviors on specifications over prohibitively large number of inputs.
2. **Test set leakage**. LLMs may have been trained on popular benchmarking datasets, thus resulting in incorrect evaluation. Certification is over *distributions of input prompts*, having large sample spaces, which are prohibitive to train on.
3. **Formal guarantees**. Benchmarking involves empirical estimation without any formal guarantees of generalization over any input sets. Similarly, adversarial attacks give limited insights as they can show existence of problematic behaviors on individual inputs but do not quantify the risk of biased LLM responses. Certification, on the other hand, gives *formal probabilistic guarantees* on LLM behaviors.

Next, we summarize the contributions of our framework LLMCert-B.
1. We design *novel specifications* that quantify the desirable relational property of low counterfactual bias in LLM responses over counterfactual prompts in a specified distribution. We illustrate such specifications with distributions of counterfactual prompt sets constructed with potentially adversarial prefixes. The prefixes are drawn from 3 distributions — (1) random, (2) mixture of jailbreaks, and (3) jailbreak perturbations in the embedding space.
2. We develop the *first probabilistic black-box certifier* LLMCert-B, applicable to both open and closed-source models, for quantifying counterfactual bias in LLM responses. LLMCertB leverages confidence intervals to generate high-confidence bounds on the probability of obtaining unbiased responses from the target LLM, given any random set of counterfactual prompts from the distribution given in the specification.
3. We find that the safety alignment of SOTA LLMs is easily circumvented with several prefixes in the distributions given in our specifications, especially those involving mixture of jailbreaks and jailbreak perturbations in the embedding space (§ 5). These distributions are inexpensive to sample from, but can effectively bring out biased behaviors from SOTA models. This shows the existence of simple, bias-provoking distributions for which no defenses exist currently. We provide quantitative measures for the fairness (lack of bias) of SOTA LLMs, which hold with high confidence. We find that there are no consistent trends in the fairness of models with the scaling of their sizes, hence suggesting that the quality of alignment techniques could be a more important factor than size for fairness.

The below figure summarizes the execution of LLMCert-B on an illustrative example.
![Overview of LLMCert-B](overview.png "Overview of LLMCert-B")

## Setup
To setup LLMCert-B, please set up a conda environment with the following command:
```conda env create -f environment.yml```

Then, activate the environment with the following command:
```conda activate LLMCert-B```

Add the API keys for closed-source models in the file ```api_keys.py```.
Make a folder to store the certification results as ```mkdir results/```.

## Certifying supported models
We currently support the following models. We are working towards extending to models. 
- Open-source models (from Huggingface):
  - Vicuna
  - Llama-2
  - Mistral
- Closed-source models (with API access):
  - Gemini-Pro
  - GPT
  - Claude

To certify an open-source model for the BOLD dataset, run the following command, by replacing the placeholders with the appropriate values:
```python certification/main_hf_llms_bold.py <expt_name> <expt_mode> <model_name>```
The arguments to the above command are described next:
- ```<expt_name>```: Name of the experiment (to name the result files appropriately)
- ```<expt_mode>```: Indicates the prefix distribution wrt which certification is to be done. Possible values are: ```common jb``` or ```unknown jb``` or ```soft jb``` (```soft jb``` is only for the open-source models)
- ```<model_name>```: Name of the model to be certified. Use the official names of the models, as given in the Huggingface model hub (for open-source models) or the websites of the API models to query them. 

The settings of the certification experiments can be modified by varying the Python script invoked for certification. 
The scripts are named as ```main_<type of LLM>_<dataset>```. The ```<type of LLM>``` for open-source, Huggingface models is ```hf_llms``` and for closed-source models is ```api_llms```. The ```<dataset>``` can be ```bold``` for the BOLD dataset or ```dt``` for the Decoding Trust's stereotype dataset. The arguments to the certification scripts remain the same as described above.

We validate our bias detector for the specifications from BOLD with a human study on Amazon Mechanical Turk. ```mturk_expt_files/``` contains the files for the experiment, including the HTML file that renders the instructions to annotate bias to the human evaluators on Mechanical Turk. 
