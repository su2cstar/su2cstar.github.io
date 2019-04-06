---
layout: post
title: "PRML : Chapter 1 - Introduction"
date:   2019-04-05T00:00:00
author: Juyoung Ahn
categories: PRML
tags: PRML
use_math: true
---

## 주어진 데이터에서 특정 패턴을 찾아내는문제
mnist데이터의 예시는 28*28 픽셀의 손글씨로 쓰인 숫자데이터이다. 즉 784개의 Gray Scale 수치정보가 담긴 데이터
필체를 인식하기 위해서는 필체의 모양을 바탕으로 직접 작성한 규칙 혹은 휴리스틱 알고리즘을 통해서 문제 해결을 시도해 볼 수 있다. 그러나 규칙기반으로 분류를 하기 위해서는 수많은 규칙이 필요하고 각각의 규칙에 대해서 예외사항을 추가하는 등 모델이 복잡해지고 이에 따라 좋지 못한 성능이 도출된다.

머신러닝 알고리즘을 사용하여서 더 나은 결과를 얻을 수 있다.

* N개의 숫자들 $$ \left\{ X_{1}, \cdots, X_{n} \right\} $$ 을 **훈련집합(Training set)** 활용하여