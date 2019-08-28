---
title: "TensorFlow.js: Speech Command Recognizer (번역)"
date: 2019-08-14 21:45:28 -0600
categories: ML&DL
tags: [딥러닝, 음성인식, TensorFlow, TensorFlow.js] 
comments: true
---

> TensorFlow.js의 공식 모델 중 하나인 Speech command recognition에 대해 조사해 보았습니다.
>
> 원문 링크: [tfjs-models/speech-commands at master · tensorflow/tfjs-models · GitHub](https://github.com/tensorflow/tfjs-models/tree/master/speech-commands)

* 작은 어휘(small vocabulary)에서 분리된 간단한 영단어로 구성된 음성 명령을 인식할 수 있는 자바스크립트 모듈이다.

* Default vocabulary는 
    * “zero”에서 “nine”까지의 10개 숫자, 
    * “up”, “down”, “left”, “right”, “go”, “stop”, “yes”, “no”,
    * 그리고 “unknown word”와 “background noise” 분류를 포함한다.

* 이 Speech command recognizer는 웹 브라우저의 [Web Audio API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Audio_API)를 이용한다. [TensorFlow.js](https://www.tensorflow.org/js/)의 상위에 빌드되어 있으며 추론(inference) 및 transfer learning을 온전히 브라우저 내에서 수행할 수 있는데, WebGL GPU acceleration을 사용한다.

* 기저에 깔려 있는 deep neural network는 [TensorFlow Speech Commands Dataset](https://www.tensorflow.org/tutorials/sequences/audio_recognition)을 이용해 훈련되었다.
	* Core words (20개, 대부분의 speaker가 다음의 단어를 5번씩 녹음하였음)

		* “Yes”, “No”, “Up”, “Down”, “Left”, “Right”, “On”, “Off”, “Stop”, “Go”, “Zero”, “One”, “Two”, “Three”, “Four”, “Five”, “Six”, “Seven”, “Eight”, and “Nine”

	* Auxiliary words (10개, 대부분의 speaker가 다음의 단어를 1번씩 녹음하였음)
		* “Bed”, “Bird”, “Cat”, “Dog”, “Happy”, “House”, “Marvin”, “Sheila”, “Tree”, and “Wow”

	* More details on the dataset: 
		* Warden, P. (2018) “Speech commands: A dataset for limited-vocabulary speech recognition”  [https://arxiv.org/pdf/1804.03209.pdf](https://arxiv.org/pdf/1804.03209.pdf) 

* 이 API를 이용해 `1. 실시간 스트리밍(오디오 input) 인식`과 `2. 오프라인 인식`이 가능하다.
    * 자세한 API 사용법은 [여기](https://github.com/tensorflow/tfjs-models/tree/master/speech-commands#api-usage)를 참고