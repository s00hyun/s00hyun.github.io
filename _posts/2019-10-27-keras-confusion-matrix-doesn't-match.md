---
title: "Keras predict_generator로 confusion matrix를 그렸을 때 accuracy가 안 맞는 오류 해결방안 정리"
date: 2019-10-27 21:24:28 -0600
categories: ML&DL
tags: [딥러닝, CNN, Keras] 
comments: true
---

## 1. 문제 

`ImageDataGenerator`로 데이터셋을 생성할 경우 `predict_classes` 대신 `predict_generator`를 이용해 테스트 클래스를 예측하게 되는데, 이 때 `evaluate_generator`로 얻은 accuracy와 `sklearn`의 `confusion_matrix`를 통해 계산한 accuracy가 일치하지 않는 문제가 발생했다.


## 2. 원인

`predict_generator`를 이용해 다음과 같이 테스트 클래스를 예측하면 인덱스가 맞지 않게 된다. 
```
confusion_matrix(validation_generator.classes, y_pred)
```

## 3. 해결방안 (Solution) 

>  [https://groups.google.com/forum/#!topic/keras-users/bqWwFox_zZs](https://groups.google.com/forum/#!topic/keras-users/bqWwFox_zZs) 

```
confusion_matrix(validation_generator.classes[validation_generator.index_array], y_pred)
``` 
를 사용한다.


## 4. 참고

### Keras ImageDataGenerator
> [Image Preprocessing - Keras Documentation](https://keras.io/preprocessing/image/#flow_from_directory)

파일 구조를 이용해 자동으로 이미지 데이터셋을 읽고 라벨링할 수 있다.

### Keras Model 저장하기 & 읽어서 evaluate하기
> [How to Save and Load Your Keras Deep Learning Model](https://machinelearningmastery.com/save-load-keras-deep-learning-models/)

### Confusion Matrix란?
> [Confusion Matrix in Machine Learning - GeeksforGeeks](https://www.geeksforgeeks.org/confusion-matrix-machine-learning/)

### Scikit-learn으로 Confusion Matrix 그리기
> [sklearn.metrics.confusion_matrix — scikit-learn 0.21.3 documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.confusion_matrix.html)

> [Confusion matrix — scikit-learn 0.21.3 documentation](https://scikit-learn.org/stable/auto_examples/model_selection/plot_confusion_matrix.html#sphx-glr-auto-examples-model-selection-plot-confusion-matrix-py)

### Scikit-learn으로 Classification Report 뽑기
> [sklearn.metrics.classification_report — scikit-learn 0.21.3 documentation](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.classification_report.html)


## 주요 코드
```
print("-- Evaluate --")
STEP_SIZE_VALID = validation_generator.n // validation_generator.batch_size
scores = model.evaluate_generator(generator=validation_generator, steps=STEP_SIZE_VALID)
print("%s: %.2f%%" %(model.metrics_names[1], scores[1]*100))


#Confusion Matrix and Classification Report

np.set_printoptions(precision=2)

validation_generator.reset()
Y_pred = model.predict_generator(validation_generator, STEP_SIZE_VALID+1)#validation_generator.n // validation_generator.batch_size+1)
classes = validation_generator.classes[validation_generator.index_array]
y_pred = np.argmax(Y_pred, axis=-1)  # Returns maximum indices in each row
print(sum(y_pred==classes)/10000)

class_names = ['cry', 'laugh', 'silence', 'speech(babble)']  # Alphanumeric order

print('-- Confusion Matrix --')
print(confusion_matrix(validation_generator.classes[validation_generator.index_array], y_pred))

# Plot non-normalized confusion matrix
plot_confusion_matrix(validation_generator.classes[validation_generator.index_array], y_pred, classes=class_names, title='Confusion matrix, without normalization')
# Plot normalized confusion matrix
plot_confusion_matrix(validation_generator.classes[validation_generator.index_array], y_pred, classes=class_names, normalize=True, title='Normalized confusion matrix')

plt.show()

print('-- Classification Report --')
print(classification_report(validation_generator.classes[validation_generator.index_array], y_pred, target_names=class_names))

```