# Laboratory-Work-5-Activity-Comparative-Analysis

# Google Collab: https://colab.research.google.com/drive/1dqIGs7VRWWRDJ0mISmvVYHgekBcfFYlb?usp=sharing
# Good Model: https://colab.research.google.com/drive/1yIEG2he15AhLqo6j0Zbu9RJg2w2SS5-f?usp=sharing
## 📊 Model Comparison Results
# Laboratory Work 5 — Comparative Analysis of Pre-trained CNN Models

## Dataset
- **Name:** Ornamental Plants
- **Classes:** 20
- **Total Images:** ~6,973
- **Images per class:** ~275–350

---

## Performance Comparison Table

| Model | Train Accuracy | Train Loss | Test Accuracy | Test Loss | Precision | Recall | F1-score | AUC Score |
|-------|---------------|------------|---------------|-----------|-----------|--------|----------|-----------|
| Teachable Machine | 0.9990 | 0.0100 | 0.9937 | 0.0445 | 0.9940 | 0.9937 | 0.9938 | 0.9998 |
| Good Model (LW4) | 0.9401 | 0.2619 | 0.9275 | 0.3003 | 0.9287 | 0.9279 | 0.9272 | 0.9863 |
| MobileNetV2 | 0.9138 | 0.3549 | 0.9268 | 0.3078 | 0.9272 | 0.9272 | 0.9263 | 0.9888 |
| LW4 2nd Model | 0.9681 | 0.2087 | 0.8730 | 0.5878 | 0.8832 | 0.8718 | 0.8727 | 0.9797 |
| LW3 1st Model | 0.8116 | 0.6194 | 0.8314 | 0.6069 | 0.8418 | 0.8321 | 0.8313 | 0.9605 |
| ResNet50 | 0.3750 | 2.1839 | 0.3895 | 2.1736 | 0.4516 | 0.3914 | 0.3494 | 0.8537 |
| EfficientNetB0 | 0.0626 | 3.0189 | 0.0538 | 3.1757 | 0.0027 | 0.0500 | 0.0051 | 0.6291 |

---
## Guide Questions
# A. Model Performance.<br><br>
Which pre-trained model achieved the highest accuracy? Why?
- Out of the three pre-trained models we tested, MobileNetV2 came out on top with a test accuracy of 92.68%. When we further improved it using two-phase fine-tuning, it became our Good Model reaching 92.75%. The reason it performed so well really comes down to compatibility — MobileNetV2 was designed to work with pixel values scaled between 0 and 1, which is exactly what our Rescaling(1./255) layer provides. Because the preprocessing matched perfectly, the model's pretrained ImageNet features activated immediately and started recognizing meaningful plant patterns from the very first epoch. You could see this in the training logs, it jumped to 61% validation accuracy in just the first epoch, which none of the other models came close to.<br><br>

2. Which model had the lowest performance? What could be the reason?<br><br>
- EfficientNetB0 was by far the worst performer, finishing with a test accuracy of just 5.38%, which is essentially the same as randomly guessing among 20 classes. What made this particularly frustrating is that EfficientNetB0 is actually a very powerful architecture. The problem was not the model itself but how we fed data into it. EfficientNetB0 already has its own internal preprocessing built in, so it expects raw pixel values between 0 and 255. But our code applied an extra rescaling layer on top of that, which pushed the pixel values down to an extremely tiny range that the model had never seen before. The pretrained features simply never activated, and the model gave up and predicted the same class, celosia, for literally every single image it saw. If you look at the confusion matrix, every row has all its values piled into the celosia column. It is a clear and vivid picture of a model that learned absolutely nothing.<br><br>

3. How did loss values compare across models?<br>
Referring to the table:<br>
<img width="888" height="351" alt="image" src="https://github.com/user-attachments/assets/45695f69-6017-4b8c-be83-4c381b58ddef" /><br>
- Looking at the table, the difference in loss values across models is quite dramatic and tells the story of each model's training quality. The Good Model had a train loss of 0.2619 and test loss of 0.3003 — both low and close together, which is exactly what we want. MobileNetV2 from LW5 was similarly healthy at 0.3549 train and 0.3078 test. On the other end of the spectrum, EfficientNetB0 ended with a train loss of 3.0189 and test loss of 3.1757 — values that barely moved throughout all six epochs of training, confirming the model was completely stuck. ResNet50 was stuck too, ending at around 2.18 for both train and test loss. One interesting thing to note about the LW4 2nd Model is that while it had a low train loss of 0.2087, its test loss ballooned to 0.5878 — a sign that the model was overfitting to training data. Only the Good Model truly nailed both low loss and consistency between training and testing.

# B. Evaluation Metrics<br><br>
4. Why is accuracy not enough to evaluate a model?<br>
- Accuracy sounds like the perfect metric, but it can be deeply misleading. The best example from our own experiment is EfficientNetB0 — it predicted celosia for every single input yet still registered a 5.38% accuracy simply because celosia made up roughly 5% of the validation set. If we only looked at accuracy, we might think it learned something. The EfficientNetB0 Confusion Matrix immediately exposes the truth: one bright column, complete darkness everywhere else.
In a 20-class problem like ours, accuracy treats all mistakes equally. It does not tell you whether the model is failing consistently on one tough species like Adelfa or spreading errors evenly. That is why we need precision (when the model says it is a certain plant, how often is it right?), recall (out of all actual images of that plant, how many did the model catch?), F1-score (the balance of both), and AUC (how well the model ranks correct predictions above incorrect ones at every threshold). Looking at the Precision, Recall, and F1-score per Class bar chart from Activity 1, you can see exactly which species drove the macro averages down and where targeted improvement is needed — something a single accuracy number could never show you.<br><br>

5. Which model had the best F1-score? What does it indicate?<br>
- From the summary table, the Good Model achieved the best F1-score among all student-trained models at 0.9272. The Model from TM topped the full table at 0.9938, but setting that aside, our Good Model's F1 of 92.72% is a strong result that means it is neither over-predicting nor missing too many true positives — it is balanced and thorough across all 20 classes.<br>
- This is confirmed in the Good Model Classification Report, where the macro average precision, recall, and F1 are all 0.93. Looking at the per-class breakdown, even the weakest classes like Adelfa (0.87) and Katakataka (0.89) still performed respectably, while standout classes like Yellow Bell (0.99) and Red Powder Puff (0.98) showed near-perfect F1 scores. The Confusion Matrix for the Improved Model reinforces this — the diagonal is bright and consistent from top to bottom, with only minor off-diagonal scatter in the few harder classes, a visual representation of a model that earns its F1-score honestly across every species. <br><br>

6. How did Precision and Recall differ across models?<br> 
- One of the most telling things about looking at precision and recall side by side is how they reflect each model's behavior.<br>
- The Good Model had precision at 0.9287 and recall at 0.9279 — nearly identical, showing a well-balanced model that is neither trigger-happy nor overly conservative. MobileNetV2 from LW5 showed the same healthy pattern at 0.9272 for both. ResNet50 showed a bigger gap at 0.4516 precision and 0.3914 recall, meaning it was more careful about what it predicted but still missed many actual positives. EfficientNetB0 had the most extreme imbalance — precision at 0.0027 and recall at 0.0500 — perfectly explained by the confusion matrix showing all predictions collapsing into celosia.<br>
- The per-class Precision, Recall, F1-score bar chart from Activity 1 makes this comparison especially clear for the Good Model, showing how tightly the three bars track each other for most classes and where small gaps appear for Adelfa, Katakataka, and celosia — exactly the classes that would benefit most from additional training data.<br><br>

## C. Confusion Matrix Analysis

7. Which classes were frequently misclassified?<br>

- Looking across all our confusion matrices, a few classes consistently gave the models trouble.<br><br>
- <img width="1965" height="890" alt="image" src="https://github.com/user-attachments/assets/8a8d07cb-25ff-4b53-8a8e-b04a77fcb114" /><br><br>

- In the Good Model Confusion Matrix (right panel), Adelfa had a diagonal value of 57 out of 66 — decent but with some spread into neighboring classes, reflecting its visual similarity to other pink-flowering plants. Katakataka showed 57 correct but with notable misclassifications into nearby leaf-dominant species. Celosia had only 62 correct out of 75, consistent with its 83% recall in the classification report.<br><br>

<img width="1228" height="1089" alt="image" src="https://github.com/user-attachments/assets/68a6a345-8810-4967-97a8-b2bdbb13aef4" /><br><br>

- In the ResNet50 Confusion Matrix, the situation was far worse. Blue Pea Vine had a diagonal value of effectively zero — its 74 validation images were scattered across Katakataka (16), Bluebell (14), celosia (18), and others. Singapore Daisy similarly failed with its images split mainly between Katakataka (12) and Garden Croton (24). Aster had only 7 correct out of 68, with 16 misclassified as Polka Dot.<br><br>

<img width="1228" height="1089" alt="image" src="https://github.com/user-attachments/assets/e28c9aee-5a89-4a0a-ad81-9b158fc69d5c" /><br><br>

- In the EfficientNetB0 Confusion Matrix, every single class was misclassified — the diagonal is completely dark with the entire dataset deposited in the celosia column.<br><br>

8. What patterns did you observe in the confusion matrix?<br>
- Several clear patterns emerged when studying all four confusion matrices side by side.<br>
- Diagonal brightness reflects learning quality directly. The Good Model and MobileNetV2 confusion matrices show a bright, uniform diagonal — you can immediately see the model works. ResNet50's diagonal is patchy and dim, and EfficientNetB0's is completely absent. Just glancing at any confusion matrix tells you the model's quality before reading a single number. Visually similar species cluster their errors. In the ResNet50 Confusion Matrix, misclassifications were not random. The middle rows — Katakataka, Laceleaf, Garden Croton, Polka Dot — show a concentrated block of off-diagonal errors among themselves. These are all leaf-dominant plants, and ResNet50 found some broad textural similarity between them but could not differentiate further. The Traveler's Palm / Variegated Pandan pair also showed 12 mutual misclassifications, suggesting structural similarity in their long-leaf appearance.<br>
- Improvement is visible between baseline and Good Model. The side-by-side Confusion Matrix comparison is particularly convincing — the baseline model's diagonal has several dim or missing entries (Laceleaf at 50, Katakataka at 48, Cupid Peperomia at 56), while the Good Model's equivalent entries are noticeably brighter (Laceleaf at 73, Katakataka at 57, Cupid Peperomia at 69). The improvement is not just in the numbers — you can literally see the diagonal get brighter from left panel to right panel.<br><br>

## D. ROC and AUC
<img width="208" height="144" alt="image" src="https://github.com/user-attachments/assets/584cccf0-54ab-4bf9-9c8d-cb581aaefd10" /><br>
<img width="1189" height="889" alt="image" src="https://github.com/user-attachments/assets/59fb2525-16a9-4607-97c3-bf3d4e3a6966" /><br>
<img width="1790" height="790" alt="image" src="https://github.com/user-attachments/assets/b6eaed7c-ccb4-4345-8740-ed979036d876" /><br>


9. Which model had the highest AUC score?<br>
- From the summary table, the Model from TM had the highest AUC at 0.9998. Among student-trained models, MobileNetV2 from LW5 had the highest at 0.9888, just slightly above the Good Model's 0.9863. This is visible in the MobileNetV2 ROC Curve from LW5 — the curves are tightly packed near the top-left corner with almost no separation between them, meaning every class achieved near-perfect discrimination. Eight classes reached AUC=1.00. Compare that to the EfficientNetB0 ROC Curve where the lines drift diagonally across the plot, many of them tangled near or below the random baseline — a fundamentally different visual story.<br><br>

10. What does AUC tell us about model performance?<br><br>

<img width="1189" height="889" alt="image" src="https://github.com/user-attachments/assets/a51a85ef-9dde-4ae8-b857-d650e66b6b75" /><br>
<img width="1189" height="889" alt="image" src="https://github.com/user-attachments/assets/f09cba42-4f6d-4f66-b244-eb8b09d287ad" /><br>
<img width="1790" height="790" alt="image" src="https://github.com/user-attachments/assets/0ad87483-2042-4a65-b43b-a02f5118e97b" /><br><br>
- An AUC of 1.0 means always. An AUC of 0.5 means never — just a coin flip.<br>
- Looking at the MobileNetV2 ROC Curve, the curves all shoot steeply upward toward the top-left and flatten along the top edge — this shape means the model achieves very high true positive rates while keeping false positive rates near zero. The EfficientNetB0 ROC Curve shows the opposite: lines that slope diagonally from bottom-left to top-right, closely hugging the random baseline, meaning the model's probability scores carry almost no useful information about which class an image belongs to. The Good Model ROC Curve sits closer to the MobileNetV2 result — tight curves in the top-left corner, with the weakest class Adelfa still achieving AUC=0.96, a meaningful improvement from the baseline model's Adelfa AUC of 0.87.<Br><br>

## E. Explainability (Grad-CAM)

11. What did Grad-CAM reveal about model decision-making?<br>

<img width="1313" height="1377" alt="image" src="https://github.com/user-attachments/assets/00ab44bc-b663-4a11-8d91-c3301d46b59c" /><br><br>

- The Grad-CAM Heatmap Comparison across all three LW5 models on the same Adelfa flower image is honestly one of the most revealing outputs of the entire project because it lets us see inside each model's thinking.
MobileNetV2's heatmap shows a well-defined warm zone — deep red and orange — concentrated on the flower's center, petals, and stamen structure. The model is looking at the plant itself to make its decision. EfficientNetB0's heatmap is almost completely cold blue across the entire image, with a single small red hotspot in the bottom-right corner — a patch of background leaves that has nothing to do with identifying the species. The model was essentially guessing based on irrelevant background texture. ResNet50's heatmap shows a broad circular warm zone covering the general center of the image — it knows the flower is somewhere there, but its activation is diffuse and non-specific compared to MobileNetV2's focused attention.<br><br>

12. Did the model focus on relevant image regions?<br>
- Looking at the Grad-CAM overlays in the right column of the comparison figure, the answer is clearly yes for MobileNetV2 and no for EfficientNetB0.<br>
- MobileNetV2's overlay shows warm coloring concentrated precisely on the flower petals and center, with cool tones correctly suppressing the green background leaves. The model is paying attention to exactly the right part of the image. EfficientNetB0's overlay shows the original image tinted almost entirely cool purple-blue, with only a tiny warm spot in a background corner — the model's attention had nothing to do with the plant it was supposedly classifying. ResNet50's overlay is somewhere in between — the flower region gets some warm coloring, but so does a lot of surrounding background, showing broad awareness without precision. The correct prediction of Adelfa from ResNet50 appears to have been partially coincidental rather than based on the right features.

13. Which model produced the most meaningful heatmaps?<br>
- MobileNetV2 produced the most meaningful and interpretable Grad-CAM heatmaps without question. In the Grad-CAM Heatmap Comparison figure, the top row tells a clean story — the heatmap clearly shows a warm blob centered on the flower, and the overlay highlights precisely the petals and stamen that any botanist would use to identify Adelfa. The red zone is focused and purposeful rather than scattered. This kind of interpretable output is not just academically satisfying it is also what builds trust when deploying the system in a real-world application where users need to understand why the model made the prediction it did.<br><br>

## F. Model Comparison & Improvement
14. Which model would you recommend for deployment? Why?<br><br>
<img width="1136" height="218" alt="image" src="https://github.com/user-attachments/assets/41dddcc9-927e-4144-987c-a6bf191d68e4" /><br><br>

- Looking at the full summary table, the Good Model is the clear recommendation for deployment. It achieves test accuracy of 92.75%, train loss of 0.2619, test loss of 0.3003, F1-score of 0.9272, and AUC of 0.9863 — every single metric within or exceeding the recommended targets. But the most important number for real-world reliability is the generalization gap of just 1.26%, well within the ≤5% standard.<br>
- The Good Model Training Curves make this confidence concrete. Looking at the accuracy plot, the validation curve actually runs above the training curve throughout Phase 1 — meaning the model generalized better on unseen data than it did on its own training data. In Phase 2, both curves converge and climb together to around 93% by the final epoch, with the training and validation lines tracking closely. The loss plot mirrors this story — both train and val loss drop together, converge, and flatten at a low value. There is no sign of the val loss spiking away from train loss, which is what overfitting looks like. Compare this to the LW4 2nd Model where train loss was 0.2087 but test loss was 0.5878 — a clear overfitting signal that the Good Model successfully avoided through transfer learning and proper regularization.<br><br>

15. How can you further improve your best-performing model?<br><br>
<img width="335" height="311" alt="image" src="https://github.com/user-attachments/assets/382b56d1-9210-455f-9286-907451bfe6ec" /><br><br>

- Several targeted improvements could push the Good Model beyond 92.75%. Unfreezing more of the MobileNetV2 backbone beyond the current 30 layers — perhaps 50 or more — with a very small learning rate of around 0.000005 would allow deeper adaptation of plant-specific features. Looking at the Good Model Classification Report, the three classes with the most room for improvement are Adelfa (F1: 0.87), Katakataka (0.89), and celosia (0.89) — adding 100 to 200 carefully selected images for just these three species would likely close the gap without a full retraining. Stronger augmentation techniques like random hue shifts and Gaussian noise could help the model handle the lighting variations real users will encounter when photographing plants outdoors. Test-Time Augmentation is a free accuracy boost that requires no additional training at all — just run each image through the model multiple times with slight variations and average the results, which typically adds 0.5% to 1.5% accuracy.<br><br>

## G. Real-World Application

16. How can your model be applied in real-world scenarios?<br>
- There are genuinely exciting ways this model could be put to practical use. The most straightforward application is a mobile plant identification app where anyone — students, gardeners, tourists, or plant enthusiasts — can point their camera at any of the 20 ornamental plant species and get an instant identification with care tips. The Good Model's 92.75% test accuracy and AUC of 0.9863, visible in our summary table, means users can trust the predictions in 9 out of 10 cases.<br>
- Nurseries and botanical gardens could use it to automate inventory management and verify species labeling. Environmental researchers could deploy it in field surveys to speed up biodiversity cataloging. On a more personal level, it could integrate into smart garden systems that recognize which plants you own and automatically customize care schedules per species. The per-class classification report showing strong performance on visually distinct species like Yellow Bell (F1: 0.99), Red Powder Puff (0.98), and Traveler's Palm (0.97) means these high-confidence classes would be especially reliable in a production setting.<br><br>

17. What are the risks of deploying an inaccurate model?<br>
- The risks are real and in some cases serious. The most concerning scenario involves toxic plant misidentification — Adelfa, for example, is actually oleander, which is highly toxic if ingested. Our classification report shows Adelfa has the lowest recall at 86%, meaning about 14% of Adelfa images are being classified as something else. In a deployed app, a user who receives a wrong identification for a toxic plant and acts on it could face genuine health consequences.
Beyond safety, consistently wrong predictions damage user trust quickly — once someone catches a confident wrong prediction, they are unlikely to trust the system again even when it is correct. This is why the Good Model's confusion matrix is so important as a deployment tool — the relatively clean diagonal and minimal off-diagonal scatter give us confidence that errors, when they do happen, are mostly at the edges of visually similar classes rather than wild misidentifications across completely different species. The contrast with EfficientNetB0's confusion matrix — where everything collapses into one class — shows exactly what an untrustworthy model looks like and why thorough evaluation before deployment is non-negotiable.<br><br>

18. How can this system be integrated into a mobile/web app?<br>

Convert model to TensorFlow Lite (mobile) or ONNX (cross-platform)<br>
Backend API using Flask, FastAPI, or Laravel to handle prediction requests<br>
Frontend uploads image → API returns predicted class + confidence score<br>
Display results with confidence percentage and Grad-CAM visualization<br>
Host on cloud platform (Google Cloud, AWS) for scalability<br>
Collect user feedback to continuously improve model accuracy over time<br>
