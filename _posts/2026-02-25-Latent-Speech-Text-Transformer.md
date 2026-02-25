---
title: '[논문리뷰] Latent Speech-Text Transformer'
date: 2026-02-25
permalink: /posts/2021/08/fisrt-post/
tags:
  - Paper Review
---

# Motivation

HOLI Lab에서 음성모델과 관련된 연구에 참여하고 있습니다.
연구에서 만드는 모델은 Text와 Speech를 동시에 생성할 수 있습니다.
저희의 기대로는 해당 모델이 생성하는 Text와 Speech가 정확히 일치해야하지만, 그렇지 않았습니다.

관련하여 다른 Speech-Text Transformer는 어떤 방식으로 학습을 하였는지 살펴보던 중, ICLR 2026 Oral을 받은 이 논문을 확인하여 정리해보게 되었습니다.

---

# Abstract

Latent Speech-Text Transformer(LST) 논문은 Speech와 Text를 다룰때 생기는 불균형에서 모티베이션을 가집니다.

Text token과 비교할때 Speech token은 불균형적으로 더 깁니다. 이에 따라, Speech 모달리티는 Text 모달리티보다 상당히 불안정한 모습을 보입니다. 한 논문에서는 SLM은 LLM보다 1000배 느리게 학습한다고 합니다. 

이러한 문제를 해결하기 위한 방법으로 논문에서는 기존 SLM인 SpiRit-LM에, Byte Latent Transformer의 patch 기법을 엮습니다.
BLT는 LLM에 대한 연구 논문이지만, 이것을 speech token에 적용시켜, LST는 speech token을 동적으로 latent speech patch로 전환합니다.

논문에 따르면, LST는 speech-speech, text-text 처리 모두에서 baseline보다 더 높은 성적을 얻었다고 합니다.

---

# Introduction

## 문제인식

Scaling Properties of Speech Language Models에 의하면, text-based LLM과 비교할 때 SLM의 linguistic 성능은 1000배 느리게 스케일링 된다고 합니다. 원인은 Text보다 Speech가 더 많은 토큰을 요구하기 때문으로 보입니다. 따라서, 같은 정도의 의미를 내포한 데이터도 text가 아니라 speech로 처리할 경우 더 많은 연산량을 사용한다고 생각할 수 있습니다. 

이를 해결하기 위한 최신 연구 방법으로는, warm initalization, pretraining with interleaved speech-text data, modeling S and T in multiple streams 등이 있었습니다. 그러나 위 방법들로도 text 모달리티 성능과 speech 모달리티 성능의 격차는 심한 상태 그대로였습니다. 이는 text에 대한 정보와 speech에 대한 정보가 상당히 불균형(mismatch)하다고 볼 수 있습니다.

## 해결 방법

Byte Latent Transformer는 token들을 latent patch로 묶어 FLOP 효율을 크게 향상시킨 모델입니다. 고정된 토큰이 아닌, 동적으로 결정되는 latent patch를 이용하며, 엔트로피 계산을 통해 데이터 복잡성이 높은 곳에 더 많은 연산을 투자하도록 설계합니다.

해당 patch 기법을 차용하여 SLM에 이용하여 SLM의 scaling 문제를 해결하고, 더 높은 성능을 끌어내고자 하였습니다. 그러나 BLT의 patch 기법은 text-base에 맞추어져 있어, 엔트로피 방법을 그대로 사용하지 못하고 단순화된 방법을 사용한 것으로 보입니다.

baseline은 Spirit LM 논문에서 사용한 모델을 변형하여 만들었습니다.
Spirit LM은 pretraining with interleaved speech-text data 방법을 이용하여 훈련된 text-speech 멀티모달 언어모델입니다. interleaved speech-text data는 하나의 squence에 text token과 speech token을 섞은 data로 이를 pretraining에 이용하여 text-speech alignment를 높이고자 사용한 방법입니다. 본래 Spitit LM은 Speech-to-Text나 Text-to-Speech 도 다루었으나, LST에서는 다루지 않은 것으로 보입니다.

---

# LST

## LST 아키텍쳐

## GSLM

---

# Experiment Design
## Dataset

## Benchmark

## Baseline

---

# Result
