---
title: "삽질일기: TypeError: unhashable type: 'numpy.ndarray'"
date: 2019-08-05 21:30:28 -0600
categories: ML&DL
tags: [딥러닝, 머신러닝, TensorFlow] 
comments: true
---

> 오늘 scikit-learn으로 작성했던 울음소리 감지 linear regression 모델을 TensorFlow로 다시 구현하는 과정에서 저지른 엄청난 삽질들에 대해 이야기해보려고 한다. ㅎㅎㅎㅎㅎ 신나!!^^

## 1. AttributeError: module 'tensorflow._api.v1.train' has no attribute 'GradientDescentOptmizer'
---

눈썰미가 빠른 분이라면 눈치챘을 것이다. `GradientDescentOptmizer`가 아니라 `GradientDescentOptimizer`니까..!^^ **i** 하나를 빼먹은 줄도 모르고 구글링을 오지게 했다...

* 텐서플로우 버전이 1.14.0인데 (노트북 교환받기 전에 설치했던) 2.0 버전인 줄 착각한 채로 구글링을 했더니 `tf.train.GradientDescentOptimizer` 대신 `tf.keras.optimizers.SGD`를 사용해야 한다는 스택오버플로우 답변을 발견했다. 

* 질문을 올린 사람처럼 API 버전이 `tensorflow._api.v2.train`이라면 아래의 링크를 참고하여 해결하면 된다. 
    > [module 'tensorflow._api.v2.train' has no attribute 'GradientDescentOptimizer'](https://stackoverflow.com/questions/55682718/module-tensorflow-api-v2-train-has-no-attribute-gradientdescentoptimizer)

* 내 텐서플로우 API 버전은 `tensorflow._api.v1.train`인데 멍청하게 저 답변을 따라갔다. 왜냐하면 api 버전 빼고는 에러메세지가 너무나도 똑같았기 때문ㅋㅋ

* `tf.keras.optimizers.SGD`의 `minimize` 메소드에는 인자로 loss function의 variables를 함께 전달해줘야 한다고 한다. 예제 코드 찾느라 이것저것 뒤지다 보니 1시간 넘게 삽질을 하고 있었다. ㅎㅎ

* 차라리 상위레벨의 Keras를 공부해서 쓰는 게 장기적으로 편하려나 별의 별 생각을 하다가 팀장오빠에게 하소연했더니 단번에 나의 오타를 발견해 주었다...

* 마음 급히 먹지 말고 **KEEP CALM AND FIND YOUR TYPO...**


## 2. TypeError: unhashable type: 'numpy.ndarray'
--- 

```
# Fixed Code
sess = tf.Session()
sess.run(tf.global_variables_initializer())
feed={X_holder: X_test, Y_holder: Y_test}

for step in range(50001):
    sess.run(train, feed_dict=feed)
    if step % 2000 == 0:
        print(step, sess.run(cost, feed_dict=feed))
```

> 다시 한 번 되새긴다. **KEEP CALM AND FIND YOUR TYPO...**

* 기존 파일에서 이미 변수명으로 `X`, `Y`(`numpy.ndarray`)를 사용했었기 때문에 새로운 placeholder 변수로 `X_holder`, `Y_holder`를 선언했다.

* 하지만 내가 복붙했던 코드에서는 `X`와 `Y`를 placeholder로 사용하고 있었는데, 이게 너무나도 익숙했던 나머지 변수명이 잘못된 줄도 몰랐다. 다시 삽질을 시작했다..^^

* 구글링해보니 정말 다양한 가능성들이 존재했다. 텐서들의 shape을 다시 확인하고 datatype도 통일시켰다. 하지만 에러는 사라지지 않았다...

* 그렇게 또 1시간을 날렸던 것 같다. 수다 좀 떨면서 머리 식히고 코드를 위에서부터 다시 읽었다... 그렇게 원인을 발견했다... 변수명...

    ```
    # Error
    feed={X: X_test, Y: Y_test}
    ```

* **KEEP CALM AND FIND YOUR TYPO... READ THE WHOLE LINES...**

* 이제 텐서플로우 모델을 `model.json`으로 저장하고 `ml5.js`와 연결해야지!!