# Day 1 · What is machine learning?

- **Status:** ⏳ in progress
- **Course:** Workbook Part 1 (Day 1) · notebook `Day1_What_is_ML.ipynb`
- **Watch:** [How to use Kaggle Notebooks](https://www.youtube.com/watch?v=BPQDZLPtgDc) (4 min) · [ML Zero to Hero, Part 1](https://www.youtube.com/watch?v=KNAWp2S3w94) (7 min)
- **Time:** about 2 hours

## 1. The big idea

In normal programming, **you write the rules**:

```text
rules + data    →  answers
```

In machine learning, you give **examples together with the correct answers**, and the computer **finds the rules** itself:

```text
data + answers  →  rules (the "model")
```

**Why Project 2 needs this.** Try writing `if` statements that separate a healthy tomato leaf from one with early blight. A 224 × 224 colour photo is **224 × 224 × 3 = 150,528 numbers**. The light changes, the angle changes, every phone camera is different, and the disease looks different at each stage. Nobody can write those rules by hand. We *can* collect thousands of labelled photos and let a model learn them.

## 2. Vocabulary, using our project

| Term | Plain meaning | In Project 2 |
|------|---------------|--------------|
| **Example** | one item to learn from | one photo of a leaf |
| **Feature** | something measured about the example | the photo's pixel values |
| **Label** | the correct answer for that example | "tomato · early blight" |
| **Model** | the rules the computer learned | MobileNetV2, fine-tuned on leaf photos |
| **Training** | showing labelled examples so the model improves | on a free cloud GPU (Colab or Kaggle) |
| **Prediction** (or *inference*) | the model's answer for a new example | on the farmer's phone, offline |

> **Labels are the truth the model trusts.** Mislabelled photos teach the model wrong rules, and its accuracy score won't warn you. Our labels must come from people who know plant diseases (IRAD is the planned partner), so careful labelling is real research work.

## 3. Three families of machine learning

| Family | Learns from | Example |
|--------|-------------|---------|
| **Supervised** | examples *with* labels | leaf photo → disease name (**our project**) |
| **Unsupervised** | examples *without* labels, by finding groups | sorting thousands of unlabelled field photos into look-alike groups before an expert labels them |
| **Reinforcement** | trial and error, with a reward | Project 3's smart greenhouse learning when to water |

## 4. The learning loop

Training repeats four steps, thousands of times:

1. **Guess**: predict with the current settings.
2. **Measure**: compare the guesses with the labels to see how wrong they were. That number is the *loss* (Day 2).
3. **Adjust**: change the settings a little so the model is less wrong. That is *gradient descent* (Day 2).
4. **Repeat.**

Today's model has 3 settings. MobileNetV2 has millions. It's the same loop.

## 5. Run the notebook

I run the course in Zed ([setup](../SETUP.md)).

1. Open `Zed_Notebooks_Days1-10/Day1_What_is_ML.py`. Put the cursor in a cell and press **Ctrl+Shift+Enter** to run it. Go cell by cell and read each output.
   *(In Colab instead: **File → Upload notebook**, pick `Day1_What_is_ML.ipynb`, and run cells with **Shift + Enter**.)*
2. **Predict before you run.** Write down your guesses:
   - Section 3: how often will the rule `weight > 210` be right?
   - Section 4: will the learned model beat that rule?
   - Section 5: is the 260 g fruit an orange or a mango, and how sure is the model?
3. Do the notebook's **Your turn** tasks 1–3.

<details>
<summary><b>Tutor's run</b> (open only after your own run)</summary>

- `X.shape` is `(120, 2)`: 120 fruits, with 2 features each.
- The rule `weight > 210` is right **100%** of the time because the two groups don't overlap: the heaviest orange weighs 182 g and the lightest mango 216 g.
- The model is also 100% right on these fruits. On easy data, a hand-written rule is enough.
- The 260 g fruit → **mango**, with probability ≈ 1.00.
- Near the border the model hesitates: a 200 g fruit with smoothness 5 gets P(mango) = 0.55.
- **Challenge hint:** after you retrain with three fruits, read *every* line of output, including warnings. Then compare how big the weight numbers are (hundreds) with the smoothness numbers (0 to 10).

</details>

## 6. Two extra experiments

Add each experiment as a new cell at the end of your Day 1 file: type a new `# %%` line, paste the code under it, and run it. Each one builds its own data, so your earlier changes don't matter. **Guess the result before you run it.**

### Experiment 1: The banana problem

The model only knows oranges and mangoes. What will it say about bananas?

```python
import numpy as np
from sklearn.linear_model import LogisticRegression

rng = np.random.default_rng(42)
n = 60
orange = np.c_[rng.normal(150, 15, n), rng.normal(3, 1.0, n)]
mango  = np.c_[rng.normal(280, 30, n), rng.normal(7, 1.2, n)]
X = np.vstack([orange, mango])
y = np.array([0]*n + [1]*n)
model = LogisticRegression().fit(X, y)          # knows only orange (0) and mango (1)

banana = np.c_[rng.normal(120, 15, n), rng.normal(8, 1.0, n)]   # light and very smooth
print("Predictions for 60 bananas:", model.predict(banana))
print("Average confidence:", round(model.predict_proba(banana).max(axis=1).mean(), 3))
```

### Experiment 2: When the groups overlap

Now the mangoes are lighter, so their weights overlap with the oranges'.

```python
import numpy as np
from sklearn.linear_model import LogisticRegression

rng = np.random.default_rng(42)
n = 60
orange = np.c_[rng.normal(150, 15, n), rng.normal(3, 1.0, n)]
mango  = np.c_[rng.normal(190, 30, n), rng.normal(7, 1.2, n)]   # lighter mangoes: weights now overlap
X = np.vstack([orange, mango])
y = np.array([0]*n + [1]*n)

print("Rule 'weight > 210'  :", round(((X[:, 0] > 210) == y).mean(), 3))
print("Rule 'smoothness > 5':", round(((X[:, 1] > 5) == y).mean(), 3))
model = LogisticRegression().fit(X, y)
print("Learned model        :", round(model.score(X, y), 3))

banana = np.c_[rng.normal(120, 15, n), rng.normal(8, 1.0, n)]   # the same bananas as Experiment 1
print("Bananas now predicted as:", model.predict(banana))
```

**Bonus:** after Experiment 2, re-run the plot cell in section 6 of the notebook and see which way the boundary turns.

<details>
<summary><b>Tutor's results</b> (open after running both)</summary>

**Experiment 1:** all 60 bananas → **orange** (0), with an average confidence of **1.0**, i.e. 100%.

**Experiment 2:**

| Approach | Right on these fruits |
|----------|----------------------|
| hand rule `weight > 210` | 60.8% |
| hand rule `smoothness > 5` | 98.3% |
| learned model (both features) | 100% |

The model worked out by itself that smoothness is now the better clue. In the plot, the boundary turns from almost vertical (a weight cut near 200 g) into a slanted line that mostly follows smoothness.

The *same* bananas are now called **mango**: 59 of the 60.

⚠️ These scores were measured on the same fruits the model learned from, which makes them too optimistic. Day 5 explains why.

</details>

## 7. Self-test

**Workbook:** answer Day 1 questions 1–4 (page 6) **before** you look at the answer key (page 20).

**Tutor's questions (Project 2):**

1. In our app, what is one *example*, what are its *features*, and what is its *label*?
2. Why can a hand-written rule separate oranges from mangoes, but not healthy leaves from diseased ones?
3. In Experiment 1, the model was 100% sure the bananas were oranges. Why couldn't it say "I don't know"?
4. In Experiment 2, the *same* bananas got a different answer. What does that tell you about trusting a model on inputs it was never trained on?
5. A farmer photographs a cassava leaf with our app. Would the rule "only show a diagnosis when confidence is above 90%" protect us? Use Experiment 1 to explain, and propose a better idea.
6. **Video:** asked about x = 10, the Hello World model answers close to 19, but not exactly 19. Why?

**Check-in:** write down one question to bring to Dr. Fotsing.

## 8. Takeaway for Project 2

<details>
<summary>Open after you've written your answers</summary>

A classifier can only choose from the classes it was trained on. It has no "I don't know" answer. Its confidence means "orange *rather than* mango". It does **not** mean "this is a fruit I recognise".

Our model will do the same thing with a cassava leaf, a hand, or a blurry photo. It will still name one of our diseases, possibly at 99%, and a model trained on slightly different data may name a different one (Experiment 2). So a confidence threshold alone can't protect farmers.

Handling unknown photos is therefore a **design requirement** we plan from the start. For example: an extra "not one of our crops" class trained on real photos of other things, a test set full of such photos, and a "retake the photo" message in the app.

</details>

## 9. My answers and reflection

*Coming next: my answers, the tutor's feedback, and what I learned.*
