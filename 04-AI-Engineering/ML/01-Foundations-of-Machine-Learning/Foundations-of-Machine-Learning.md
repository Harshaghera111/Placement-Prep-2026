# Machine Learning — Module 1: Foundations of Machine Learning

> **Course:** Machine Learning (24CS3503) · Semester 5  
> **Module Hours:** 09  
> **Scope:** Well-posed learning problems, Designing a learning system, Introduction to ML, Types of learning, Data preprocessing

---

## 1. Module Overview

This module lays the **entire groundwork** for everything you will study in Machine Learning. Think of it as the foundation of a building — if you skip it or understand it poorly, every advanced topic (neural networks, deep learning, NLP, etc.) will feel shaky.

**What you will learn in this module:**

1. What Machine Learning actually is and why it matters.
2. How to formally describe a learning problem (the T-E-P framework).
3. How to design and think about an ML system end-to-end.
4. The three major types of ML: Supervised, Unsupervised, and Reinforcement Learning.
5. How to prepare raw data before feeding it to a model (preprocessing).

### Module 1 Roadmap

```
PART A: Introduction to Machine Learning
  ↓
PART B: Well-Posed Learning Problems (T-E-P Framework)
  ↓
PART C: Designing a Learning System (End-to-End Workflow)
  ↓
PART D: Types of Machine Learning (Supervised / Unsupervised / Reinforcement)
  ↓
PART E: Comparing the Three Types
  ↓
PART F: Data Preprocessing (Missing Values, Imbalanced Data, Feature Engineering)
  ↓
PART G: Real-World Applications
  ↓
PART H: Revision, Definitions, Interview Questions
```

> **Tip:** Do not rush through this module. The concepts here are asked in every ML interview and every semester exam. Spend time truly *understanding* them, not just memorising definitions.

---
---

# PART A — INTRODUCTION TO MACHINE LEARNING

---

## 2. What is Machine Learning?

### 2.1 The Intuition

Imagine you are a child learning to recognise fruits. Nobody gives you a rule book that says:

- "If it is round and red, it is an apple."
- "If it is yellow and curved, it is a banana."

Instead, your parents simply **show** you many fruits and tell you their names. Over time, your brain learns to identify fruits — even ones you have never seen before.

**Machine Learning works the same way.** Instead of a human writing explicit rules, we give a computer **a lot of examples (data)** and let it **figure out the rules (patterns) on its own.**

### 2.2 Breaking Down the Key Words

| Term | Simple Meaning |
|------|----------------|
| **Machine** | A computer program |
| **Learning** | Improving at a task through experience (data) |
| **Data** | The examples or observations we provide |
| **Pattern** | A recurring relationship hidden inside the data |
| **Prediction** | Using discovered patterns to guess the answer for new, unseen data |
| **Decision-making** | Choosing an action or output based on what the model has learned |

### 2.3 A Simple Definition

> **Machine Learning** is a field of computer science where we build programs that **automatically improve their performance** at some task **by learning from data**, rather than being explicitly programmed with rules.

### 2.4 The Core Idea

```
Traditional Programming:
  Human writes rules  →  Computer follows rules  →  Output

Machine Learning:
  Computer sees data  →  Computer discovers rules  →  Computer makes predictions
```

The computer is **not hard-coded** with the answer. It **learns** the answer from examples.

### 2.5 A Concrete Example

**Problem:** Predict whether an email is spam or not spam.

- **Traditional approach:** A programmer manually writes hundreds of rules:
  - If the email contains "free money", mark as spam.
  - If the sender is unknown, mark as spam.
  - …and so on.

- **Machine Learning approach:** We give the computer **thousands of emails already labelled as spam or not spam**. The computer studies these examples and learns its own rules. When a new email arrives, it predicts: spam or not spam.

The ML approach is powerful because:
- You don't need to anticipate every possible spam pattern.
- The system can **adapt** as spam tactics change, by learning from new data.

---

## 3. Why Do We Need Machine Learning?

### 3.1 The Problem with Manual Rules

Consider writing a program to recognise handwritten digits (0–9). Every person writes digits differently. You would need to account for:

- Thick vs thin strokes
- Tilted vs straight writing
- Connected vs disconnected strokes
- Thousands of personal styles

Writing manual `if-else` rules for every possible variation is **practically impossible**. Even if you managed it, the rules would be brittle — any new handwriting style could break them.

### 3.2 When ML Becomes Useful

Machine Learning is the right tool when:

| Situation | Example |
|-----------|---------|
| Rules are too complex to write by hand | Image recognition, speech recognition |
| Rules keep changing | Spam detection (spammers change tactics) |
| The problem involves finding hidden patterns in large data | Customer purchase behaviour |
| Human expertise is hard to articulate | A doctor's intuition about diagnosis |
| The scale of data is too large for human analysis | Millions of transactions for fraud detection |

### 3.3 Real-World Motivation

- **Email filtering** — Gmail classifies billions of emails daily. No human could write rules for all of them.
- **Product recommendations** — Netflix/Amazon suggest content by learning from millions of users' behaviour.
- **Medical diagnosis** — ML models can detect diseases from X-rays that even experienced doctors might miss.
- **Self-driving cars** — No programmer could write rules for every possible road scenario.

> **Key takeaway:** We need ML when problems are too complex, too large, or too dynamic for hand-written rules.

---

## 4. Traditional Programming vs Machine Learning

This is a **frequently asked exam and interview question**. Understand it deeply.

### 4.1 Visual Comparison

```
┌─────────────────────────────────────────────┐
│         TRADITIONAL PROGRAMMING             │
│                                             │
│   Input Data  ──┐                           │
│                 ├──→  Program  ──→  Output   │
│   Rules ────────┘    (written               │
│   (written by          by human)            │
│    human)                                   │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│           MACHINE LEARNING                  │
│                                             │
│   Input Data  ──────┐                       │
│                     ├──→  Learning   ──→  Learned   ──→  New Input  ──→  Output
│   Expected Output ──┘     Algorithm        Model                      (Prediction)
│   (answers / labels)                                                    │
└─────────────────────────────────────────────┘
```

### 4.2 Side-by-Side Comparison

| Aspect | Traditional Programming | Machine Learning |
|--------|------------------------|------------------|
| **Input** | Data + Rules | Data + Expected Outputs |
| **Output** | Answers | A learned model (rules) |
| **Who writes the rules?** | Human programmer | The algorithm discovers them |
| **Adaptability** | Must manually update rules | Re-train on new data |
| **Scalability** | Hard with complex problems | Scales with more data |
| **Example** | Calculator | Spam filter |

### 4.3 Example: Email Classification

**Traditional Programming:**
```
IF email contains "lottery" AND sender is unknown THEN spam
IF email contains "meeting" AND sender is colleague THEN not spam
...
(hundreds of rules)
```

**Machine Learning:**
```
Give the algorithm 10,000 emails labelled as spam / not spam.
The algorithm learns which words, patterns, and features distinguish spam.
For any new email → the model predicts spam or not spam.
```

> **Exam tip:** Be ready to draw the two diagrams and explain the fundamental difference: in traditional programming, humans provide rules; in ML, the machine discovers rules from data.

---

## 5. Basic Machine Learning Terminology

Before going further, you **must** be crystal clear on these terms. They will appear in every lecture, every exam, and every interview.

### 5.1 Dataset

A **dataset** is a collection of data used for training or testing a machine learning model. Think of it as a **spreadsheet** where:
- Each **row** is one example (a data point).
- Each **column** is one characteristic (a feature).

**Example — House price dataset:**

| Area (sq ft) | Bedrooms | Age (years) | Location | Price (₹) |
|--------------|----------|-------------|----------|-----------|
| 1200 | 2 | 5 | Urban | 50,00,000 |
| 1800 | 3 | 10 | Suburb | 70,00,000 |
| 900 | 1 | 2 | Urban | 35,00,000 |

### 5.2 Data Point / Instance / Sample

A single row in the dataset. One example.

In the table above, the row `[1200, 2, 5, Urban, 50,00,000]` is one data point.

### 5.3 Feature (Input Variable)

A **feature** is a measurable property of the data that the model uses to make predictions.

In the house example: `Area`, `Bedrooms`, `Age`, `Location` are all **features**.

> **Analogy:** Features are like the **questions on a form** that you fill in. The model reads these answers to make its decision.

### 5.4 Label / Target (Output Variable)

The **label** (also called the **target**) is the **answer** we want the model to predict.

In the house example: `Price` is the **label**.

### 5.5 CRITICAL: Feature vs Label

This distinction trips up many beginners. Let's make it absolutely clear:

| | Feature | Label |
|--|---------|-------|
| **What is it?** | Input — what we know | Output — what we want to predict |
| **Given during training?** | Yes | Yes (in supervised learning) |
| **Given during prediction?** | Yes | No — the model must predict this |
| **Also called** | Input variable, independent variable, predictor | Target, output variable, dependent variable |

```
Features (what we know)          Label (what we predict)
┌──────────────────────┐         ┌───────────┐
│ Area, Bedrooms,      │  ───→   │  Price     │
│ Age, Location        │         │            │
└──────────────────────┘         └───────────┘
```

### 5.6 Model

A **model** is the mathematical representation that the machine learning algorithm produces after training. It captures the patterns from the data.

> **Analogy:** If the data is a textbook, the model is the **student's understanding** after studying it. The student (model) can now answer new questions.

### 5.7 Algorithm

An **algorithm** is the specific method or procedure used to learn from the data and produce a model.

- The **algorithm** is the *learning process*.
- The **model** is the *result* of that learning.

| Term | Analogy |
|------|---------|
| Algorithm | The study method a student uses |
| Model | The knowledge the student gains after studying |

**Examples of ML algorithms:** Linear Regression, Decision Trees, K-Nearest Neighbours, Neural Networks (you will learn these in later modules).

### 5.8 Training

**Training** is the process of feeding data to an algorithm so that it can learn patterns and produce a model.

> During training, the algorithm looks at features AND labels, adjusts itself, and gradually improves.

### 5.9 Testing

**Testing** is the process of evaluating the trained model on **new, unseen data** to check how well it performs.

> During testing, the model is given only features. It produces predictions, and we compare those predictions to the actual labels.

### 5.10 Prediction

A **prediction** is the output produced by a trained model when given new input features.

```
New Input Features  →  Trained Model  →  Prediction
```

### 5.11 Inference

**Inference** is the process of using a trained model to make predictions on new data. In practice, "prediction" and "inference" are often used interchangeably, but technically:

- **Training** = learning from data
- **Inference** = using the learned model on new data

### 5.12 Summary Table

| Term | One-Line Definition |
|------|---------------------|
| Dataset | Collection of data used for ML |
| Data Point | A single example / row |
| Feature | An input variable (what we know) |
| Label | The output variable (what we predict) |
| Model | Learned representation of patterns |
| Algorithm | The method used to learn |
| Training | Process of learning from data |
| Testing | Evaluating the model on unseen data |
| Prediction | Model's output for new input |
| Inference | Using the model to make predictions |

---
---

# PART B — WELL-POSED LEARNING PROBLEMS

---

## 6. Well-Posed Learning Problem

This is one of the **most important concepts** in Module 1. It is frequently asked in exams and helps you understand what it truly means for a machine to "learn."

### 6.1 What Does "Learning Problem" Mean?

Before you build any ML system, you need to answer a fundamental question:

> "What exactly is the machine supposed to learn?"

A **learning problem** is a clearly defined task where a computer program needs to improve its performance through experience.

But not every vaguely stated problem is a proper learning problem. For a problem to be well-suited for ML, it must be **well-posed** — meaning it must be stated precisely enough that we can:
1. Know what the task is.
2. Know what data (experience) the machine will learn from.
3. Know how to measure whether the machine is actually improving.

### 6.2 The T-E-P Framework

Tom Mitchell (1997) gave one of the most famous and widely cited definitions in ML:

> *"A computer program is said to **learn** from experience **E** with respect to some class of tasks **T** and performance measure **P**, if its performance at tasks in **T**, as measured by **P**, improves with experience **E**."*

Let's break this down piece by piece.

---

### 6.3 Task (T)

**What it is:** The specific job the machine learning program is supposed to do.

**How to think about it:** Ask yourself — "What do I want the program to accomplish?"

**Examples of tasks:**

| Task | Description |
|------|-------------|
| Classify emails as spam or not spam | Binary classification |
| Predict the price of a house | Regression |
| Recognise handwritten digits | Multi-class classification |
| Group customers by buying behaviour | Clustering |
| Play chess and win | Game playing |

> **Key point:** The task must be specific and measurable. "Make the computer smart" is NOT a well-defined task.

---

### 6.4 Experience (E)

**What it is:** The data or information the program learns from. This is the **training data**.

**How to think about it:** Ask yourself — "What examples or data will the machine study to learn?"

**Examples of experience:**

| Task | Experience (E) |
|------|----------------|
| Spam classification | A database of 10,000 emails labelled as spam / not spam |
| House price prediction | Historical records of house sales with features and prices |
| Digit recognition | Thousands of images of handwritten digits with correct labels |
| Chess playing | Records of past chess games (wins, losses, moves) |

> **Key point:** Without experience (data), there is no learning. The quality and quantity of experience directly affect how well the model learns.

---

### 6.5 Performance Measure (P)

**What it is:** A quantitative metric that tells us **how well** the program is performing the task.

**Why it is needed:** Without a performance measure, we have no way to know if the machine is actually "learning" (improving) or just guessing randomly.

**Examples of performance measures:**

| Task | Performance Measure (P) |
|------|------------------------|
| Spam classification | Percentage of emails correctly classified (accuracy) |
| House price prediction | Average difference between predicted and actual price (mean error) |
| Digit recognition | Percentage of digits correctly identified |
| Chess playing | Percentage of games won against opponents |

> **Key point:** The performance measure must be objective and measurable. "The model seems pretty good" is NOT a valid performance measure.

---

### 6.6 T-E-P: Putting It All Together

```
┌──────────────────────────────────────────────────────┐
│              WELL-POSED LEARNING PROBLEM             │
│                                                      │
│   T (Task)         →   What to do?                   │
│   E (Experience)   →   What to learn from?           │
│   P (Performance)  →   How to measure improvement?   │
│                                                      │
│   Learning = P improves on T with more E             │
└──────────────────────────────────────────────────────┘
```

**Memory trick:** Think of **TEP** as a **checklist**. Before starting any ML project, check:

- ✅ T — Is the task clearly defined?
- ✅ E — Do I have data to learn from?
- ✅ P — Can I measure how well the model is doing?

If any of these is missing, the problem is **not well-posed** for machine learning.

---

### 6.7 Detailed Examples

#### Example 1: Spam Detection

| Component | Description |
|-----------|-------------|
| **T** (Task) | Classify incoming emails as "spam" or "not spam" |
| **E** (Experience) | A database of 50,000 emails, each labelled as spam or not spam |
| **P** (Performance) | Percentage of emails correctly classified on a test set |

**Explanation:** The program studies thousands of previously labelled emails (E). It learns patterns like certain words, sender addresses, and formatting that distinguish spam. Its task (T) is to classify new emails. We measure success (P) by how many emails it gets right.

---

#### Example 2: House Price Prediction

| Component | Description |
|-----------|-------------|
| **T** (Task) | Predict the selling price of a house given its features |
| **E** (Experience) | Historical data of 10,000 house sales (area, bedrooms, location, price) |
| **P** (Performance) | Mean Absolute Error — the average difference between predicted and actual price |

**Explanation:** The model trains on past house sale data (E). Its task (T) is to predict the price of a new house. Performance (P) is measured by how close the predicted prices are to the actual prices — the smaller the error, the better.

---

#### Example 3: Disease Prediction

| Component | Description |
|-----------|-------------|
| **T** (Task) | Predict whether a patient has diabetes based on medical test results |
| **E** (Experience) | Medical records of 5,000 patients with test results and diabetes diagnosis |
| **P** (Performance) | Percentage of correct predictions (accuracy), and also sensitivity/specificity |

**Explanation:** The model learns from patient records (E) that include features like blood glucose level, BMI, age, and the known diagnosis. Its task (T) is to predict if a new patient has diabetes. Performance (P) is how often it gets the diagnosis right.

---

### 6.8 Formal Definition (Exam-Ready)

> **Tom Mitchell's Definition (1997):**
>
> "A computer program is said to learn from experience **E** with respect to some class of tasks **T** and performance measure **P**, if its performance at tasks in **T**, as measured by **P**, improves with experience **E**."

**In simple language:** A program is "learning" if it gets better at its job as it sees more data.

**Why this definition matters:**
- It makes "learning" a **precise, measurable** concept.
- It prevents us from loosely saying "the computer learned something" without evidence.
- It is the **standard definition** used in textbooks, exams, and research papers.

---

### 6.9 Common Mistakes About T, E, and P

| Mistake | Correction |
|---------|------------|
| Confusing T with E | T is the **task** (what to do). E is the **data** (what to learn from). They are different. |
| Defining T too vaguely | "Make the computer intelligent" is NOT a valid task. It must be specific: "Classify images of cats vs dogs." |
| Forgetting P | Without P, you cannot claim the system is learning. You must have a measurable metric. |
| Using the training data as the performance measure | P should be measured on **unseen test data**, not the same data used for training. |
| Thinking E must be labelled | In unsupervised learning, E can be unlabelled data. The definition does not require labels. |

---
---

# PART C — DESIGNING A LEARNING SYSTEM

---

## 7. Designing a Learning System

Once you understand what a well-posed learning problem is, the next question is: **how do you actually build an ML system?**

Designing a learning system means planning the complete pipeline — from understanding the problem to deploying a working model.

### 7.1 Overall ML Workflow

```
┌─────────────────────────┐
│  1. Problem Definition   │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  2. Data Collection      │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  3. Data Preparation     │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  4. Feature Selection /  │
│     Feature Engineering  │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  5. Choose Learning      │
│     Approach             │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  6. Training             │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  7. Validation / Testing │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  8. Evaluation           │
└──────────┬──────────────┘
           ↓
┌─────────────────────────┐
│  9. Prediction /         │
│     Deployment           │
└─────────────────────────┘
```

Each stage is essential. Skipping or rushing any stage will result in a poor model.

---

## 8. Problem Definition

This is the **first and most critical step**. If you define the problem incorrectly, everything that follows will be wrong.

### 8.1 Questions to Ask

- **What are we trying to predict?** → This becomes the **label / target**.
- **What information do we have?** → These become the **features / inputs**.
- **What type of output do we expect?**

### 8.2 Classification vs Regression (Preview)

At the problem definition stage, you must determine the type of output:

| Output Type | Problem Type | Example |
|-------------|-------------|---------|
| **Category** (discrete) | Classification | Spam / Not Spam, Disease / No Disease |
| **Number** (continuous) | Regression | House price = ₹52,00,000 |

> **Memory tip:**
> - Classification → "Which category?"
> - Regression → "How much?" or "What number?"

---

## 9. Data Collection

### 9.1 What Data Is Required?

You need data that is **relevant to the problem** and contains enough examples for the algorithm to learn meaningful patterns.

### 9.2 Sources of Data

| Source | Example |
|--------|---------|
| Company databases | Customer transaction records |
| Public datasets | Kaggle, UCI ML Repository, government open data |
| APIs | Twitter API, weather data API |
| Surveys / forms | Manually collected responses |
| Sensors / IoT | Temperature, motion, health sensors |
| Web scraping | Product reviews, news articles |

### 9.3 Data Quality Matters

> **"Garbage in, garbage out"** is the golden rule of ML.

If your data is:
- **Too small** → The model won't learn enough patterns.
- **Biased** → The model will learn biased patterns (e.g., only trained on one demographic).
- **Noisy** → Full of errors and inconsistencies → poor predictions.
- **Irrelevant** → Contains features that have nothing to do with the target → wasted computation.

**The quality and quantity of your data often matters more than the choice of algorithm.**

---

## 10. Data Preparation

Real-world data is almost **never** clean and ready to use. Data preparation (also called **data preprocessing**) involves cleaning and transforming the raw data.

### 10.1 Common Data Problems

| Problem | Description |
|---------|-------------|
| Missing values | Some cells in the dataset are empty |
| Incorrect data | Typos, wrong entries, or corrupted values |
| Inconsistent formats | "Male" vs "M" vs "male" for the same thing |
| Categorical data | Text values like "Red", "Blue" that need encoding |
| Different scales | Age (0–100) vs Salary (10,000–10,00,000) — vastly different ranges |
| Outliers | Extreme values that don't represent the general pattern |

### 10.2 Why Preparation Is Necessary

- Most ML algorithms expect **clean, numerical data** as input.
- Missing values can cause algorithms to crash or produce incorrect results.
- Features on vastly different scales can cause some features to dominate others unfairly.

> **Note:** We will cover specific preprocessing techniques (missing value imputation, handling imbalanced data, feature engineering) in detail in Part F of this module.

---

## 11. Features and Labels

We introduced features and labels in Section 5, but let's reinforce with more examples since this distinction is **absolutely fundamental**.

### Example 1: House Price Prediction

| Feature (Input — what we know) | Label (Output — what we predict) |
|-------------------------------|----------------------------------|
| Area (sq ft) | **Price (₹)** |
| Number of bedrooms | |
| Location | |
| Age of building | |

### Example 2: Disease Prediction

| Feature (Input) | Label (Output) |
|-----------------|----------------|
| Age | **Diabetic? (Yes / No)** |
| Blood pressure | |
| Glucose level | |
| BMI | |

### Example 3: Student Exam Result

| Feature (Input) | Label (Output) |
|-----------------|----------------|
| Attendance (%) | **Pass / Fail** |
| Hours studied | |
| Previous grade | |
| Assignment score | |

### Example 4: Spam Detection

| Feature (Input) | Label (Output) |
|-----------------|----------------|
| Number of links in email | **Spam / Not Spam** |
| Contains "free money"? | |
| Sender in contact list? | |
| Email length | |

> **Rule of thumb:** Features = columns you give to the model as input. Label = the one column you want the model to predict.

---

## 12. Training, Validation, and Testing

### 12.1 Why Split the Data?

You cannot test a student using the same questions you taught them — that tests memory, not understanding. Similarly, you cannot evaluate an ML model on the same data it was trained on.

We split the dataset into separate portions:

```
Full Dataset
├── Training Set   (typically 60-80%)  →  Model learns from this
├── Validation Set (typically 10-20%)  →  Used to tune the model during development
└── Test Set       (typically 10-20%)  →  Final evaluation on unseen data
```

### 12.2 Training Data

- The portion of data the model **learns from**.
- The algorithm sees both features AND labels.
- The model adjusts itself to fit these examples.

### 12.3 Validation Data

- Used **during development** to compare different models or settings.
- Helps you decide which model or configuration works best.
- The model does NOT learn from this data.

### 12.4 Test Data

- Used **only at the end** for final evaluation.
- Simulates how the model will perform on completely new, real-world data.
- Must NEVER be used during training or model selection.

### 12.5 Why This Matters

| If you... | Problem |
|-----------|---------|
| Train and test on the same data | You measure memorisation, not learning. The model may **overfit** — perform well on training data but poorly on new data. |
| Skip validation | You cannot properly compare or tune different models. |
| Use test data during development | Your "final" score is no longer trustworthy. |

> **Cross-validation** is an advanced technique where you repeatedly split the data in different ways to get a more reliable performance estimate. You will learn this in detail later, but know that it exists.

---

## 13. Choosing a Learning Approach

The nature of your problem determines which type of ML to use:

| Situation | Approach |
|-----------|----------|
| You have labelled data (features + correct answers) | **Supervised Learning** |
| You have data but NO labels | **Unsupervised Learning** |
| An agent interacts with an environment and learns from rewards/penalties | **Reinforcement Learning** |

We will now explore each of these in depth.

---
---

# PART D — TYPES OF MACHINE LEARNING

---

## 14. Types of Machine Learning — Overview

Machine Learning approaches are broadly categorised into three types based on the **nature of the data and feedback** available:

```
                    Machine Learning
                         │
         ┌───────────────┼───────────────┐
         │               │               │
    Supervised      Unsupervised    Reinforcement
     Learning        Learning        Learning
         │               │               │
    ┌────┴────┐     ┌────┴────┐     ┌────┴────┐
    │         │     │         │     │         │
 Classif. Regress. Cluster. Dimens. Agent-based
                           Reduct.  learning
```

| Type | Data has labels? | Feedback type | Goal |
|------|-----------------|---------------|------|
| Supervised | Yes | Correct answers provided | Learn mapping from input → output |
| Unsupervised | No | No explicit feedback | Discover hidden patterns/structure |
| Reinforcement | No labels; has rewards | Reward/penalty signals | Learn to maximise cumulative reward |

---

## 15. Supervised Learning

### 15.1 What Is Supervised Learning?

**Analogy:** Imagine a teacher (supervisor) teaching a student. The teacher gives practice questions (features) along with the **correct answers** (labels). The student studies these and learns to solve similar questions on their own.

> **Supervised Learning** is a type of ML where the algorithm learns from **labelled data** — data where both the input features and the correct output (label) are provided.

The word "supervised" comes from the idea that the learning process is guided by a **supervisor** (the labels / correct answers).

### 15.2 What Is Labelled Data?

**Labelled data** = Every data point has a known, correct answer attached.

| Area | Bedrooms | Price (Label) |
|------|----------|---------------|
| 1200 | 2 | ₹50,00,000 |
| 1800 | 3 | ₹70,00,000 |
| 900 | 1 | ₹35,00,000 |

Here, we **know** the price for each house. That price is the **label**.

**Unlabelled data** = Same features, but no answer column:

| Area | Bedrooms | Price |
|------|----------|-------|
| 1500 | 2 | **?** |

The model must **predict** the missing price.

### 15.3 Features and Labels in Supervised Learning

In supervised learning:
- **Features** are the inputs the model uses to make predictions.
- **Labels** are the correct outputs the model tries to learn.
- During **training**, the model sees BOTH features and labels.
- During **prediction**, the model receives ONLY features and must produce the label.

### 15.4 How Supervised Learning Works

```
TRAINING PHASE:
┌──────────────────┐    ┌──────────────────┐
│  Input Features   │    │  Correct Labels   │
│  (what we know)   │    │  (correct answer)  │
└────────┬─────────┘    └────────┬──────────┘
         │                       │
         └───────────┬───────────┘
                     │
                     ↓
          ┌─────────────────────┐
          │  Learning Algorithm  │
          └──────────┬──────────┘
                     │
                     ↓
          ┌─────────────────────┐
          │    Trained Model     │
          └─────────────────────┘

PREDICTION PHASE:
┌──────────────────┐
│  New Input        │
│  (features only)  │
└────────┬─────────┘
         │
         ↓
┌─────────────────────┐
│    Trained Model     │
└────────┬────────────┘
         │
         ↓
┌─────────────────────┐
│    Prediction        │
│    (predicted label) │
└─────────────────────┘
```

### 15.5 Classification

**Classification** is a supervised learning task where the model predicts a **category** or **class**.

The output is **discrete** — it belongs to one of a fixed set of categories.

**Key question Classification answers:** *"Which category does this belong to?"*

### 15.6 Binary Classification

The output has **exactly two** possible classes.

| Problem | Class 1 | Class 2 |
|---------|---------|---------|
| Email filtering | Spam | Not Spam |
| Disease screening | Positive | Negative |
| Loan approval | Approved | Rejected |
| Fraud detection | Fraud | Genuine |

> **Binary** = two outcomes.

### 15.7 Multi-Class Classification

The output has **three or more** possible classes.

| Problem | Possible Classes |
|---------|-----------------|
| Animal recognition | Cat, Dog, Horse, Bird, … |
| Digit recognition | 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 |
| Sentiment analysis | Positive, Neutral, Negative |
| Risk level | Low, Medium, High |

> **Multi-class** = more than two outcomes.

### 15.8 Regression

**Regression** is a supervised learning task where the model predicts a **continuous numerical value**.

The output is a **number** that can take any value within a range.

**Key question Regression answers:** *"How much?" or "What value?"*

| Problem | Predicted Value |
|---------|----------------|
| House price prediction | ₹52,30,000 |
| Temperature forecasting | 34.7°C |
| Stock price prediction | ₹1,245.60 |
| Salary estimation | ₹8,50,000/year |
| Crop yield prediction | 2.3 tonnes/acre |

### 15.9 Classification vs Regression

This is a **very commonly asked** comparison in exams and interviews.

| Aspect | Classification | Regression |
|--------|---------------|------------|
| **Output type** | Discrete category / class | Continuous number |
| **Question answered** | "Which category?" | "How much?" |
| **Example output** | "Spam", "Cat", "High Risk" | 52.3, ₹70,00,000, 98.6°F |
| **Number of possible outputs** | Finite set of classes | Infinite (any number in a range) |
| **Example task** | Email → Spam / Not Spam | House features → Price |
| **Common algorithms** | Logistic Regression, Decision Trees, SVM, KNN | Linear Regression, Polynomial Regression |
| **Evaluation metrics** | Accuracy, Precision, Recall, F1 | MAE, MSE, RMSE, R² |

> **Quick test:** If the answer is a **category name** → Classification. If the answer is a **number on a continuous scale** → Regression.

### 15.10 Advantages and Limitations of Supervised Learning

**Advantages:**
- Produces **highly accurate** models when sufficient labelled data is available.
- Output is **well-defined** and easy to evaluate.
- Widely applicable — most real-world ML problems can be framed as supervised learning.
- Many mature, well-understood algorithms available.

**Limitations:**
- Requires **labelled data**, which can be expensive and time-consuming to create.
  - Example: Labelling thousands of medical images requires expert doctors.
- May **overfit** — memorise training data instead of learning general patterns.
- Cannot discover completely unknown patterns — it can only learn what the labels tell it.
- Performance depends heavily on the **quality of labels**.

---

## 16. Unsupervised Learning

### 16.1 What Is Unsupervised Learning?

**Analogy:** Imagine you are given a basket of mixed fruits, but nobody tells you the name of any fruit. You naturally start grouping them — round red ones together, long yellow ones together, small purple ones together. You have **discovered structure** without anyone telling you the categories.

> **Unsupervised Learning** is a type of ML where the algorithm learns from **unlabelled data** — data that has features but **no labels/answers**.

The algorithm must find **hidden patterns, groupings, or structure** in the data on its own.

### 16.2 Unlabelled Data

In unsupervised learning, we do NOT have a "correct answer" column.

| Customer ID | Age | Income | Spending Score |
|-------------|-----|--------|---------------|
| C001 | 25 | 40,000 | 75 |
| C002 | 45 | 90,000 | 30 |
| C003 | 23 | 35,000 | 80 |
| C004 | 50 | 95,000 | 25 |

There is no label column. We don't know what "category" each customer belongs to. The algorithm must discover any meaningful groupings by itself.

### 16.3 How Unsupervised Learning Works

```
┌──────────────────┐
│  Unlabelled Data  │
│  (features only,  │
│   no answers)     │
└────────┬─────────┘
         │
         ↓
┌────────────────────┐
│  Learning Algorithm │
│  (finds patterns)   │
└────────┬───────────┘
         │
         ↓
┌────────────────────┐
│  Discovered         │
│  Structure:         │
│  - Groups/Clusters  │
│  - Patterns         │
│  - Reduced features │
└────────────────────┘
```

### 16.4 Pattern Discovery

The main goal of unsupervised learning is to discover **hidden structure** in data:

- Which data points are **similar** to each other? → Clustering
- Are there **redundant features** that can be combined? → Dimensionality Reduction
- Are there **unusual data points** that don't fit any pattern? → Anomaly Detection

### 16.5 Clustering

**Clustering** is the most common unsupervised learning task. It groups similar data points together.

**Example — Customer Segmentation:**

A retail company has data on thousands of customers but no predefined categories. Clustering can discover natural groups:

```
  Spending ↑
  Score    │
           │  ★ ★ ★          ● ● ●
           │  ★ ★              ● ●
           │
           │        ▲ ▲ ▲
           │       ▲ ▲
           │
           └──────────────────────→ Income

  ★ = Group 1: Young, low income, high spending (impulsive buyers)
  ▲ = Group 2: Middle income, moderate spending (average customers)
  ● = Group 3: High income, high spending (premium customers)
```

The algorithm discovered these groups **without being told** what groups to look for.

**Why this is useful:**
- The company can create targeted marketing campaigns for each group.
- Discover customer segments you didn't know existed.

**Other clustering examples:**
- Grouping news articles by topic.
- Grouping genes with similar expression patterns.
- Identifying types of network traffic.

### 16.6 Dimensionality Reduction

Sometimes datasets have **too many features** (e.g., 500 columns). Many of these features may be redundant or correlated.

**Dimensionality reduction** reduces the number of features while keeping the most important information.

> **Analogy:** Imagine summarising a 500-page book into a 50-page summary. You lose some detail, but you keep the essential content.

**Why it's useful:**
- Speeds up training.
- Reduces noise.
- Makes data easier to visualise (e.g., reducing 100 features to 2 for plotting).
- Helps avoid the "curse of dimensionality" (models struggle with too many features).

**Common techniques** (you will study these in detail in later modules):
- **PCA** (Principal Component Analysis)
- **LDA** (Linear Discriminant Analysis)

> **Note:** You do NOT need to know the algorithms yet. Just understand **what** dimensionality reduction does and **why** it is useful.

### 16.7 Advantages and Limitations of Unsupervised Learning

**Advantages:**
- Does **not require labelled data** — saves the cost and effort of labelling.
- Can discover **hidden patterns** that humans might not notice.
- Useful for **exploratory data analysis** — understanding the structure of new data.

**Limitations:**
- Results can be **harder to interpret** — there is no "correct answer" to compare against.
- **Evaluation is subjective** — how do you know if the clusters are "good"?
- May find patterns that are **meaningless** or coincidental.
- Generally **less accurate** than supervised learning for prediction tasks.

---

## 17. Reinforcement Learning

### 17.1 What Is Reinforcement Learning?

**Analogy:** Think about how you train a dog.

- The dog performs an action (sits, rolls, fetches).
- If the action is good, you give a **treat** (reward).
- If the action is bad, you give **no treat** or say "no" (penalty).
- Over time, the dog learns which actions lead to treats and repeats those.

Nobody shows the dog the "correct" answer. The dog learns through **trial and error** with **feedback** (rewards and penalties).

> **Reinforcement Learning (RL)** is a type of ML where an **agent** learns to make decisions by interacting with an **environment**, receiving **rewards** for good actions and **penalties** for bad ones, and learning a strategy (**policy**) that maximises the total reward over time.

### 17.2 Agent

The **agent** is the learner / decision-maker. It is the entity that takes actions.

**Examples:**
- A robot navigating a room.
- An AI playing a video game.
- A self-driving car.
- A trading algorithm.

### 17.3 Environment

The **environment** is everything outside the agent that it interacts with. It responds to the agent's actions.

**Examples:**
- The room the robot is in.
- The game world.
- The road and traffic.
- The stock market.

### 17.4 State

The **state** represents the current situation of the agent in the environment at a given moment.

**Examples:**
- The robot's current position in the room.
- The current score and positions in a game.
- The car's speed, position, and surrounding traffic.

### 17.5 Action

An **action** is a move or decision the agent can make.

**Examples:**
- The robot moves left, right, forward, or backward.
- The game player jumps, attacks, or defends.
- The car accelerates, brakes, or turns.

### 17.6 Reward

A **reward** is a numerical signal the agent receives after taking an action. It tells the agent how good or bad the action was.

| Action Outcome | Reward |
|---------------|--------|
| Robot reaches the goal | +100 (positive reward) |
| Robot hits a wall | -10 (penalty) |
| Robot takes one step | -1 (small penalty to encourage efficiency) |

### 17.7 Policy

A **policy** is the strategy the agent learns — a mapping from states to actions.

> "In this state, take this action."

A good policy maximises the **total cumulative reward** over time, not just the immediate reward.

**Example:** A chess-playing agent might sacrifice a piece now (short-term loss) to win the game later (long-term gain). The policy captures this strategic thinking.

### 17.8 The Basic RL Loop

```
┌─────────────┐
│   Agent      │
│  observes    │
│  state       │
└──────┬──────┘
       │
       ↓
┌─────────────┐
│  Agent       │
│  chooses     │
│  action      │
└──────┬──────┘
       │
       ↓
┌─────────────────┐
│  Environment     │
│  responds:       │
│  - New state     │
│  - Reward        │
└──────┬──────────┘
       │
       ↓
┌─────────────┐
│  Agent       │
│  updates     │
│  policy      │
└──────┬──────┘
       │
       └──→ Repeat
```

**Step-by-step:**
1. The agent **observes** the current state of the environment.
2. Based on its current policy, the agent **chooses an action**.
3. The environment **responds** with a new state and a reward.
4. The agent **updates its policy** based on the reward received.
5. This cycle **repeats** thousands or millions of times until the agent learns a good policy.

**Simple Game Example:**

Imagine a grid world where a robot must reach a treasure:

```
┌───┬───┬───┬───┐
│ R │   │   │   │      R = Robot (agent)
├───┼───┼───┼───┤      X = Wall (penalty: -10)
│   │ X │   │   │      T = Treasure (reward: +100)
├───┼───┼───┼───┤
│   │   │ X │   │      Each step costs -1
├───┼───┼───┼───┤      (to encourage finding shortest path)
│   │   │   │ T │
└───┴───┴───┴───┘
```

- The robot tries different paths.
- Hitting walls → negative reward → learns to avoid walls.
- Reaching treasure → big positive reward → learns to move toward treasure.
- After many attempts, the robot discovers the **optimal path**.

### 17.9 Advantages and Limitations of Reinforcement Learning

**Advantages:**
- Can learn in **complex, dynamic environments** where rules are unknown.
- Does **not require labelled data** — learns from interaction.
- Can discover **optimal strategies** that humans might not think of.
- Suitable for sequential decision-making (games, robotics, navigation).

**Limitations:**
- Requires **many iterations** (trial and error) — can be very slow.
- Designing a good **reward function** is challenging and critical.
- Can be **unstable** — small changes in rewards can lead to very different behaviour.
- Computationally **expensive** — often requires powerful hardware.
- Difficult to apply to all types of problems — best suited for decision-making tasks.

---
---

# PART E — COMPARING THE THREE TYPES

---

## 18. Supervised vs Unsupervised vs Reinforcement Learning

### 18.1 Comprehensive Comparison Table

| Aspect | Supervised Learning | Unsupervised Learning | Reinforcement Learning |
|--------|--------------------|-----------------------|----------------------|
| **Data** | Labelled data (features + answers) | Unlabelled data (features only) | No dataset; learns from interaction |
| **Labels** | Required | Not available | Not applicable |
| **Feedback** | Direct — correct answer is given | No feedback | Indirect — reward/penalty signal |
| **Goal** | Learn input → output mapping | Discover hidden structure/patterns | Learn a policy to maximise reward |
| **Learning style** | Learn from examples with answers | Find patterns without guidance | Learn from trial and error |
| **Human analogy** | Student with a teacher | Student exploring on their own | Child learning from consequences |
| **Typical tasks** | Classification, Regression | Clustering, Dimensionality Reduction | Game playing, Robot control |
| **Example** | Spam detection, Price prediction | Customer segmentation | AlphaGo, Self-driving car |
| **Evaluation** | Compare predictions to known answers | Subjective; cluster quality metrics | Total reward accumulated |
| **Data requirement** | Labelled data (expensive to create) | Any data (easier to obtain) | Simulated or real environment |

### 18.2 Memory Trick

```
┌────────────────────────────────────────────────────────────┐
│                                                            │
│  Supervised     →  Teacher gives ANSWERS                   │
│                    "Here's the question AND the answer.    │
│                     Learn to solve similar ones."          │
│                                                            │
│  Unsupervised   →  Discover PATTERNS                      │
│                    "Here's a pile of data.                 │
│                     Find something interesting."           │
│                                                            │
│  Reinforcement  →  Trial + FEEDBACK                       │
│                    "Try something. I'll tell you if it     │
│                     was good or bad. Figure it out."       │
│                                                            │
└────────────────────────────────────────────────────────────┘
```

### 18.3 Quick Visual Summary

```
                    ┌─────────────────┐
                    │ Machine Learning │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
   ┌─────┴─────┐      ┌─────┴─────┐      ┌─────┴─────┐
   │ Supervised  │      │Unsupervised│      │Reinforcement│
   │             │      │            │      │             │
   │ Has labels  │      │ No labels  │      │ Has rewards │
   │ Has teacher │      │ Self-study │      │ Trial+error │
   └─────┬───────┘      └─────┬──────┘      └──────┬──────┘
         │                    │                     │
    ┌────┴────┐          ┌────┴────┐           ┌────┴────┐
    │         │          │         │           │         │
 Classif. Regress.   Cluster.  Dim.Red.    Agent-Env
                                           Interaction
```

---
---

# PART F — DATA PREPROCESSING

---

## 19. Introduction to Data Preprocessing

### 19.1 What Is Data Preprocessing?

**Data preprocessing** is the process of cleaning, transforming, and organising raw data before feeding it to a machine learning algorithm.

### 19.2 Why Is Preprocessing Needed?

Real-world data is **messy**. It comes from multiple sources, collected by different people, at different times, in different formats. Common problems include:

- Missing entries (blank cells)
- Inconsistent formatting
- Outliers (extreme unusual values)
- Irrelevant features
- Imbalanced class distribution

> **Analogy:** Imagine trying to cook a meal with unwashed, unsorted, partially rotten ingredients. You need to clean, sort, and prepare the ingredients before cooking. Data preprocessing is the "ingredient preparation" step of ML.

### 19.3 Impact on Model Performance

| Data Quality | Model Quality |
|-------------|---------------|
| Clean, well-prepared data | Better patterns learned → better predictions |
| Messy, unprocessed data | Garbage patterns → garbage predictions |

Most data scientists spend **60-80% of their time** on data collection and preprocessing, and only 20-40% on actual model building. That tells you how important this step is.

---

## 20. Missing Values

### 20.1 What Are Missing Values?

Missing values are **blank or null entries** in a dataset — places where data should exist but doesn't.

**Example:**

| Patient ID | Age | Blood Pressure | Glucose | Diagnosis |
|------------|-----|---------------|---------|-----------|
| P001 | 45 | 130 | 180 | Diabetic |
| P002 | 52 | — | 200 | Diabetic |
| P003 | — | 120 | 95 | Healthy |
| P004 | 38 | 140 | — | Healthy |

The `—` entries are missing values. Patient P002 has no blood pressure recorded, P003 has no age, and P004 has no glucose reading.

### 20.2 Why Do Missing Values Occur?

| Reason | Example |
|--------|---------|
| Data entry errors | Someone forgot to fill a field |
| Equipment failure | A sensor malfunctioned during recording |
| Survey non-response | A patient refused to answer a question |
| Data not applicable | "Spouse's income" for an unmarried person |
| Data merging issues | Combining databases with different columns |

### 20.3 Why Are Missing Values a Problem?

1. **Most ML algorithms cannot handle missing values** — they will throw an error or produce incorrect results.
2. Missing values **reduce the effective dataset size** if you simply remove incomplete rows.
3. They can introduce **bias** if the missingness is not random.

### 20.4 Approaches to Handle Missing Values (Missing Value Imputation)

**Imputation** means filling in the missing values with reasonable substitute values.

| Approach | How It Works | When to Use |
|----------|-------------|-------------|
| **Remove rows** | Delete any row with missing values | When very few rows have missing data |
| **Remove columns** | Delete the entire column/feature | When most values in that feature are missing |
| **Mean imputation** | Replace missing value with the average of that column | Numerical data with few missing values |
| **Median imputation** | Replace with the middle value of that column | Numerical data with outliers (median is robust to outliers) |
| **Mode imputation** | Replace with the most frequent value | Categorical data (e.g., "Male" appears most often) |
| **Constant value** | Replace with a fixed value (e.g., 0 or "Unknown") | When a specific default makes domain sense |

**Example — Mean Imputation:**

| Patient | Glucose |
|---------|---------|
| P001 | 180 |
| P002 | 200 |
| P003 | 95 |
| P004 | **?** |

Mean of known values = (180 + 200 + 95) / 3 = **158.3**

Replace P004's missing glucose with **158.3**.

> **Important caveat:** Imputation introduces **approximated data** — it's better than nothing, but it's not the actual value. More advanced techniques exist (e.g., KNN imputation, regression imputation) but those are beyond Module 1 scope.

---

## 21. Balanced vs Imbalanced Data

### 21.1 What Is a Balanced Dataset?

A **balanced dataset** has roughly **equal** numbers of examples for each class.

**Example — Email classification:**

| Class | Count |
|-------|-------|
| Spam | 5,000 |
| Not Spam | 5,000 |

Ratio is approximately 1:1. This is balanced.

### 21.2 What Is an Imbalanced Dataset?

An **imbalanced dataset** has a **significant difference** in the number of examples across classes.

**Example — Fraud detection:**

| Class | Count |
|-------|-------|
| Genuine transactions | 99,500 |
| Fraudulent transactions | 500 |

Ratio is approximately 199:1. This is highly imbalanced.

### 21.3 Why Does Imbalance Matter?

Here's the critical insight:

> If 99.5% of transactions are genuine, a model that **always predicts "genuine"** (without even looking at the data) would achieve **99.5% accuracy**.

That sounds great, right? **But this model is completely useless** — it catches ZERO fraud cases, which was the entire point of building it.

**The problem:** With imbalanced data, **accuracy is misleading**. The model can "cheat" by always predicting the majority class.

### 21.4 Example: Fraud Detection in Detail

```
Total transactions:  100,000
Genuine:              99,500  (99.5%)
Fraudulent:              500  (0.5%)

Model that ALWAYS predicts "Genuine":
  Correct predictions:   99,500  (all genuine correctly classified)
  Wrong predictions:        500  (all fraud missed)
  Accuracy:             99.5%   ← Looks great!
  Fraud caught:          0/500  ← Completely useless!
```

### 21.5 Basic Approaches to Handle Imbalance

| Approach | Description |
|----------|-------------|
| **Oversampling** | Duplicate or generate more examples of the minority class |
| **Undersampling** | Remove some examples of the majority class |
| **Use better metrics** | Instead of accuracy, use Precision, Recall, F1-Score (covered in later modules) |
| **Class weights** | Tell the algorithm to pay more attention to the minority class |

> **For now**, just understand **why** imbalance is a problem and **why accuracy alone is not enough**. Advanced techniques will be covered later.

---

## 22. Feature Engineering

### 22.1 What Is a Feature?

A **feature** is a measurable property or characteristic of the data that is used as input to a model.

In a house price dataset: Area, Bedrooms, Location, Age are all features.

### 22.2 What Is Feature Engineering?

> **Feature engineering** is the process of using domain knowledge to **create, transform, or select** features that make machine learning models work better.

It is often considered the **most important** step in building a good ML model.

> **"Coming up with features is difficult, time-consuming, and requires expert knowledge. Applied machine learning is basically feature engineering."** — Andrew Ng

### 22.3 Why Does Feature Engineering Matter?

The features you give to a model **directly determine** what the model can learn.

- **Good features** → The model can easily find patterns → High accuracy.
- **Bad or irrelevant features** → The model struggles → Low accuracy.

No amount of algorithm sophistication can compensate for poor features.

### 22.4 Types of Feature Engineering

#### A. Creating New Features

Combine or transform existing features to create more informative ones.

**Example:**

| Existing Features | New Feature Created |
|-------------------|-------------------|
| `Birth Year` | `Age = Current Year - Birth Year` |
| `Total Purchase`, `Number of Visits` | `Average Purchase = Total / Visits` |
| `Length`, `Width` | `Area = Length × Width` |
| `Date of Transaction` | `Day of Week`, `Month`, `Is Weekend?` |

#### B. Transforming Existing Features

Change the representation of a feature to make it more useful.

| Transformation | Example |
|---------------|---------|
| **Log transformation** | Transform highly skewed salary data: `log(salary)` |
| **Encoding categorical data** | Convert "Red", "Blue", "Green" into numbers: 0, 1, 2 |
| **Scaling / Normalisation** | Rescale features to a common range (e.g., 0 to 1) |
| **Binning** | Convert continuous age into groups: "Young", "Middle", "Senior" |

#### C. Selecting Useful Features (Feature Selection)

Remove features that are **irrelevant or redundant** to reduce noise and improve performance.

**Example:** In a disease prediction dataset, a "Patient ID" column has no predictive value and should be removed. A "Phone Number" column is also irrelevant.

**Why remove features?**
- Reduces training time.
- Reduces overfitting (the model won't try to learn from noise).
- Makes the model simpler and more interpretable.

### 22.5 Feature Engineering Example: House Price Prediction

**Original features:**
| Area | Bedrooms | Bathrooms | Year Built | Has Garden | Location |

**Engineered features:**
| Feature | How It Was Created |
|---------|-------------------|
| `Price per sqft` | Created from area and price of nearby houses |
| `Building Age` | `Current Year - Year Built` |
| `Total Rooms` | `Bedrooms + Bathrooms` |
| `Is New` | `1 if Age < 5 years, else 0` |
| `Location Score` | Encode location as a numerical rating |

> **Key insight:** Feature engineering requires **domain knowledge** — understanding the problem domain helps you create features that capture meaningful information.

---
---

# PART G — MODULE 1 APPLICATIONS

---

## 23. Real-World Applications

### 23.1 Healthcare — Disease Prediction

| Aspect | Details |
|--------|---------|
| **Problem** | Predict whether a patient has diabetes |
| **Input / Features** | Age, BMI, blood pressure, glucose level, family history |
| **Output** | Diabetic / Not Diabetic |
| **Learning Type** | Supervised Learning |
| **ML Task** | Binary Classification |
| **Impact** | Early detection, preventive care, reduced healthcare costs |

### 23.2 Finance — Fraud Detection

| Aspect | Details |
|--------|---------|
| **Problem** | Detect fraudulent credit card transactions |
| **Input / Features** | Transaction amount, time, location, merchant type, user history |
| **Output** | Fraud / Genuine |
| **Learning Type** | Supervised Learning |
| **ML Task** | Binary Classification (with imbalanced data) |
| **Impact** | Saves billions in financial losses annually |

### 23.3 General Classification — Spam Detection

| Aspect | Details |
|--------|---------|
| **Problem** | Classify emails as spam or not spam |
| **Input / Features** | Words in email, sender info, number of links, email length |
| **Output** | Spam / Not Spam |
| **Learning Type** | Supervised Learning |
| **ML Task** | Binary Classification |
| **Impact** | Keeps inboxes clean, prevents phishing attacks |

### 23.4 Regression — House Price Prediction

| Aspect | Details |
|--------|---------|
| **Problem** | Predict the selling price of a house |
| **Input / Features** | Area, bedrooms, bathrooms, location, age, amenities |
| **Output** | Price (e.g., ₹52,00,000) |
| **Learning Type** | Supervised Learning |
| **ML Task** | Regression |
| **Impact** | Helps buyers, sellers, and real estate agents make informed decisions |

### 23.5 Customer Analytics — Customer Segmentation

| Aspect | Details |
|--------|---------|
| **Problem** | Group customers into meaningful segments for targeted marketing |
| **Input / Features** | Age, income, spending score, purchase frequency, product preferences |
| **Output** | Customer groups / segments (e.g., Budget buyers, Premium buyers, Occasional shoppers) |
| **Learning Type** | Unsupervised Learning |
| **ML Task** | Clustering |
| **Impact** | Personalised marketing, better customer experience, increased revenue |

---
---

# PART H — REVISION

---

## 24. Important Differences

### 24.1 Traditional Programming vs Machine Learning

| Traditional Programming | Machine Learning |
|------------------------|------------------|
| Human writes rules | Machine discovers rules |
| Data + Rules → Output | Data + Output → Learned Rules (Model) |
| Requires explicit logic | Requires data |
| Difficult to scale for complex tasks | Scales with more data |

### 24.2 Feature vs Label

| Feature | Label |
|---------|-------|
| Input (what we know) | Output (what we predict) |
| Independent variable | Dependent variable |
| Multiple per data point | Typically one per data point |
| Given during both training and prediction | Given only during training |

### 24.3 Classification vs Regression

| Classification | Regression |
|---------------|------------|
| Predicts a category | Predicts a number |
| Discrete output | Continuous output |
| "Which class?" | "How much?" |
| Spam / Not Spam | Price = ₹52L |

### 24.4 Supervised vs Unsupervised Learning

| Supervised | Unsupervised |
|-----------|-------------|
| Labelled data | Unlabelled data |
| Teacher provides answers | No teacher |
| Classification, Regression | Clustering, Dimensionality Reduction |
| Goal: predict labels | Goal: discover patterns |

### 24.5 Supervised vs Reinforcement Learning

| Supervised | Reinforcement |
|-----------|---------------|
| Learns from labelled examples | Learns from rewards/penalties |
| Correct answer given directly | Must discover correct behaviour |
| Static dataset | Dynamic interaction with environment |
| One-shot prediction | Sequential decision-making |

### 24.6 Training vs Testing

| Training | Testing |
|----------|---------|
| Model learns from this data | Model is evaluated on this data |
| Features AND labels used | Only features given; predictions compared to actual labels |
| Goal: learn patterns | Goal: measure generalisation |
| Larger portion of data (~70-80%) | Smaller portion (~10-20%) |

### 24.7 Algorithm vs Model

| Algorithm | Model |
|-----------|-------|
| The learning process / method | The result of learning |
| Runs during training | Used during prediction |
| Example: Linear Regression algorithm | Example: A specific linear equation learned from data |
| Analogy: Study method | Analogy: Knowledge gained |

---

## 25. Important Definitions

| Term | Definition |
|------|-----------|
| **Machine Learning** | A field of computer science where programs automatically improve their performance at a task through experience (data), without being explicitly programmed. |
| **Well-Posed Learning Problem** | A learning problem defined by a Task (T), Experience (E), and Performance Measure (P), where performance at T, measured by P, improves with experience E. (Tom Mitchell, 1997) |
| **Task (T)** | The specific job the ML program must perform (e.g., classify emails, predict prices). |
| **Experience (E)** | The data or information the program learns from (training data). |
| **Performance Measure (P)** | A quantitative metric that evaluates how well the program performs the task (e.g., accuracy, error rate). |
| **Dataset** | A structured collection of data used for training and testing ML models. |
| **Data Point / Instance** | A single example or observation in a dataset (one row). |
| **Feature** | A measurable property used as input to a model; an independent variable. |
| **Label / Target** | The output variable that the model is trained to predict; a dependent variable. |
| **Model** | A mathematical representation produced by an ML algorithm that captures patterns from data. |
| **Algorithm** | A specific method or procedure used to learn from data and produce a model. |
| **Training** | The process of feeding data to an algorithm so it learns patterns. |
| **Testing** | Evaluating a trained model on unseen data to measure its real-world performance. |
| **Prediction / Inference** | Using a trained model to produce output for new, unseen input data. |
| **Supervised Learning** | ML where the algorithm learns from labelled data (input + correct output). |
| **Unsupervised Learning** | ML where the algorithm learns from unlabelled data and discovers hidden patterns. |
| **Reinforcement Learning** | ML where an agent learns by interacting with an environment, receiving rewards for good actions and penalties for bad ones. |
| **Classification** | A supervised learning task that predicts a discrete category/class. |
| **Regression** | A supervised learning task that predicts a continuous numerical value. |
| **Clustering** | An unsupervised learning task that groups similar data points together. |
| **Dimensionality Reduction** | An unsupervised technique that reduces the number of features while preserving important information. |
| **Agent** | The learner/decision-maker in reinforcement learning. |
| **Environment** | The external system the RL agent interacts with. |
| **State** | The current situation of the agent in the environment. |
| **Action** | A decision or move the agent can make. |
| **Reward** | A numerical signal indicating how good an action was. |
| **Policy** | The strategy the agent learns — mapping states to actions. |
| **Data Preprocessing** | Cleaning, transforming, and organising raw data before feeding it to an ML algorithm. |
| **Missing Value Imputation** | The process of replacing missing data with substitute values (mean, median, mode, etc.). |
| **Imbalanced Data** | A dataset where class distribution is significantly unequal. |
| **Feature Engineering** | Creating, transforming, or selecting features using domain knowledge to improve model performance. |
| **Overfitting** | When a model memorises training data instead of learning general patterns, resulting in poor performance on new data. |

---

## 26. Interview Questions

### Q1: What is Machine Learning?

**Answer:** Machine Learning is a branch of computer science and artificial intelligence where computer programs are designed to automatically improve their performance at a specific task by learning from data (experience), without being explicitly programmed with rules for that task.

---

### Q2: What is a well-posed learning problem? Explain with an example.

**Answer:** A well-posed learning problem is one where we can clearly define three components:
- **T (Task):** What the program needs to do.
- **E (Experience):** What data the program learns from.
- **P (Performance Measure):** How we measure if the program is improving.

**Example — Spam Detection:**
- T: Classify emails as spam or not spam.
- E: A database of 50,000 emails labelled as spam / not spam.
- P: Percentage of emails correctly classified.

The program is said to "learn" if its accuracy (P) at classifying emails (T) improves as it trains on more labelled emails (E).

---

### Q3: What is the difference between traditional programming and machine learning?

**Answer:** In traditional programming, humans provide the rules and data; the computer applies the rules to produce output. In machine learning, humans provide data and the expected output; the computer discovers the rules (model) by itself. Traditional programming is rule-driven; ML is data-driven.

---

### Q4: What is supervised learning? Give an example.

**Answer:** Supervised learning is a type of ML where the algorithm learns from labelled data — data that includes both input features and the correct output (label). The model learns the mapping from input to output.

**Example:** Predicting house prices — features include area, bedrooms, and location; the label is the price. The model learns from historical house sale data to predict prices of new houses.

---

### Q5: What is the difference between classification and regression?

**Answer:** Both are supervised learning tasks.
- **Classification** predicts a **discrete category** (e.g., spam/not spam, cat/dog). The output is one of a fixed set of classes.
- **Regression** predicts a **continuous numerical value** (e.g., price = ₹52,00,000, temperature = 34.7°C). The output is a number.

---

### Q6: What is unsupervised learning? Give an example.

**Answer:** Unsupervised learning is a type of ML where the algorithm learns from unlabelled data — data without any predefined categories or answers. The algorithm finds hidden patterns or structure on its own.

**Example:** Customer segmentation — given customer purchasing data without any predefined categories, the algorithm groups similar customers together into clusters (e.g., budget buyers, premium buyers).

---

### Q7: What is reinforcement learning?

**Answer:** Reinforcement learning is a type of ML where an agent learns to make decisions by interacting with an environment. The agent takes actions, receives rewards (positive feedback) or penalties (negative feedback), and learns a policy (strategy) that maximises cumulative reward over time. Unlike supervised learning, no correct answer is provided — the agent must discover good behaviour through trial and error.

---

### Q8: What is the difference between a feature and a label?

**Answer:** A **feature** is an input variable — a measurable property that the model uses to make predictions (e.g., area, number of bedrooms). A **label** is the output variable — the value the model is trying to predict (e.g., house price). During training, both are provided; during prediction, only features are given and the model must predict the label.

---

### Q9: Why is data preprocessing important?

**Answer:** Real-world data is often messy — it contains missing values, inconsistent formats, outliers, and irrelevant features. Most ML algorithms cannot handle such data directly. Preprocessing cleans and transforms the data, which directly impacts model performance. Poor data leads to poor models, regardless of the algorithm used.

---

### Q10: What is feature engineering and why is it important?

**Answer:** Feature engineering is the process of using domain knowledge to create, transform, or select features that help ML models learn better. It matters because the quality of features directly determines what the model can learn. Good features can dramatically improve model performance, while poor features can make even the best algorithms fail.

---

### Q11: What is the problem with imbalanced datasets?

**Answer:** In imbalanced datasets, one class has significantly more examples than the other. This causes the model to be biased toward the majority class. For example, in fraud detection where 99.5% of transactions are genuine, a model that always predicts "genuine" achieves 99.5% accuracy but catches zero fraud. Accuracy becomes misleading, and we need alternative metrics like Precision, Recall, and F1-Score.

---

### Q12: What is overfitting?

**Answer:** Overfitting occurs when a model learns the training data too well — including its noise and specific details — and fails to generalise to new, unseen data. It performs excellently on training data but poorly on test data. It's like a student who memorises answers but cannot solve new questions.

---

## 27. Module 1 Quick Revision Sheet

### Core Concept
Machine Learning = Programs that improve at a task through experience (data), without explicit programming.

### T-E-P Framework
| | |
|---|---|
| **T** | Task — What to do |
| **E** | Experience — Data to learn from |
| **P** | Performance — How to measure improvement |

### Traditional Programming vs ML
```
Traditional:  Data + Rules     → Output
ML:           Data + Output    → Learned Model (Rules)
```

### ML Workflow
```
Problem → Data Collection → Preprocessing → Feature Engineering
  → Choose Approach → Training → Validation/Testing → Evaluation → Deployment
```

### Three Types of Learning

| Type | Data | Key Idea |
|------|------|----------|
| Supervised | Labelled | Teacher gives answers |
| Unsupervised | Unlabelled | Discover patterns |
| Reinforcement | Rewards | Trial + feedback |

### Classification vs Regression
- Classification → "Which category?" → Discrete output
- Regression → "How much?" → Continuous number

### Key Terminology
| Term | Remember As |
|------|-------------|
| Feature | Input (what we know) |
| Label | Output (what we predict) |
| Algorithm | The learning method |
| Model | The result of learning |
| Training | Learning phase |
| Testing | Evaluation phase |

### Data Preprocessing
- **Missing values** → Impute using mean / median / mode or remove
- **Imbalanced data** → Accuracy is misleading; use oversampling / undersampling / better metrics
- **Feature engineering** → Create / transform / select features using domain knowledge

### RL Key Terms
Agent → Environment → State → Action → Reward → Policy

### Exam Essentials
1. Tom Mitchell's definition of ML (the T-E-P definition).
2. Difference between traditional programming and ML (with diagrams).
3. Three types of ML with examples.
4. Classification vs Regression with examples.
5. Supervised vs Unsupervised vs Reinforcement comparison table.
6. Why data preprocessing is important.
7. Feature vs Label distinction.
8. Well-posed learning problem examples with T, E, P identified.

---

> **End of Module 1: Foundations of Machine Learning**
>
> These notes cover the complete Module 1 syllabus as per course 24CS3503.
> Next module topics (Regression models, Gradient Descent, etc.) will be covered separately.
