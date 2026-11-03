# BCU Computing Society - ML in Cybersecurity

## Can AI Detect a Suspicious Login?

**Wednesday, 4 November 2026 · 14:00–16:00 · Room C221**  
**Level:** Beginner-friendly · **Practical:** Google Colab · **Data:** Fictional/synthetic only

This workshop introduces machine learning through a defensive security use case. You will train a small model to classify fictional login attempts as *normal* or *suspicious*, inspect how it makes decisions, and test where it gets things wrong.

> **The important lesson:** a model's suspicious prediction is a risk signal, not proof of an attacker. The model does not understand intent, and it should not be trusted blindly.

---

## Start here

1. Open the [Google Colab notebook](https://colab.research.google.com/github/IvzCyberSec/ML_in_Cybersecurity_Workshop/blob/main/ML_in_Cybersecurity_Suspicious_Login_Detector.ipynb).
2. Select **Runtime → Run all** to execute the guided notebook from top to bottom, or run each cell in order with the play button.
3. Read each short explanation before running its code cell. Stop at the discussion prompts and predict what you think will happen.
4. Complete the scenario tests and the final challenge: create a fictional login that causes a surprising prediction.

You need a laptop, internet access and a Google account for Colab. No local Python installation is required.

## What are AI and machine learning?

**Artificial Intelligence (AI)** is a broad area of computing concerned with systems that perform tasks we associate with intelligent behaviour. It does not mean every AI system thinks like a person.

**Machine Learning (ML)** is one approach within AI. Instead of writing every decision as a fixed rule, we give an algorithm examples and let it find statistical patterns in those examples. The trained model then uses the patterns it learned to make predictions about new inputs.

A useful mental model is:

```text
EXAMPLES + CORRECT/KNOWN OUTCOMES
              ↓
         TRAINING
              ↓
       TRAINED MODEL
              ↓
     NEW, UNSEEN EXAMPLE
              ↓
         PREDICTION
              ↓
      HUMAN / SYSTEM ACTION
```

The model is not searching for truth or reasoning like a human. Its output depends on the data, labels, features, algorithm and training process it was given. A model can be confidently wrong.

### AI is bigger than chatbots

An LLM chatbot is one type of AI system: it generates text. This workshop uses a different kind of system: a **classifier**. A classifier receives structured information and predicts a category. Here, the categories are `normal` and `suspicious`.

A **cybersecurity analysis agent** is a wider workflow. It might collect logs, extract features, call one or more detection models, correlate findings with other evidence, and recommend an action to an analyst. The ML model is one analytical component inside that workflow. Our notebook does not create a full agent: it takes one prepared login record and returns a category.

Other ML uses in cybersecurity include:

- **Network anomaly detection:** flagging traffic patterns that differ from a learned baseline.
- **Spam and phishing classification:** classifying messages or URLs using characteristics learned from labelled examples.
- **Malware classification:** identifying files from static or behavioural features.
- **Endpoint monitoring:** identifying unusual process or user activity.
- **Fraud and account-takeover detection:** prioritising activity that resembles previously observed abuse.

These systems support analysts; they do not automatically prove malicious intent.

## The key ML ideas

| Idea | Meaning in this workshop |
|---|---|
| Dataset | A collection of example login attempts. Each row is one example. |
| Feature | An input signal about an example, such as failed attempts or whether the device is new. |
| Label | The expected category attached to an example: normal (`0`) or suspicious (`1`). |
| Training | Showing the algorithm examples and labels so it can fit a model. |
| Model | The learned decision structure used to make predictions. |
| Prediction | The category the model assigns to a new login attempt. |
| Testing | Checking predictions against examples kept aside from training. |
| Generalisation | How well the model performs on new examples, not just examples it has already seen. |
| Overfitting | When a model learns quirks of its training examples and performs poorly on new data. |
| False positive | A normal login incorrectly flagged as suspicious. |
| False negative | A suspicious-labelled login incorrectly classified as normal. |
| Bias / data quality | The model can inherit gaps, skew or mistakes in the examples and labels it learns from. |

### Supervised learning

This notebook demonstrates **supervised learning**: each training example has a label. The model learns the relationship between the features (`X`) and the target label (`y`). The labels are the answer key it is being trained to reproduce.

A key point: the quality of the labels matters. In this lab, the labels are created using an artificial scoring rule. The model is learning to approximate that rule, not learning from confirmed attacks.

### The model: a Decision Tree

A Decision Tree makes a prediction by following a series of questions, such as:

```text
Are there many recent failed attempts?
       /                \\
     YES                NO
      ↓                  ↓
Check other signals   Is the device new?
                         ...
```

The fitted tree chooses feature thresholds from the training data. We limit its depth so the tree is easier to inspect and less likely to memorise every training example. This is a classification model, not an LLM and not an autonomous security agent.

## What the notebook does

1. Imports `pandas`, `NumPy`, `Matplotlib` and `scikit-learn`.
2. Generates 1,000 fictional login records with six features.
3. Assigns synthetic labels using a simple risk-points rule.
4. Inspects the dataset and separates features (`X`) from labels (`y`).
5. Splits examples into 80% training and 20% testing data.
6. Trains a Decision Tree classifier.
7. Evaluates it using accuracy, precision, recall and a confusion matrix.
8. Visualises the tree and its feature importance.
9. Tests normal, suspicious and ambiguous fictional logins.
10. Challenges you to find a case where the model's prediction is surprising.

## Understand the login data

Each row represents one fictional login attempt. The features are:

| Feature | What it represents |
|---|---|
| `login_hour` | Hour of login, from 0 to 23. |
| `failed_attempts` | Recent incorrect password attempts. |
| `new_device` | `1` means a new device; `0` means recognised. |
| `distance_from_home_km` | Artificial distance from a usual location. |
| `vpn_used` | `1` means VPN used; `0` means not used. |
| `attempts_last_10_minutes` | Recent login attempt frequency. |
| `suspicious` | Synthetic target label: `0` normal, `1` suspicious. |

The notebook first generates random feature values. It then applies a manually authored points rule to create the `suspicious` label. That means the labels are not independently verified ground truth. The model is trained to reproduce the rule's decisions from the six features.

**Important limitation:** VPN use, travel, a new device or a late-night login are not proof of malicious behaviour. They may be legitimate. Real systems need richer context and verified outcomes.

## Understand the evaluation

- **Accuracy:** the proportion of test examples classified correctly. It can hide which type of error the model makes.
- **Precision:** when the model flags a login as suspicious, how often is that prediction correct?
- **Recall:** of all examples labelled suspicious, how many did the model identify?
- **False positive:** a normal login is flagged. Too many can create alert fatigue and frustrate users.
- **False negative:** a suspicious-labelled login is missed. This can allow risky activity to pass unnoticed.

The confusion matrix displays these outcomes. In security, the balance between false positives and false negatives matters; the best threshold depends on the system, context and cost of each error.

### Baseline result from the supplied notebook

With its fixed random seed and supplied settings, the notebook currently produces 56.1% suspicious labels and obtains 89.5% accuracy on its 200-example test split. Its confusion matrix contains 73 true negatives, 15 false positives, 6 false negatives and 106 true positives.

These figures are a reproducible classroom baseline, **not evidence of real-world detection performance**. Because the test labels come from the same artificial rule as the training labels, this measures how well the model approximates that rule.

## Your challenge

Create a fictional scenario that:

- looks suspicious to you but is classified as normal; or
- looks ordinary to you but is classified as suspicious.

Then discuss:

1. Which features influenced the result?
2. What context is missing from the dataset?
3. Could the login be legitimate?
4. Is the model's result a false positive or false negative relative to the lab label?
5. What response is proportionate: allow and monitor, request MFA, notify an analyst, or block? Explain why.

Do not use real accounts, passwords, IP addresses, personal details or locations.


## How to start your own ML project

The same thinking applies beyond login detection. Start with the problem, not with a complicated algorithm:

1. **Define the task.** What should the system output: a category (classification), a number (regression), or groups/unusual patterns (clustering or anomaly detection)?
2. **Find suitable data.** Use data that is relevant, permitted, and representative of the environment where the model will be used.
3. **Decide what counts as an example and a feature.** Keep only information that is useful and appropriate. Consider privacy and bias.
4. **Get reliable labels when using supervised learning.** The model learns the labels you provide, including mistakes in them.
5. **Create a baseline.** Start with a simple model you can explain before reaching for a complex neural network.
6. **Keep test data separate.** Evaluate on examples the model did not train on.
7. **Inspect the mistakes.** Choose metrics based on the real cost of false alarms and missed detections.
8. **Plan human oversight and monitoring.** Data and behaviour can change. Re-evaluate before relying on the model in a real system.

The notebook demonstrates one specific route: **supervised classification with a Decision Tree**. It is a starting pattern, not the only kind of ML.

## Using AI responsibly during the practical

AI is available as a **pair-programming assistant**, not as the model that detects the login. Ask it to explain one code cell, interpret an error or suggest fictional test cases. Read and verify any code it suggests before running it. Never paste secrets, real log data or personal information into an AI assistant.

Example prompt:

> I am a beginner using pandas and scikit-learn in Google Colab. Explain this code cell line by line, define any ML terms, and tell me what data goes in and what comes out. Do not rewrite the code.

## Limitations and safe use

- Synthetic data only; no real passwords, accounts, IP addresses or locations.
- Do not connect this notebook to a real authentication service.
- A suspicious prediction is a risk signal, not proof of compromise or attacker identity.
- Do not treat VPN use, travel, a new device or unusual login time as proof of malicious behaviour.
- This is an educational prototype, not a production detection system.
- Real security decisions require layered controls such as MFA, rate limiting, alerting, contextual analysis and human review.

## Further learning

- [Google Teachable Machine](https://teachablemachine.withgoogle.com/) — an interactive way to experience gathering examples, training a model and testing it.
- [Code.org: “AI: What is Machine Learning?”](https://code.org/en-US/curriculum/how-artificial-intelligence-works) — a beginner-friendly video in its “How AI Works” series; the same page includes a separate video on training data and bias.
- [Google Teachable Machine](https://teachablemachine.withgoogle.com/) — an interactive gather → train → test activity. Students can see how changing the examples affects classification.
- [Google Cloud: What is Machine Learning?](https://cloud.google.com/learn/what-is-machine-learning) — a clear written overview of AI, ML, supervised learning and unsupervised learning.
- [scikit-learn Decision Tree documentation](https://scikit-learn.org/stable/modules/tree.html) — reference for the classifier used in the notebook.
- [GreenCode videos](https://greencode.ai/videos/) — optional wider context on energy-efficient computing. These videos focus on decarbonising computing, not on explaining the fundamentals of ML, so they are not the core teaching resource.

## Workshop schedule

| Time | Activity |
|---|---|
| 14:00–14:10 | Welcome and introduction |
| 14:10–14:20 | Explain the challenge and show the suspicious-login scenario |
| 14:20–15:20 | Guided Google Colab practical |
| 15:20–15:35 | Audience challenge: confuse the model |
| 15:35–15:50 | Questions, discussion and recap |
| 15:50–16:00 | Feedback and close |

---

**BCU Computing Society · Cybersecurity Workshop**
