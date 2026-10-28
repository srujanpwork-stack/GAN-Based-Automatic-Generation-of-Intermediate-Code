# GAN-Based Automatic Generation of Intermediate Code

**Course:** Compiler Design Lab (CSE3024)  
**Project:** GAN-Based Automatic Generation of Intermediate Code from High-Level Programs

## Overview

This repository contains a lab-scale prototype exploring whether a small neural sequence model can generate **three-address code (TAC)** from a restricted C-like source language. The notebook demonstrates both a conventional compiler-style pipeline and an initial machine-learning experiment.

The project is intended for learning and experimentation. It is **not a complete C compiler**, and its neural model should not be assumed to produce correct or semantically equivalent code for arbitrary inputs.

## Repository contents

- `GAN_Intermediate_Code_Generation.ipynb` — main executable notebook: tokenizer, parser, abstract syntax tree (AST), semantic checks, deterministic TAC baseline, TAC validation/execution, synthetic training pairs, PyTorch generator/discriminator experiment, and evaluation.
- `test_dataset.json` — small, human-readable set of valid and invalid programs for testing the supported language and checking expected TAC/results.
- `README.md` — project overview, setup, usage, dataset schema, and limitations.

## Features demonstrated

1. Tokenization and parsing for a small C-like language.
2. AST construction and basic declaration/use checks.
3. Rule-based generation of TAC as a deterministic baseline.
4. Basic TAC validation and execution to compare results.
5. Synthetic source/AST-summary-to-IR training pairs.
6. A compact PyTorch GRU-based conditional generator and a discriminator.
7. Evaluation using validator pass rate, exact token match, semantic agreement, and a rule-based baseline.

## Supported source-language subset

The parser currently supports:

- `int` declarations with initialization, such as `int a = 4;`
- Assignments to previously declared variables, such as `a = a + 2;`
- Integer arithmetic using `+`, `-`, `*`, and `/`
- Parentheses and standard arithmetic precedence (`*` and `/` before `+` and `-`)
- `return` statements

A program consists of statements terminated by semicolons. This is only a restricted subset of C. It does not currently support conditionals, loops, functions, arrays, pointers, floating-point types, or the full C grammar. The current semantic checker is also deliberately simple.

## Requirements

- Python 3.10+ recommended
- Jupyter Notebook or JupyterLab
- NumPy
- PyTorch

Install the Python dependencies in your environment:

```bash
python -m pip install numpy torch jupyter
```

If you are using a notebook environment such as Google Colab, install any missing dependencies in a notebook cell.

## Run the project

1. Clone or download the repository.
2. Start Jupyter from the repository directory:

   ```bash
   jupyter notebook
   ```

3. Open `GAN_Intermediate_Code_Generation.ipynb`.
4. Run the cells from top to bottom. The notebook creates its synthetic training dataset during execution, trains the model, and prints evaluation results.
5. Review the final evaluation and limitations sections. The results are computed at runtime and may vary by environment and training run.

The notebook automatically selects CUDA when PyTorch detects a compatible GPU; otherwise it runs on the CPU. A GPU is optional for this small experiment.

## Test dataset

`test_dataset.json` contains two groups:

- `valid_cases`: programs expected to parse and pass the supported semantic checks. Each entry includes the source program, expected TAC lines, and expected return value.
- `invalid_cases`: deliberately invalid programs used to check that parsing or semantic validation rejects unsupported or incorrect input. Each entry includes the expected failure category, not an exact error message.

The expected TAC strings are based on the notebook's deterministic `generate_tac()` implementation, including temporary names such as `t1` and `t2`. The file is a **test fixture**, not the notebook's generated neural-model training dataset. The notebook does not currently automatically load this JSON file; use it as a reference for manual checks or extend the notebook to iterate over the cases and compare actual results with the expected values.

Example source program:

```c
int a = 2;
int b = 3;
int out = (a + b) * 2;
return out;
```

Expected TAC:

```text
a = 2
b = 3
t1 = a + b
t2 = t1 * 2
out = t2
return out
```

## Important note about the GAN experiment

The notebook includes supervised pretraining of the generator and separate training of the discriminator. **The discriminator is not used to adversarially update the generator.** Therefore, describe this accurately as an initial GAN-style/conditional generation experiment scaffold, not as a fully trained adversarial GAN. The discriminator's output is not a proof of correctness.

## Limitations and possible improvements

- Expand the language grammar to support control flow, functions, and more expression forms.
- Increase dataset diversity and include programs beyond generated arithmetic templates.
- Add automated tests that load `test_dataset.json` and assert parser, TAC, and execution outcomes.
- Compare multiple random seeds and report measured results rather than assumed performance.
- Add a documented adversarial generator-training method if a complete GAN is a project requirement.
- Strengthen TAC validation and testing; finite execution checks are not formal verification.

## Academic use

Use this repository to demonstrate the compiler pipeline and discuss the experimental limitations transparently. Clearly separate deterministic compiler-baseline results from neural-model results in reports and presentations.
