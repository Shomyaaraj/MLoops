# Curriculum-Industry Skill Gap Analysis Using Feast

## Student Details

| Field | Details |
|---|---|
| **Name** | Shomya Raj |
| **Register Number** | *231FA04A51* |
| **Section** | *15* |

---

## Problem Statement

The curriculum-industry skill-gap problem focuses on identifying whether a student's academic knowledge, technical skills, practical experience, and placement preparation are aligned with industry requirements.

Students may have good academic performance but may still have gaps in areas such as coding, aptitude, communication, internships, projects, certifications, hackathons, and interview preparation. This project uses student-related features to predict the student's **Placement Status**.

Feast is used as a feature store so that the same feature definitions can be consistently used during model training and prediction. This helps reduce differences between the features used during training and those used during real-time inference.

---

# Dataset

## Number of Skills / Input Features

The dataset contains **17 input features** describing a student's academic, technical, professional, and extracurricular profile.

The 17 input features are:

1. Gender
2. Age
3. Attendance_Percentage
4. CRT_Attendance_Percentage
5. Practice_Hours_Per_Week
6. Previous_Sem_CGPA
7. Internal_Marks
8. Verbal_Score
9. Aptitude_Score
10. Coding_Score
11. Internships_Completed
12. Certifications
13. Hackathons_Participated
14. Leadership_Activities
15. Mock_Interview_Score
16. Projects_Completed
17. Communication_Skills

## Dataset Columns

| Column | Description |
|---|---|
| `Student_ID` | Unique identifier for each student |
| `Gender` | Gender of the student |
| `Age` | Age of the student |
| `Attendance_Percentage` | Overall academic attendance percentage |
| `CRT_Attendance_Percentage` | Career/placement training attendance percentage |
| `Practice_Hours_Per_Week` | Number of hours spent practicing per week |
| `Previous_Sem_CGPA` | CGPA obtained in the previous semester |
| `Internal_Marks` | Internal assessment marks |
| `Verbal_Score` | Verbal ability score |
| `Aptitude_Score` | Aptitude assessment score |
| `Coding_Score` | Coding/technical assessment score |
| `Internships_Completed` | Number of internships completed |
| `Certifications` | Number of certifications obtained |
| `Hackathons_Participated` | Number of hackathons participated in |
| `Leadership_Activities` | Participation in leadership activities |
| `Mock_Interview_Score` | Score obtained in a mock interview |
| `Projects_Completed` | Number of projects completed |
| `Communication_Skills` | Communication skill score |
| `Placement_Status` | Final placement outcome and prediction target |

## Target

The target variable is:

```text
Placement_Status
```

The target represents whether a student was placed or not placed.

`Student_ID` is used as the **entity key** and is not used as a prediction feature.

## How the Entries Were Created

Each row represents one student and contains academic, technical, extracurricular, and placement-preparation information.

For example:

```text
Student_ID = 1001
Gender = Male
Age = 20
Attendance_Percentage = 72
Previous_Sem_CGPA = 6.13
Aptitude_Score = 89
Coding_Score = 34
Placement_Status = Placed
```

The numerical values represent measurable student characteristics such as attendance, CGPA, assessment scores, practice hours, internships, certifications, projects, and interview performance.

The `Placement_Status` column represents the final placement outcome and is used as the machine-learning target.

---

# Feature Engineering

The following features are managed through the Feast FeatureView.

| Feast Feature | Meaning |
|---|---|
| `Gender` | Gender of the student |
| `Age` | Age of the student |
| `Attendance_Percentage` | Percentage of academic attendance |
| `CRT_Attendance_Percentage` | Percentage of attendance in career/placement training |
| `Practice_Hours_Per_Week` | Number of hours spent practicing each week |
| `Previous_Sem_CGPA` | CGPA obtained in the previous semester |
| `Internal_Marks` | Internal assessment marks |
| `Verbal_Score` | Verbal ability score |
| `Aptitude_Score` | Aptitude assessment score |
| `Coding_Score` | Coding or technical assessment score |
| `Internships_Completed` | Number of internships completed |
| `Certifications` | Number of certifications obtained |
| `Hackathons_Participated` | Number of hackathons participated in |
| `Leadership_Activities` | Participation in leadership activities |
| `Mock_Interview_Score` | Performance score in mock interviews |
| `Projects_Completed` | Number of completed projects |
| `Communication_Skills` | Communication ability score |

> **Note:** `Placement_Status` is the target/label and is not included as an input feature in the FeatureView to avoid target leakage.

---

# Feast Architecture

```text
                         Original Dataset
                                |
                                v
                       Feature Engineering
                                |
                                v
                       Parquet Offline Data
                                |
                                v
                       Feast FeatureView
                                |
                   +------------+------------+
                   |                         |
                   v                         v
          Historical Features         Materialization
                   |                         |
                   v                         v
            Model Training             Online Store
                                             |
                                             v
                                      Online Retrieval
                                             |
                                             v
                                         Prediction
```

## Architecture Explanation

1. **Original Dataset**  
   The raw student dataset contains academic, technical, extracurricular, and placement-related information.

2. **Feature Engineering**  
   The data is cleaned and prepared into features suitable for machine-learning and feature-store management.

3. **Parquet Offline Data**  
   The processed feature data is stored in Parquet format. This provides the historical data source for Feast.

4. **Feast FeatureView**  
   The FeatureView defines which student features Feast manages and makes available for training and serving.

5. **Historical Features**  
   Historical feature values are retrieved from the offline store and used to construct the training dataset.

6. **Model Training**  
   The retrieved historical features are provided to the machine-learning model along with the target `Placement_Status`.

7. **Materialization**  
   Materialization copies the required feature values from the offline store into the online store.

8. **Online Store**  
   The online store contains the latest feature values and supports low-latency feature retrieval.

9. **Online Retrieval**  
   During prediction, the application retrieves the required features for a student from the online store.

10. **Prediction**  
    The retrieved features are passed to the trained model to predict the student's placement status.

---

# Implementation

## Entity

The entity used in the Feast implementation is:

```text
Student_ID
```

`Student_ID` uniquely identifies each student and associates the student's feature values with that student.

For example:

```text
Student_ID = 1001
```

represents one student's feature record.

---

## Data Source

The processed feature data is stored in a **Parquet file**.

The Parquet file is configured as the data source for the Feast FeatureView and provides the historical feature values required for training.

---

## FeatureView

The Feast FeatureView defines the student-related features that are managed by Feast.

Conceptually:

```text
Student_ID
    |
    +-- Gender
    +-- Age
    +-- Attendance_Percentage
    +-- CRT_Attendance_Percentage
    +-- Practice_Hours_Per_Week
    +-- Previous_Sem_CGPA
    +-- Internal_Marks
    +-- Verbal_Score
    +-- Aptitude_Score
    +-- Coding_Score
    +-- Internships_Completed
    +-- Certifications
    +-- Hackathons_Participated
    +-- Leadership_Activities
    +-- Mock_Interview_Score
    +-- Projects_Completed
    +-- Communication_Skills
```

The FeatureView provides a centralized definition of the features used for both training and serving.

---

## Historical Retrieval

Historical features are retrieved through Feast from the offline data source.

The retrieved features are combined with the corresponding `Placement_Status` target to create the model-training dataset.

The general flow is:

```text
Student ID
    |
    v
Feast Historical Retrieval
    |
    v
Historical Features
    |
    v
Training Dataset
    |
    v
Machine Learning Model
```

This allows the same feature definitions managed by Feast to be used during model development.

---

## Model

A machine-learning classification model is trained using the historical Feast features.

The model predicts:

```text
Placement_Status
```

The prediction can be either:

```text
Placed
```

or:

```text
Not Placed
```

**Model used:** *Enter the actual model used in your implementation, for example Random Forest / Logistic Regression / Decision Tree.*

---

## Online Retrieval

After materialization, the latest feature values are available in the Feast online store.

For a given student, the application uses `Student_ID` to retrieve the student's features.

The flow is:

```text
Student_ID
    |
    v
Feast Online Store
    |
    v
Latest Student Features
    |
    v
Machine Learning Model
    |
    v
Placement Prediction
```

This avoids manually recalculating and preparing the features separately during inference.

---

# Results

## 1. Historical Feature Output

An example of the historical feature output retrieved through Feast is shown below:

| Student_ID | Age | Attendance_Percentage | Previous_Sem_CGPA | Aptitude_Score | Coding_Score | Projects_Completed | Communication_Skills |
|---:|---:|---:|---:|---:|---:|---:|---:|
| 1001 | 20 | 72 | 6.13 | 89 | 34 | 0 | 75 |
| 1002 | 24 | 81 | 8.15 | 55 | 84 | 1 | 45 |

The exact output may contain all features registered in the FeatureView.

---

## 2. Model Accuracy

The trained model achieved:

```text
Accuracy: XX%
```

**Replace `XX%` with the actual accuracy obtained from your model evaluation.**

For example, if your model output is:

```text
Accuracy: 0.85
```

then the result should be reported as:

```text
Model Accuracy: 85%
```

---

## 3. Online Feature Output

After materialization, features for a student can be retrieved from the Feast online store.

Example for `Student_ID = 1001`:

| Feature | Retrieved Value |
|---|---:|
| `Age` | 20 |
| `Attendance_Percentage` | 72 |
| `CRT_Attendance_Percentage` | 55 |
| `Practice_Hours_Per_Week` | 7 |
| `Previous_Sem_CGPA` | 6.13 |
| `Internal_Marks` | 46 |
| `Verbal_Score` | 46 |
| `Aptitude_Score` | 89 |
| `Coding_Score` | 34 |
| `Internships_Completed` | 0 |
| `Certifications` | 1 |
| `Hackathons_Participated` | 1 |
| `Leadership_Activities` | 1 |
| `Mock_Interview_Score` | 94 |
| `Projects_Completed` | 0 |
| `Communication_Skills` | 75 |

---

## 4. Final Prediction

For the final prediction, use the actual output produced by your trained model.

Example format:

```text
Student_ID: 1001
Prediction: Placed
```

The final prediction is generated by retrieving the student's features from the Feast online store and passing those features to the trained classification model.

> **Final Prediction:** `Placed` *(replace this with your actual model output if different).* 

---

# Conclusion

This project demonstrates how Feast can be used to manage student-related machine-learning features for a curriculum-industry skill-gap and placement prediction problem.

The offline store supports historical feature retrieval for model training, while the online store provides fast access to the latest features during prediction. Using Feast provides a consistent feature pipeline and reduces the risk of training-serving skew between model training and real-time inference.

---

# Feast Questions and Answers

## 1. What is the entity in your Feast implementation?

The entity in the Feast implementation is `Student_ID`.

Each student is uniquely identified using `Student_ID`, and the features in the FeatureView are associated with that student.

For example:

```text
Student_ID = 1001
```

represents one student.

---

## 2. List the features stored in your FeatureView.

The FeatureView stores the following student-related input features:

- `Gender`
- `Age`
- `Attendance_Percentage`
- `CRT_Attendance_Percentage`
- `Practice_Hours_Per_Week`
- `Previous_Sem_CGPA`
- `Internal_Marks`
- `Verbal_Score`
- `Aptitude_Score`
- `Coding_Score`
- `Internships_Completed`
- `Certifications`
- `Hackathons_Participated`
- `Leadership_Activities`
- `Mock_Interview_Score`
- `Projects_Completed`
- `Communication_Skills`

`Student_ID` is the entity key, while `Placement_Status` is the target/label and is not used as an input feature.

---

## 3. Explain how one feature was calculated.

One possible engineered feature is `Overall_Score`, calculated as the average of the verbal, aptitude, coding, and communication scores:

```text
Overall_Score =
(Verbal_Score + Aptitude_Score + Coding_Score + Communication_Skills) / 4
```

For Student 1001:

```text
(46 + 89 + 34 + 75) / 4 = 61
```

Therefore, the calculated `Overall_Score` is `61`.

**Note:** If `Overall_Score` was not actually created in the implemented FeatureView, it should not be listed as an actual Feast feature.

---

## 4. What is the difference between your original dataset and the feature dataset?

The original dataset contains the raw student information collected from different sources.

The feature dataset contains cleaned, transformed, and model-ready features that are managed by Feast.

The feature dataset may contain the same useful attributes as the original dataset, but they are organized in a form suitable for machine-learning training and online serving. It can also contain engineered features created from the original columns.

In short:

```text
Original Dataset
    → Raw student information

Feature Dataset
    → Processed/model-ready features managed by Feast
```

---

## 5. What is the purpose of the offline store?

The offline store keeps historical feature data.

It is mainly used for retrieving historical features to create training datasets for machine-learning models.

```text
Offline Store
     ↓
Historical Features
     ↓
Model Training
```

---

## 6. What is the purpose of the online store?

The online store keeps the latest feature values and allows them to be retrieved quickly during real-time prediction.

```text
Online Store
     ↓
Latest Student Features
     ↓
Real-time Prediction
```

This avoids having to recalculate all features manually during inference.

---

## 7. What is the purpose of `feast apply`?

`feast apply` is used to apply the Feast configuration and create or update the objects defined in the Feast project.

This includes objects such as:

- Entities
- FeatureViews
- Data sources
- Other Feast definitions

For example:

```bash
feast apply
```

registers or updates the Feast definitions so that they can be used by the feature store.

---

## 8. What does materialization do?

Materialization copies feature data from the offline store into the online store.

For example:

```bash
feast materialize-incremental <timestamp>
```

can be used to load recent feature values into the online store.

The flow is:

```text
Offline Store
     ↓
Materialization
     ↓
Online Store
     ↓
Online Feature Retrieval
```

This makes the required features available for fast online inference.

---

## 9. What is the advantage of retrieving features through Feast instead of manually calculating them separately during training and prediction?

The main advantage is **consistency**.

If features are calculated manually, there is a risk that the feature calculation used during training will be different from the calculation used during prediction. This problem is known as **training-serving skew**.

Feast provides a centralized way of defining and retrieving features for both training and serving.

Advantages include:

- Reduces duplicate feature-calculation code
- Helps prevent training-serving skew
- Provides centralized feature management
- Makes features reusable
- Provides fast online feature retrieval
- Makes feature updates easier to manage

The overall idea is:

```text
                 Feast Feature
                  /        \\
                 /          \\
                ↓            ↓
        Model Training    Prediction
```

Both stages use the same feature definitions.

---

## 10. State two limitations of your current dataset.

### Limitation 1: Limited dataset size

The dataset contains a limited number of student records. Therefore, it may not represent the complete student population and may limit the ability of the model to generalize to new students.

### Limitation 2: Limited real-world industry features

The dataset contains academic, technical, extracurricular, and placement-preparation features, but it does not capture several real-world factors such as:

- Quality and relevance of projects
- Quality and relevance of internships
- Industry-specific skills
- Resume quality
- Company-specific requirements
- Current job-market conditions

Therefore, the dataset may not completely represent the actual placement process.

---

## 11. State two ways your feature store could be improved when more curriculum and industry evidence becomes available.

### Improvement 1: Add curriculum-based features

As more curriculum evidence becomes available, the FeatureView can be extended with features such as:

- Relevant course completion
- Core-subject performance
- Technical electives completed
- Skill proficiency
- Course-to-job relevance
- Performance in industry-oriented courses

This would make the feature store more closely aligned with the student's academic curriculum.

### Improvement 2: Add industry-based features

Industry evidence can be used to add features such as:

- In-demand technical skills
- Job-role-specific skill scores
- Industry certification relevance
- Internship relevance
- Project relevance to specific job roles
- Current employer skill requirements

This would allow the feature store to evolve with changing industry requirements.

---

# Quick Feast Summary

| Concept | Purpose |
|---|---|
| **Entity** | `Student_ID`, uniquely identifies a student |
| **FeatureView** | Defines and manages student features |
| **Offline Store** | Stores/retrieves historical features for training |
| **Online Store** | Stores latest features for fast prediction |
| **`feast apply`** | Applies/registers Feast definitions |
| **Materialization** | Copies features from offline store to online store |
| **Historical Retrieval** | Retrieves features for model training |
| **Online Retrieval** | Retrieves latest features for inference |
| **Target** | `Placement_Status` |
