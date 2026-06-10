<img width="945" height="522" alt="mentalhealth" src="https://github.com/user-attachments/assets/052ff7e4-fa01-4fc0-8cfe-62ba7ec98f5c" />

# Analyzing Students' Mental Health

> **SQL data analysis project** — Exploring factors that influence the mental health of international students at a Japanese university.

---

## Project Overview

This project analyzes survey data collected in 2018 by a Japanese international university, published the following year and approved by several ethical and regulatory boards.

The original study found that **international students have a higher risk of mental health difficulties** than the general population, and that two factors are especially predictive of depression:

- **Social connectedness** — sense of belonging to a social group
- **Acculturative stress** — stress associated with adapting to a new culture

The goal of this project is to verify whether the data supports those conclusions and whether **length of stay is a contributing factor**.

---

## Data Description

| Field | Description |
|---|---|
| `inter_dom` | Student type (international or domestic) |
| `japanese_cate` | Japanese language proficiency |
| `english_cate` | English language proficiency |
| `academic` | Academic level (undergraduate or graduate) |
| `age` | Current age of the student |
| `stay` | Current length of stay in years |
| `todep` | Total depression score (PHQ-9 test) |
| `tosc` | Total social connectedness score (SCS test) |
| `toas` | Total acculturative stress score (ASISS test) |

---

## Analyses

### 1. Length of Stay and Mental Health

```sql
SELECT stay,
       COUNT(*) AS count_int,
       ROUND(AVG(todep), 2) AS average_phq,
       ROUND(AVG(tosc), 2) AS average_scs,
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom = 'Inter'
GROUP BY stay
ORDER BY stay DESC
```

**Results:**

| Length of Stay (years) | No. Students | Avg. Depression (PHQ) | Avg. Social Connectedness (SCS) | Avg. Stress (AS) |
|---|---|---|---|---|
| 10 | 1 | 13.00 | — | — |
| 8 | 1 | 10.00 | — | — |
| 7 | 1 | 4.00 | — | — |
| 6 | 3 | 6.00 | — | — |
| 5 | 1 | 0.00 | — | — |
| 4 | 14 | 8.57 | — | — |
| 3 | 46 | 9.09 | — | — |
| 2 | 39 | 8.28 | — | — |
| 1 | 95 | 7.48 | — | — |

> Groups with longer stays have very small samples (1–3 students), so results should be interpreted with caution.

---

### 2. Social Connectedness and Depression

```sql
SELECT
    CASE
        WHEN tosc <= 24 THEN 'Low social connectedness (0-24)'
        WHEN tosc BETWEEN 25 AND 36 THEN 'Medium social connectedness (25-36)'
        ELSE 'High social connectedness (+37)'
    END AS social_connectedness,
    COUNT(*) AS students,
    ROUND(AVG(todep), 2) AS avg_depression
FROM students
WHERE inter_dom = 'Inter'
GROUP BY social_connectedness
ORDER BY social_connectedness DESC
```
**Results:**

|conectividade_social|estudantes|depressao_media
|---|---|---|
Media conectividade social (25-36)|54|8.93
Baixa conectividade social (0-24)|22|14.73
Alta conectividade social +37|125|6.49

Results show a **clear inverse relationship** between social connectedness and depression: students with low social connectedness have significantly higher depression scores.

---

### 3. Gender and Religion

```sql
SELECT gender,
       religion,
       COUNT(*) AS student_count,
       ROUND(AVG(todep), 2) AS average_phq,
       ROUND(AVG(tosc), 2) AS average_scs,
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom = 'Inter'
GROUP BY gender, religion
ORDER BY gender, average_phq DESC
```

**Results:**

| Gender | Religion | No. Students |
|---|---|---|
| Female | No | 86 |
| Female | Yes | 42 |
| Male | No | 40 |
| Male | Yes | 33 |

---

### 4. International vs. Domestic Students

```sql
SELECT inter_dom,
       COUNT(*) AS total_students,
       ROUND(AVG(todep), 2) AS average_phq,
       ROUND(AVG(tosc), 2) AS average_scs,
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom IN ('Dom', 'Inter')
GROUP BY inter_dom
```

**Results:**

| Student Type | Total | Avg. Depression | Avg. Social Connectedness | Avg. Stress |
|---|---|---|---|---|
| International | 201 | 8.04 | 37.42 | 75.56 |
| Domestic | 67 | 8.61 | 37.64 | 62.84 |

> International students show **substantially higher acculturative stress** (75.56 vs. 62.84), confirming the original study's findings.

---

### 5. Japanese Language Proficiency

```sql
SELECT japanese_cate,
       COUNT(*) AS student_count,
       ROUND(AVG(todep), 2) AS average_phq,
       ROUND(AVG(tosc), 2) AS average_scs,
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom = 'Inter'
GROUP BY japanese_cate
ORDER BY average_phq
```

**Results:**

| Japanese Level | No. Students | Avg. Depression | Avg. Social Connectedness | Avg. Stress |
|---|---|---|---|---|
| High | 25 | 7.40 | 38.48 | 72.00 |
| Low | 91 | 7.91 | 36.99 | 76.65 |
| Average | 85 | 8.38 | 37.56 | 75.45 |

> Students with **higher Japanese proficiency** tend to show lower depression and acculturative stress, suggesting that language integration plays a protective role.

---

### 6. Age Impact

```sql
SELECT age,
       COUNT(*) AS student_count,
       ROUND(AVG(todep), 2) AS average_phq,
       ROUND(AVG(tosc), 2) AS average_scs,
       ROUND(AVG(toas), 2) AS average_as
FROM students
WHERE inter_dom = 'Inter'
GROUP BY age
ORDER BY age DESC
```

**Results:**

| Age | No. Students | Avg. Depression (PHQ) | Avg. Social Connectedness (SCS) | Avg. Stress (AS) |
|---|---|---|---|---|
| 31 | 4 | 5.75 | 43.50 | 80.25 |
| 30 | 3 | 9.33 | 41.00 | 97.33 |
| 29 | 4 | 3.75 | 43.00 | 63.50 |
| 28 | 3 | 3.33 | 39.00 | 71.00 |
| 27 | 1 | 10.00 | 35.00 | 42.00 |
| 25 | 9 | 6.11 | 37.33 | 80.78 |
| 24 | 6 | 4.67 | 42.33 | 74.33 |
| 23 | 12 | 9.67 | 32.00 | 81.25 |
| 22 | 14 | 8.36 | 38.14 | 70.43 |
| 21 | 39 | 9.23 | 37.74 | 75.23 |
| 20 | 34 | 7.35 | 38.21 | 73.26 |
| 19 | 41 | 8.44 | 37.90 | 74.10 |
| 18 | 28 | 8.75 | 34.11 | 80.61 |
| 17 | 3 | 4.67 | 37.33 | 70.67 |


---

## Key Findings

1. **Length of stay appears to influence depression levels**, though small sample sizes for stays beyond 4 years limit the reliability of those results.

2. **Social connectedness is a strong predictor of depression** — students with low social connectedness consistently show higher depression scores, confirming the original study.

3. **Acculturative stress is markedly higher in international students** (75.56 vs. 62.84 for domestic), corroborating the project's central premise.

4. **Japanese language proficiency is associated with lower depression and stress**, suggesting that cultural integration through language has a protective effect.

5. **Younger students (ages 18–23) show the highest acculturative stress levels**, possibly because it is their first experience of living independently abroad.

---

## Technologies Used

- **PostgreSQL** — querying and data analysis
- **DataCamp DataLab** — notebook execution environment
- **SQL** — primary analysis language

---


## Reference

Original study published in 2019, based on a survey conducted in 2018 at an international university in Japan, approved by relevant ethical and regulatory bodies.
