# 🗺️ Learning Roadmap

**Destination:** build Project 2's AI model, a crop-disease classifier for tomato, tea, coffee and cocoa. It must run **offline on a cheap Android phone** and stay accurate on **real field photos**.

This plan follows Dr. Fotsing Kuetche's course. I update it as new materials arrive.

## Why "reliable" and "efficient"

Two results from the research group's kickoff set the bar:

- **Reliable:** a plant-disease model scored 99.35% on lab-style photos, but only 31.4% on photos taken in other conditions ([Mohanty et al., 2016](https://doi.org/10.3389/fpls.2016.01419)). A result only counts if it holds on messy, real-world photos.
- **Efficient:** a cassava-disease model ran offline on a phone in 50–200 ms ([Ramcharan et al., 2019](https://doi.org/10.3389/fpls.2019.00272)). Our target is a model under 8 MB that needs no internet.

## The four phases (from the kickoff)

1. **Learn and design**: tools, data, architecture ← *we are here*
2. **Build**: first working prototype
3. **Test and improve**: real leaves, real farmers
4. **Release and write**: open-source code, report, paper

Dates come from the team's Gantt chart.

## Phase 1 · Learn

### Week 1 · Foundations (Workbook Part 1, notebooks 1–5)

| Day | Topic | Watch | Why it matters for Project 2 |
|-----|-------|-------|------------------------------|
| 1 | What is machine learning? | [How to use Kaggle Notebooks](https://www.youtube.com/watch?v=BPQDZLPtgDc) (4 min) · [ML Zero to Hero 1](https://www.youtube.com/watch?v=KNAWp2S3w94) (7 min) | Our app is supervised learning: leaf photo in, disease label out |
| 2 | Learning from mistakes: loss and gradient descent | ML Zero to Hero 1 again: listen for *loss* and *optimizer* | Every model we train, MobileNetV2 included, learns with this loop |
| 3 | Yes or no: probabilities and thresholds | — | Deciding when the app is sure enough to show a diagnosis |
| 4 | Is the model any good? Precision and recall | — | A missed disease and a false alarm cost a farmer different things, and accuracy alone hides that |
| 5 | Data and overfitting | — | An honest train / validation / test split is the first defence against the 99% → 31% trap |

### Week 2 · Computer vision: from pixels to a model on a device (Workbook Part 2, notebooks 6–10)

The workbook calls **Day 9 the most important day of the course**.

| Day | Topic | Watch | Why it matters for Project 2 |
|-----|-------|-------|------------------------------|
| 6 | How computers see | [ML Zero to Hero 2](https://www.youtube.com/watch?v=bemDFpNooA8) (7 min) | A leaf photo is just numbers: 224 × 224 × 3 = 150,528 of them. For the photo exercise, I'll open my own phone photo of a leaf |
| 7 | Convolutions and CNNs | [ML Zero to Hero 3](https://www.youtube.com/watch?v=x_VrgWTKkiM) (6 min) | Filters learn spots, rings and textures, which is what disease looks like |
| 8 | Training for the real world | [ML Zero to Hero 4](https://www.youtube.com/watch?v=u2TjZzNuly8) (5 min) | Transfer learning with MobileNetV2 is exactly our project's recipe |
| 9 | Testing the truth | — | Lab vs field photos: test every class and every condition separately |
| 10 | Shrink and ship | — | Quantise to TensorFlow Lite so the model fits and runs on the phone |

**Three points from Part 2 that apply directly to Project 2:**

- **Colour can be the symptom (Day 8).** The workbook warns that strong colour changes can harm training when colour is what tells healthy from sick. On leaves it often is: yellowing, brown spots, the orange powder of coffee leaf rust. Flips, small rotations, zoom and brightness changes are safe; strong colour shifts are not.
- **Test by group and keep a failure folder (Day 9).** For us, the groups are crop, disease, phone model, light (sun, shade, cloud), region and season. The photos the model gets wrong tell us what to collect next.
- **We can't skip quantisation (Day 10).** Rule of thumb: 1 million weights take about 4 MB in float32 and 1 MB in int8. MobileNetV2 without its ImageNet head has 2,257,984 weights, so about 9 MB in float32, which is already over our 8 MB target. In int8 that drops to about 2.3 MB. I'll check this on Day 10 by converting the real model.

## Phase 1 · Design (proposed; to confirm with Dr. Fotsing and my teammate)

1. Read and summarise the two papers above.
2. Survey datasets for our four crops. PlantVillage has tomato, but no tea, coffee or cocoa, so those need other public datasets plus field photos (IRAD is the planned partner).
3. Baseline: fine-tune MobileNetV2 on tomato, test it on field-style photos, and measure the drop.
4. Export to TensorFlow Lite (under 8 MB) and time it on a budget Android phone.
5. Agree the model ↔ app contract with the mobile teammate: input size, label list, confidence rule, and what happens with a photo of something unknown.

## ❓ Open questions for the check-ins

- **Which part of the plant do we photograph?** Cocoa's most damaging disease in Cameroon, black pod, shows on the **pods**. Coffee berry disease attacks the **berries**. Neither shows mainly on leaves.
- **Which 5–7 diseases per crop?** They should be the ones that are actually common in Cameroon, chosen with agronomists.
- **Over 90% accuracy, but on which test set?** Lab-style photos or real field photos? (See the 99% → 31% result above.)
- **Which languages?** The project sheet says French plus a local language; the kickoff says French and English.
- **What does the app do with a photo of something else?** See [Day 1, Experiment 1](daily-log/day-01.md#experiment-1-the-banana-problem).

## 🧰 Tools and tips

- I run the course notebooks locally in **Zed** ([setup](SETUP.md)). **Colab** and **Kaggle** give free GPU time for Day 8, and Kaggle hosts many plant-disease datasets.
- The videos are from 2019 and use some older APIs (such as `ImageDataGenerator`). The course notebooks use the modern ones (`image_dataset_from_directory`, `Random*` layers); prefer those.
- Google renamed TensorFlow Lite to **LiteRT** in 2024. It is the same `.tflite` format, so search docs under both names.
