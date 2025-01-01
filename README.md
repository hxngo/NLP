# NLP Architecture Deep Dive
A technical deep dive into modern NLP architectures, focusing on the structural analysis and operational principles of BERT and GPT models.

## Overview
이 문서는 현대 자연어 처리의 핵심 아키텍처인 BERT와 GPT의 기술적 작동 원리와 구조를 상세히 다룹니다. LSTM과 GRU부터 Transformer 기반 모델까지, 주요 NLP 모델의 발전 과정과 구조적 특징을 설명합니다.

## Contents

### 1. 기초 모델 구조
- LSTM과 GRU의 특징
- 순환 신경망(RNN)의 기본 개념
- Sequence to Sequence 학습

### 2. Transformer 아키텍처
- 인코더-디코더 구조
- 어텐션 메커니즘
- 교사 강요(Teacher Forcing) 학습 방식
- 워드 임베딩 과정

### 3. BERT 아키텍처
- 인코더 전용 구조의 특징
- Masked Language Model (MLM)
- Next Sentence Prediction (NSP)
- 특수 토큰([CLS], [SEP]) 활용
- BERT Pooler 구조
- Downstream Task 적용 방식

### 4. GPT 아키텍처
- 디코더 전용 구조의 특징
- Autoregressive 언어 모델링
- GPT 시리즈의 발전 과정
- Raw Data 학습 프로세스
- Downstream Task 구현 방법

### 5. 핵심 혁신 요소
- Self-Supervised Learning
- Pretraining & Fine-tuning 패턴
- Task별 적용 전략

## Detailed Content
[여기에 전체 기술 문서 내용이 들어갑니다]

## References
- Downstream Task 개념: https://chan-lab.tistory.com/31
- BERT 상세 설명: https://hwiyong.tistory.com/392
- Sequence to Sequence, Attention 개념: https://acdongpgm.tistory.com/216

## Contributing
문서의 오류를 발견하시거나 추가하고 싶은 내용이 있으시다면 Issues나 Pull Requests를 통해 기여해주세요.

## License
This project is licensed under the MIT License - see the LICENSE file for details.
