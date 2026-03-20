---
title: "박정민 | 포트폴리오"
permalink: /resume/
layout: resume
---

# **박정민**   
[mail](mailto:james1990a@snu.ac.kr) [github](http://github.com/racul) [github blog](http://racul.github.io) [linkedin](https://www.linkedin.com/in/jeongmin-park-9a181b3a1/)

- 명확한 목표를 세우고 목표에 알맞은 결과를 내고자 노력합니다.
- 사람과 정보를 중요하게 생각하여, 적극적인 소통을 추구합니다.
- ML/DL을 공부하여 AI 시스템으로 현실의 문제를 해결하고자 합니다.

## 학력

서울대학교 컴퓨터공학부, 2021. 03 - 2027. 02 (예정)
- 4학년 재학
- 전체 평점 **3.84** / 4.3, 전공 평점 **3.92** / 4.3 (수강 학점: 98)


## 프로젝트 경험

### '자연언어처리 개론' 과목 프로젝트 (KorEduBench)

2025.10 - 2025.12

<div class="project-links">
<a href="/posts/2025/12/KorEduBench/">📎 Post</a>
</div>

**[문제 상황]**
- 한국 공교육 도메인 텍스트를 **교육과정 성취기준 코드**로 분류하는 벤치마크 설계
- 성취기준 수가 많아 **LLM 단독 분류의 비용과 정확도 한계**가 존재

**[진행 사항]**
- AI Hub 교과 데이터를 정제하며 **오라벨과 클래스 불균형 문제**를 점검하고 벤치마크 데이터셋 구성
- **RoBERTa-large 기반 top-k classifier**를 구축해 관련 성취기준 후보를 우선 검색하는 2-step 파이프라인 설계
- LLM 입력 구조와 few-shot 설정을 비교하며 **교육 도메인 분류 성능과 효율성**을 분석

### '딥러닝의 기초' 과목 프로젝트 (ARC-AGI Challenge)

2025.05 – 2025.06

<div class="project-links">
<a href="/files/dl_arcagi_project_report.pdf">📎 Report</a>
</div>

**[문제 상황]**
- **ARC-AGI 데이터셋**에 대한 모델 추론 정확도 향상
- 제한된 GPU 및 **추론 시간 제약(100분)** 하에서 효율적 추론 수행

**[진행 사항]**
- **Qwen3-0.6B**를 기반 모델로 선정하고, **full fine-tuning** 중심의 학습 전략 비교·실험 수행
- **2-stage training pipeline** 설계: 원본 데이터 학습 후, **flip/rotation, digit shuffle** 기반 데이터 증강을 추가하여 일반화 성능 개선
- **추론 파이프라인 고도화**: **rank-based task filtering**, **task-specific test-time training (TTT)**, **confidence-based Best-of-N sampling**을 결합하여 정답 선택 성능 향상
- 베이스 모델군, 모델 크기, fine-tuning 방식(Full FT vs QLoRA), 데이터 다양성에 따른 성능을 비교 분석
- **Notion 기반 협업 환경** 구축, 데이터 증강 및 테스트 결과 시각화 코드 구현, 실험 및 결과 분석 수행


### 2024 SNU FastMRI Challenge

2024.06 - 2024.08

<div class="project-links">
<a href="https://github.com/B0neh3ad/FastMRI_2024_shasha">📎 Github</a>
<a href="https://www.youtube.com/watch?v=ho63rjf3XVs">📎 발표 영상</a>
</div>

**[문제 상황]**  
- 가속 촬영된 MRI 뇌 이미지를 고화질로 복원
- GPU 메모리(16GB) 및 추론 시간 제약
- 제한된 데이터 양으로 인한 학습 효율 문제

**[진행 사항]**  
- MRI Reconstruction SOTA 모델인 [E2E VarNet](https://arxiv.org/abs/2004.06688)을 기반으로 실험 설계
- k-space / image 도메인 증강 병행으로 데이터 부족 문제 해결
    - 기존 [k-space 증강 기법](https://github.com/z-fabian/MRAugment)에 밝기, 대비, mask augmentation 추가
    - [CutMixUp](https://arxiv.org/abs/2004.00448) 기법으로 이미지 데이터 다양성 확보
- 모델 규모 확장 대신 dataset 분석 및 효율적인 전/후처리에 집중
- WandB / Notion을 활용한 실험 관리 및 팀 협업 체계 구축

**[결과]**  
- **전체 4위 수상**  
- Public dataset 기준 SSIM 0.9803 달성

---

## 수상내역

- 2024.09, SNU FastMRI Challenge, **4위**, 서울대학교
- 2025.02, STX 장학재단 장학생, **2025년 장학생 선발** (총 11명), STX 장학재단
- 2025.02, 운해재단 장학생, **2025년 12기 운해장학생 선발**, 운해재단

## 기타 활동

### 2025 [SNU FastMRI Challenge](https://fastmri.snu.ac.kr/) 총괄 조교
2025.05 - 2025.09
- 대회 규정 설정 및 280여명의 참가자 관리
- [Vessl AI](https://vessl.ai/ko) 및 공과대학 정보화지원실과 협업하여 k8s 기반 GPU 컨테이너 관리
- 기존 brain image dataset에 knee image를 추가하여 대회 주제의 범용성을 높임
- 새로운 dataset을 위한 데이터 전처리, 학습 baseline 코드 작성
- 참가자들이 대회 주제와 dataset에 빠르게 익숙해지도록 [튜토리얼 notebook(.ipynb) 파일](https://github.com/LISTatSNU/FastMRI_challenge/blob/2025_baby_varnet/tutorial.ipynb) 제작 및 배포
- [Github issues](https://github.com/LISTatSNU/FastMRI_challenge/issues)를 통해 대회 관련 질의응답을 수시로 진행

### 서울대학교 Holi Lab 학부연구인턴
2025.08 - 2026.03
- 서울대학교 조요한 교수 지도하에 학부연구인턴으로 활동
- SLM Agent 연구를 수행하며 AI Agent의 벤치마크와 시스템에 대한 이해를 심화 [논문리뷰](https://racul.github.io/posts/2026/02/LST/)

### '컴퓨팅 기초' 학부생 조교
2024.03 - 2024.06, 2024.09 - 2024.12
- 2024학년도 1학기 및 2학기 ‘컴퓨팅 기초’ 과목의 조교로 활동
- Python, 웹 크롤링, 데이터 분석 관련 실습과 프로젝트를 지원하며 학습 보조 및 질의응답 담당

### 서울대학교 농생대 밴드부 매김소리
2024.09 - 2025.02
- 어쿠스틱 및 일렉기타로 활동
- 연말 공연에서 2개 곡 무대에 참여

### 서울대학교 중앙동아리 러닝크루 달리샤
2024.03 - 2025.02
- 주 1회 교내 운동장 정기 러닝에 참여
- 사이버 영토 수호 마라톤 대회 10km (50:58 완주) 참가

## 기술 스택

<div class="skills-section">
  <div class="skill-category">
    <span class="skill-label">프로그래밍</span>
    <div class="skill-tags">
      <span class="skill-tag">C/C++</span>
      <span class="skill-tag">Python</span>
    </div>
  </div>
  
  <div class="skill-category">
    <span class="skill-label">ML/DL</span>
    <div class="skill-tags">
      <span class="skill-tag">Pytorch</span>
      <span class="skill-tag">Huggingface</span>
    </div>
  </div>
</div>