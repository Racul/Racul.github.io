---
title: '[논문리뷰] Latent Speech-Text Transformer'
date: 2026-02-25
permalink: /posts/2026/02/LST/
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

ST 아키텍처는 전반적으로 BLT의 patch 기반 modeling 흐름을 speech-text modeling에 맞게 가져온 구조로 볼 수 있습니다. 개별 token을 직접 autoregressive하게 처리하기보다, 여러 token을 하나의 latent patch로 묶어 global transformer가 더 짧은 sequence를 다루게 만들고자 하였습니다. 이를 위해 LST도 BLT와 마찬가지로 Patch Encoder - Global Transformer - Patch Decoder의 큰 흐름을 유지합니다. Patch Encoder는 여러 speech token을 하나의 patch embedding으로 압축하고, Global Transformer는 이 patch들과 text token을 함께 모델링하며, Patch Decoder는 다시 patch-level 정보를 참고해 실제 speech token을 복원하는 역할을 맡습니다.

다만 LST는 BLT를 그대로 옮겨온 것은 아닙니다. hash embedding은 실험적으로 도움이 되지 않아 제외하였고, speech token에 대해 BPE를 적용하는 단순 subword화 역시 성능 개선으로 이어지지 않아 제외하였다고 이야기합니다. 즉, LST는 BLT의 전체적인 틀은 유지하되, speech-text modeling에 유효한 부분만 선택적으로 차용하였습니다.

## Patch

BLT에서 Patch 아이디어를 가져왔지만, 그 내용에서 차이가 드러납니다. BLT는 byte sequence를 대상으로 fixed stride, BPE, entropy 기반 patching 등 여러 patching scheme을 폭넓게 비교하며, 어떤 분할 방식이 더 효율적인지 실험적으로 탐색했습니다. 반면 LST는 speech를 다루기 때문에 그만큼 다양한 patching을 실험하지는 않았고, 주로 Static Patching과 Alignment Patching에 집중합니다. 

Static Patching은 일정 길이의 speech span을 고정길이로 묶는 방식이고, Alignment Patching은 forced alignment를 Wav2Vec2+CTC로 구현하여 가변적인 길이의 patch로 묶는 방식입니다. 그리고 학습 초반에는 alignment 정보를 적극 활용하다가, 후반으로 갈수록 static patching으로 옮겨가는 방식을 Curriculum Patching이라 정의합니다. 단순 Satatic Patching과 Alignment Patching 기법에 한계가 있었기 때문에 이를 해결하기 위해 사용했던 시도로 보입니다.

BLT에서 사용한 Entropy Patching과 비교하면 Alignment Patching이나 Curriculum Patching은 의미나 논리가 약한 것 같다고 느꼈습니다. Entropy Patching은 정보량이 높거나 예측이 어려운 위치를 기준으로 patch 경계를 정하는 방식으로, 복잡한 구간은 잘게 나누고, 단순한 구간은 크게 묶어 연산을 효율화하려는 발상이 있습니다. 그러나 Alignment Patching에는 그러한 철학이 부족합니다. 

이 점에서 LST의 patching은 BLT처럼 “어떤 patch 기준이 가장 좋은가”를 폭넓게 탐색하는 방향보다는, speech와 text의 semantic alignment를 어떻게 patch에 반영할 것인가에 더 초점이 맞추어져 있다고 볼 수 있습니다. 다시 말해 BLT가 압축 효율과 patch 경계 자체의 설계에 더 관심이 있었다면, LST는 speech-text 모델이라는 특성상 연산량 절감과 cross-modal correspondence를 함께 고려한 patch 설계를 택했다고 이해할 수 있습니다. 그러한 부분에서 사실, BLT는 아이디어를 제공했되, 정말로 LST가 BLT를 업그레이드하여 Speech에 적용했다고 보기엔 부족함이 있다고 생각하였습니다.


---

# Experiment Design
## Dataset

학습 데이터는 text와 speech를 함께 사용합니다. Text는 Llama 2 pre-training collection의 1.8T tokens를 사용하고, Speech는 LibriLight, People’s Speech, Multilingual LibriSpeech, Spotify와 같은 대규모 corpus를 사용합니다. speech 데이터는 전체적으로 매우 큰 규모이며, 대략 총 200k hours 수준으로 이해할 수 있습니다.

이 점은 논문의 문제의식과도 연결됩니다. speech 데이터 자체가 적은 것은 아닌데도, speech는 text보다 훨씬 더 긴 token sequence를 요구하기 때문에 학습이 불리합니다. 즉, 이 논문은 단순히 데이터 부족을 다루기보다, speech를 표현하는 방식 자체의 비효율성을 더 근본적인 문제로 보고 있다고 이해할 수 있습니다.

## Benchmark

평가는 speech-to-speech와 text-to-text 두 모드를 모두 비교하는 방식으로 이루어집니다. 이를 위해 text 기반 벤치마크를 TTS를 사용해 speech 형태로 변환하여, 동일한 과제를 text와 speech 양쪽에서 수행하도록 구성하였습니다. 이 설계는 모델이 speech와 text를 함께 다루는 만큼, 두 모달리티에서의 성능 차이를 직접 비교하려는 의도로 보입니다.

주요 벤치마크는 다음과 같습니다.

- sHellaSwag (HS) \\
기존 HellaSwag를 speech 버전으로 바꾼 벤치마크입니다. 일상적 commonsense reasoning을 평가하는 문장 완성형 과제로 볼 수 있습니다.

- StoryCloze / Topic Story Cloze (SC / TSC) \\
짧은 이야기와 그 결말 후보를 주고, 더 자연스러운 결말을 고르는 과제입니다. narrative coherence나 semantic consistency를 보는 벤치마크로 이해할 수 있습니다.

전체적으로 보면, 논문은 짧은 발화 수준의 음운·구문 능력보다, 의미론적·서사적 coherence를 더 중요하게 본 것 같습니다. 그래서 sWUGGY나 sBLiMP 같은 짧은 구문 기반 평가는 의도적으로 제외하였습니다. 이 선택은 논문의 관심사가 speech token 자체의 미세한 언어학적 품질보다, speech-text sequence를 길게 모델링할 때의 효율성과 의미 보존에 더 있다는 점을 보여줍니다. 다만 그만큼 평가 범위가 다소 좁아진다는 아쉬움은 있습니다.

## Baseline

비교군은 patching 전략별로 나뉘어 있습니다. 대표적으로는 다음과 같습니다.

- Static Patching only
- Aligned Patching only
- Mixed Patching
- Curriculum Patching

Static Patching은 일정 길이로 speech를 고정적으로 나누는 방식이고, Aligned Patching은 forced alignment를 이용해 text 단위와 더 잘 맞는 speech span을 묶는 방식입니다. Mixed는 이 둘을 섞은 형태이고, Curriculum은 학습 초반에는 alignment 정보를 더 활용하다가 후반에는 static patching으로 옮겨가는 전략입니다.

이 비교군 구성은, 단순히 “patch를 쓰느냐 안 쓰느냐”보다는 어떤 방식의 patching이 speech-text modeling에 더 적합한가를 보려는 설계라고 할 수 있습니다. 다만 BLT처럼 patch 경계 설계 자체를 폭넓게 비교한 것은 아니기 때문에, Related Work 차별성이 아주 강하게 드러난다고 보기는 어렵습니다. 오히려 이 논문의 실험은 새로운 patching 방식을 다양하게 제안한다기보다, speech-text 정렬을 고려한 patching이 실제로 baseline보다 유효한가를 검증하는 쪽에 더 가깝습니다.

## 정리

정리하면, LST의 실험 설계는 크게 두 가지를 보려는 것으로 이해할 수 있습니다. 하나는 speech와 text를 함께 다룰 때 speech 쪽의 비효율을 patching이 완화할 수 있는가이고, 다른 하나는 그 과정에서 어떤 patching 방식이 더 적절한가입니다.

---

# Result

LST의 결과는 전반적으로, speech를 patch 단위로 다루는 방식이 단순한 연산 절감에 그치지 않고 실제 성능 향상으로도 이어졌는가를 확인하는 방향으로 읽을 수 있습니다. 특히 이 논문은 speech 성능만이 아니라 text 성능도 함께 보는 구조를 취하고 있기 때문에, patching이 단순한 compression이 아니라 speech-text alignment에도 영향을 주는지를 함께 보여주려는 것으로 보입니다.

## Controlled Budget 결과

먼저 compute-controlled setting에서는, 같은 계산 예산 안에서 LST가 baseline보다 일관되게 더 높은 성능을 보입니다. 특히 HellaSwag에서 Base SpeechLLM이 S→S 39.0, T→T 47.0인 반면, LST(Curriculum)는 S→S 45.5, T→T 52.2를 기록합니다. StoryCloze와 TopicStoryCloze에서도 비슷한 경향이 이어집니다. 이는 patching을 통해 같은 연산량으로 더 많은 유효 정보를 처리할 수 있게 되었고, 그 효과가 speech와 text 양쪽에 모두 반영된 것으로 이해할 수 있습니다. 반대로, 본래 취지대로 speech와 text간의 성능 간극이 크게 줄었는가를 생각해보면 크게 그런 성향이 보이지는 않는 것 같습니다.

data-controlled setting에서도 결과는 비슷합니다. 같은 양의 speech/text token을 보게 했을 때도, LST는 sequence를 patch로 압축하므로 더 적은 계산으로 학습할 수 있는데, 그 상황에서도 baseline보다 높은 성능을 유지합니다. 예를 들어 HellaSwag에서 Base SpeechLLM은 S→S 40.2, T→T 49.6, LST(Curriculum)는 S→S 45.5, T→T 52.2를 기록합니다. 논문은 이 과정에서 compute saving이 약 20% 수준이라고 설명하는데, 이는 LST가 단지 성능만 좋은 것이 아니라 효율까지 함께 개선한 구조라는 점을 강조하려는 것으로 보입니다.

## Scaling 결과

scaling 결과에서도 LST의 경향은 유지됩니다. 420M에서 1.8B까지 compute-optimal하게 비교했을 때, 작은 모델부터 이미 baseline보다 높고, 모델이 커질수록 그 차이가 더 분명해집니다. 예를 들어 1.8B에서 HellaSwag 기준으로 speech는 35.3 → 39.0* text는 45.7 → 46.3으로 올라갑니다. 즉, LST는 작은 기법적 보정보다는, speech LM의 scaling bottleneck을 완화하는 방향의 구조적 개선에 더 의미가 있다고 보입니다.

7B에서도 fixed-token budget 하에서 LST가 더 빠르게 성능이 올라가고 더 높은 정확도를 유지한다고 설명합니다. 다만 논문 스스로도 이 구간이 scaling-law optimal regime보다는 낮은 데이터-제한 조건이라고 말하고 있으므로, 여기서는 “LST의 장점이 큰 모델에서도 사라지지 않았다” 정도로 이해하는 것이 적절해 보입니다.

## Downstream 결과

downstream 실험에서는 ASR adaptation과 TTS reconstruction을 봅니다. ASR에서는 baseline이 초기 fine-tuning에서 매우 불안정한 모습을 보이는 반면, LST는 훨씬 안정적으로 적응합니다. 예를 들어 1k iteration에서 baseline은 140 / 202 WER 수준으로 사실상 무너지는 반면, LST는 6.8 / 10.4 WER를 기록합니다. 이는 patching이 단순히 pretraining benchmark 점수만 높이는 것이 아니라, speech representation 자체를 더 다루기 쉬운 형태로 바꾼 것일 가능성을 보여줍니다.

TTS에서는 품질이 크게 나빠지지 않으면서 generation length가 줄어든 점이 중요합니다. baseline과 LST의 CER은 거의 비슷하지만, LST는 generation units를 약 1/4 수준으로 줄입니다. 즉, LST의 장점은 학습 효율뿐 아니라 추론 효율에도 연결됩니다. 논문은 이를 통해 더 적은 decoding step으로 비슷한 품질을 유지할 수 있다고 주장하고 있습니다.

## Patching 분석

patching 전략별 ablation에서도 aligned patching이 static보다 대체로 더 낫고, curriculum이 가장 강한 결과를 보입니다. 다만 논문은 이러한 차이가 왜 나타났는지에 대해 충분히 깊은 해석을 제시하지는 않았기에, aligned patching과 static patching의 차이에 대해 더 심도있는 분석을 진행하고, 그것을 통해 왜 aligned patching을 먼저 수행하고 나중에 static patching을 진행했는지, 반대의 순서라던가, aligned-static-aligned 와 같은 다른 구조의 구성이 더 유리하지는 않았을지 고찰을 할 수 있으면 좋겠다고 생각하였습니다.

# 정리

LST 논문은 speech-text 모델링에서 반복적으로 등장하던 문제를, 단순히 데이터 규모나 모델 크기의 문제가 아니라 speech와 text의 토큰 단위 자체가 불균형하다는 문제로 다시 짚었다는 점에서 의미가 있었습니다. 이를 해결하기 위해 BLT의 patch 아이디어를 speech 영역으로 가져와, 긴 speech sequence를 latent patch 단위로 압축해 처리하려 했고, 실제로 controlled budget, scaling, downstream 실험 전반에서 baseline보다 나은 결과를 보였습니다. 특히 성능 향상뿐 아니라 compute 절감과 generation length 감소까지 함께 확인되었다는 점에서, 이 접근은 단순한 구조 변화라기보다 speech LM의 비효율을 완화하려는 실질적인 시도로 볼 수 있었습니다.

다만 이 논문은 BLT의 아이디어를 speech에 맞게 정교하게 확장했다기보다는, 그 큰 틀을 가져와 speech-text 모델에 맞게 제한적으로 적용한 연구에 더 가까워 보였습니다. patch 설계의 다양성은 BLT보다 줄어들었고, aligned patching과 curriculum patching이 왜 효과적인지에 대한 분석도 충분하지는 않았습니다. 또한 speech와 text 사이의 성능 간극이 완전히 해소되었다고 보기도 어려웠습니다. 그럼에도 불구하고, speech-text mismatch를 표현 단위와 연산 단위의 문제로 재해석하고, patching을 통해 이를 완화할 수 있음을 보였다는 점은 인상적이었습니다. 개인적으로는 이 논문이 완성된 해답을 제시했다기보다는, 앞으로 speech-text transformer를 설계할 때 어떤 단위로 speech를 모델링해야 하는가라는 질문을 다시 생각하게 만든 논문으로 남았습니다.