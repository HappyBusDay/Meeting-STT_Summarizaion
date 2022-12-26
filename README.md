# 회의 내용 음성인식 & 문서요약


# 0. 목차
<table>
    <thead>
        <tr align=center>
            <th>번호</th>
            <th>목차</th>   
        </tr>
    </thead>
    <tbody>
        <tr align=center>
            <td>1</td>
            <td><a href="#1">프로젝트 개요</a></td>
        </tr>
        <tr align=center>
            <td>2</td>
            <td><a href="#2">데이터 셋</a></td>
        </tr>
        <tr align=center>
            <td>3</td>
            <td><a href="#3">데이터 전처리</a></td>
        </tr>
        <tr align=center>
            <td>4</td>
            <td><a href="#4">모델 선정</a></td>
        </tr>
        <tr align=center>
            <td>5</td>
            <td><a href="#5">개발 환경</a></td>
        </tr>
        <tr align=center>
            <td>6</td>
            <td><a href="#6">결론</a></td>
        </tr>
        <tr align=center>
            <td>7</td>
            <td><a href="#7">보완점</a></td>
        </tr>
        <tr align=center>
            <td>8</td>
            <td><a href="#8">멤버 & 역할</a></td>
        </tr>
        <tr align=center>
            <td>9</td>
            <td><a href="#9">References</a></td>
        </tr>  
     </tbody>
</table>
<br>

---
# 1. <a name="1">프로젝트 개요</a>

### 1-1. 프로젝트 기간
> 2022.11. 14 ~ 2022.12.23



### 1-2. 프로젝트 배경 및 기대효과
<br>

**1-2-1. 프로젝트 배경**
> - 음성 인식과 자동 문서화 서비스로 회의록 작성 부담을 덜어주자 <br>

<div><img width="600px" height="300px" src="https://user-images.githubusercontent.com/79880476/209476495-b3841af9-9c8d-48d2-8948-6b42e463a725.jpg"</div>
<br>
<br>
  
**1-2-2. 프로젝트 기대효과**  
> - 회의록 정리를 위한 시간과 노동력 최소화 및 신속한 회의록 공유 <br>
> - 회의록 작성자의 작성 부담 경감 및 참여도 증가로 회의 완성도 제고<br>
> - 회의록 관리 및 열람 편의성 제공<br>
<div><img width="500px" height="200px" src="https://user-images.githubusercontent.com/79880476/209476519-858daf61-6a1b-48c4-9b33-718789a91f1d.png"</div>  


  
### 1-3. 프로젝트 내용

> 회의 음성데이터를 STT하여, Text를 추출하고, Text를 문서요약 해주는 서비스 구현

<div><img width="950px" height="350px" src="https://user-images.githubusercontent.com/79880476/209460515-924665d1-3291-49eb-9287-671ffd6b6873.png"</div>

---
  
# 2.<a name="2">데이터 셋</a>

  
### 2-1. 음성 데이터
  
  - <a href="https://github.com/goodatlas/zeroth">Zeroth 데이터 셋</a>
    - Kaldi open source tool-kit을 사용해서 한국어 음성인식기를 구현하는 프로젝트에서 사용된 데이터
    - 발화시간 : 51.6시간 (Train 22,263발화 & Test 457발화)
  - <a href="https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=464">AI Hub - 주요 영역별 회의 음성인식 데이터 셋</a>
    - 전체 카테고리 中 경제, 생활, 교육, 의료, 문화, 스포츠, 연예, 세계 이용
    - 발화시간 : 34.8시간 (Train 18,000 발화 & Test 1,626 발화)

    <br>

    > **총 발화시간 : 86.4시간**
<br>

### 2-2. 문서요약 데이터
  - <a href="https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=582">AI Hub - 요약문 및 레포트 생성 데이터 셋</a>
    - “회의록” 카테고리 12,240개
  - <a href="https://www.aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=591">AI Hub - 방송 콘텐츠 대본 요약 데이터 셋</a>
    - “시사” 카테고리 7,641개
    
    <br>
    
    > **총 Train data 개수 : 19,881개**

<br>

---
  
# 3.<a name="3">데이터 전처리</a>
### 3-1. Audio Fragmentation : 너무나 큰 원본데이터를 학습에 용이하게 분리시키는 과정
  - 용량이 큰 음성 파일을 시작점과 끝점을 기준으로 분리시켜 Input size를 축소
  - 대부분의 음성 파일이 균등한 길이(약 10초)로 나뉘어져 효율적인 학습 가능
  
<div><img width="950px" height="200px" src="https://user-images.githubusercontent.com/79880476/209461052-98266fb9-ba88-4634-b900-f330058996ac.jpg"</div>

<br>
<br>
  
### 3-2. Split audio into voice and background sound : 음성과 배경소리(잡음) 분리 기법
  - Spleeter 모듈 사용 (딥러닝 기술을 이용해 음성에서 MR(배경음악)과 보컬을 분리하는 라이브러리)
  - 데이터 수와 학습 시간이 충분히 확보되지 않을 때에는 잡음 제거 방식이 효과적
  
<div><img width="300px" height="300px" src="https://user-images.githubusercontent.com/79880476/209461566-019c3a60-2430-40c6-9bad-1136f3072cae.jpg"</div>

---
  
# 4.<a name="4">모델 선정</a>

### 4-1. 음성모델

- **facebook/wav2vec2-xls-r-300m**
    - Facebook에서 2020년 발표한 음성 모델로 128개 언어로 공개된 436,000시간 분량의 음성 오디오에 대해 최대 20억 개의 매개변수로 사전 학습된 모델
        - pre-training 과정에서 BERT와 유사하게 마스킹 기법 사용
        - self-supervised learning, 그중에서도 contrastive learning을 이용해서 학습

### 4-2. 문서요약모델

- **psyche/KoT5-summarization**
    - 위키 백과 및 신문기사 등 23개 종류의 출처에서 총 939GB의 데이터 수집, 전처리 후 약 42GB의 한국어 말뭉치로 사전 학습된 모델
        - 모든 언어 문제를 text-to-text task로 변환
        - 요약, 분류, 번역, 회귀 등 multi-task learning 수행 가능
        - 연속된 token을 하나의 mask로 치환하는 Modified Masked Language Model
    
---
  
# 5. <a name="5">개발 환경</a> 

- **사용 툴** : Python, AWS, Colab Pro
    
      huggingface-hub==0.11.1
      datasets==2.7.1
      tokenizers==0.13.2
      torch==1.12.1
      transformers==4.25.1
      tqdm==4.64.1
      sentencepiece==0.1.97
      multiprocess==0.70.14
      rouge_score==0.1.2
      urllib3==1.25.11
      torchaudio==0.10.0

---
  
# 6. <a name="6">결과</a> 

### 6-1. 음성파트
  - 음성에선 일반적으로 WER(Word Error Rate)을 이용해 평가 수행 (WER : (잘못 삭제 + 잘못 대체 + 잘못 추가)된 단어 수 / 정답 단어 수)
      - WER은 허깅페이스에 있는 pre-trained model들과 비교하면 더 낮은 수치까지 학습 → 추후 학습 진행시 더 좋은 성능 기대
      - 낮은 WER에 불구하고 부정확한 발음을 명확히 추론하지 못하는 문제, 문장 부호(마침표, 물음표 등) 미출력 <br>
        <br>
  
      **∴ 맞춤법 및 문법 교정을 통해 후처리 수행**
  
      <br> 
  <div><img width="450px" height="200px" src="https://user-images.githubusercontent.com/79880476/209462419-5b5cea0d-f7da-430c-b7df-16b8fa21eeb0.jpg"</div>

### 6-2. 문서요약파트 
  - 문서 요약은 일반적으로 Rouge score를 이용해 정량 평가 수행 (Rouge score : 정답 요약문과 생성 요약문이 얼마나 겹치는지를 나타내는 점수)
      - 동일한 단어와 순서만을 기준으로 판단하기 때문에 새로운 단어 및 문장(추상적 요약)에 대한 제대로 된 평가에 제한
      - Rouge score와 더불어 정성 평가도 함께 수행
      - 예측 값이 1-2문장으로 짧고, 구조화 되지 않음 → 출력 형식 지정해주어 비슷한 형식으로 구현<br>
    <br>
  <div><img width="600px" height="200px" src="https://user-images.githubusercontent.com/79880476/209462422-584ac717-b0b1-47fa-a4e5-7084b88af4de.jpg"</div>
<br>

---
    
# 7. <a name="7">보완점</a>

### 7-1. 음성인식
  - **다화자 회의에서 발생 할 수 있는 중첩된 음성 인식 문제** <br> 
    → Spleeter  모듈 추가 학습하여 사람의 목소리를 분류해 중첩된 음성을 인식하는 방법 <br>
    → Dacon의 ‘음성 중첩 데이터 분류 AI 경진대회’ 및 레퍼런스 참조해서 해결방안 모색 <br>

  - **명확히 인식하지 못하는 발음**<br>
    → 다양한 데이터 추가 및 vocab을 늘려 충분히 학습하는 방안 <br>
    → 한글의 특성에 맞는 target 데이터 수정 <br>
    
    
### 7-2. 문서요약
  - **개조식 형식의 회의록 아웃풋 미 구현** <br> 
    → 구조화 된 데이터셋을 직접 구축하고 학습시키는 방안 <br>
    → 주 문장과 보조 문장을 구분하여 모델 구축 <br>

  - **회의 안건 및 키워드 미 추출**<br>
    → TF-IDF 모델 및 통계 모델을 이용하여 토픽 모델링  <br>
    → 키워드 Word Cloud 생성으로 회의의 주요 안건 시각화 <br>

  - **은어 및 사내 용어에 대한 부정확한 맞춤법 표기**<br>
    → 사내 용어(줄임말,은어) 단어사전 구축  <br>
    → 사내 용어 단어사전을 구축하여 맞춤법 단계에서 해당 용어로 바꿔주는 처리방안 모색 <br>
    
---
    
# 8. <a name="8">멤버 & 역할</a>

- 김용재 : 음성모델 학습 & 플라스크 웹서버 구축
- 국승용 : 모델 학습 & 음성 데이터 후처리 & 플라스크 웹페이지 제작 & PPT
- 강효진 : 문서요약 모델 학습 & 음성 데이터 전처리 & 플라스크 웹페이지 제작
- 오혜인 : 음성모델 학습 & 플라스크 웹페이지 제작 및 기획  &웹 DB 구축& 발표
- 이재혁 : 음성데이터 전처리 & 모델 학습 & PPT
- 정효제 : 문서요약 모델 학습 & 플라스크 웹페이지 제작 

---

# 9. <a name="9">References</a>

- <a href="https://www.chosun.com/economy/tech_it/2021/09/03/5O35DRPSZJHVFKT26DIOH7B72A">조선일보 기사</a>
- <a href="https://github.com/deezer/spleeter">spleeter 모듈</a>
- <a href="https://www.dacon.io/competitions/official/235616/overview/description">코드 참고(데이콘 음성 중첩 데이터 분류대회)</a>
- <a>Papers</a>
    - <a href="https://arxiv.org/pdf/2006.11477v3.pdf">Wav2vec2.0</a><a> - Baevski, Alexei, et al. "wav2vec 2.0: A framework for self-supervised learning of speech representations."</a>
    - <a href="https://www.jmlr.org/papers/volume21/20-074/20-074.pdf?ref=https://githubhelp.com">T5</a><a> - Raffel, Colin, et al. "Exploring the limits of transfer learning with a unified text-to-text transformer."</a>

