# (Homepage)[]

# Abstract
     골프 스윙은 몸 전체를 능숙하 움직여야 하는 복잡한 움직임이다. 따라서 생물역학적 주제가 되기도 한다. 이 논문에서 우리는 골프 스윙의 주요 이벤트를 감지하고 골프 스윙 분석을 용이하게 하기 위한 골프 스윙 시퀀싱의 개념을 소개한다. 골프 스윙 시퀀스의 알관된 평가를 위해, 우리는 벤치마킹 데이터셋으로 GolfDB도 제안한다. 더욱이 레퍼런스 베이스라인처럼 골프 스윙 시퀀싱 성능을 평가하기 위해, 우리는 경량화된 딥러닝 모델인 SwingNet을 제안한다. 이것은 8개의 골프 스윙 이벤트를 평균 76.1%로 정확하게 감지하고 8개의 이벤트 중 6개를 91.8%로 정확하게 감지한다.

# 1. Introduction
### 1.1. Introduction  
      1. 골프는 여러 나라에서 많이 치고 배우는데 오래 걸리니까 골퍼들의 스윙을 면밀히 조사해야한다.
      2. 여러분야에서 골프를 연구하지만 특수장비가 필요하고 시간이 많이 걸려 일상적인 골퍼에게 실용적이지 않다.
      3. 장비가 발달함에 따라 고화질 촬영 후 슬로우 모션 영상으로 분석할 수 있으나 이는 효율적이지 않다.(시간, 이벤트 수)
      4. 딥러닝 모델들이 영상 관련 분야에 성능을 드러내고 있고, 골프 영상 분석에도 잘 쓰일 것이다.

### 1.2. GolfDB 소개
  
<img src="../../img/golf1.png" width='400'>
  
    1. 1400개의 390K 프레임,HD화질의 프로 골퍼의 스윙 영상 - YouTube 추출
    2. BB와 클럽의 타입(아이언, 드라이버 등)에 대한 라벨, 선수 성별도 존재
    3. 다음 여덟가지 이벤트로 라벨링됌.
   
|1|2|3|4|5|6|7|8|
|-|-|-|-|-|-|-|-|
|Address|Toe-up|Mid-backswing|Top|Mid-downswing|Impact|Mid-follow-thorough|Finish|


# 2. Related Work

### 2.1 Computer Vision in Golf
    1. 골프에서 CV가 가장 잘 쓰이는 곳은 공 추적 기술일 것이다.
    2. CV는 스윙 분석에도 많이 쓰여왔다.(스윙 시 신체 추적 등)

### 2.2 Action Detection
    1. Action recognition은 highest-level task이며 영상에서 단일 동작을 예측해내는 것에 해당한다. Two-stream, 3D CNN, LSTM 등 다양한 메커니즘을 활용해왔다.
    2. Temporal action detection은 mid-level task이며 untrimmed video에서 동작의 시작과 끝 프레임을 예측해낸다.
    3. Spatio-temporal action localization은 lowest-level task이며 가장 복잡한 task이다. 이는 구간분할과 동장에 대한 Localization이 동시에 이뤄지며, 몇몇 연구들은 3D CNN과 object detection 모델을 결합해 만든다.
    4. Event Spotting은 action detection과 달리 특정 시점을 예측한다. 앞으로 이것을 event detection이라 부른다.

# 3 Golf Swing Sequencing
    골프 스윙 시퀀싱 작업을 단일 골프 스윙을 포함하는 트리밍된 영상에서 event detection로 설명한다. 트리밍된 골프 스윙 비디오를 사용하는 이유는 세 가지이다.
    1. 본 데이터셋을 사용하는 가장 큰 목적은 방송 영상이 아닌 즉각적인 영상 분석을 통한 피드백이라고 가정한다.
    2. 현장에서 골프 스윙 시퀀스를 보고자하는 골퍼/강사는 피사체가 중앙에 위치하게 할 수 있다. 이는 시공간적 Localization의 필요성을 줄인다.
    3. Trimmed video 사용 시 성능 향상


     골프 스윙 시퀀스를 구성하는 8개의 연속 이벤트를 다음과 같이 정의한다.
    1. Address -> takeaway 시작 전 순간. backswing 동작 이전
    2. Toe-up -> backswing 시 샤프트가 지면과 평행을 이루는 순간
    3. Mid-backswing -> backswing 시 팔이 지면과 평행을 이루는 순간
    4. Top -> 클럽이 downswing으로 방향을 바꾸는 순간
    5. Mid-downswing -> downswing 시 팔이 지면과 평행을 이루는 순간
    6. Impact -> 클럽이 공에 닿는 순간
    7. Mid-follow-through -> 샤프트가 follow-through 시 지면과 평행을 이루는 순간
    8. Finish -> 골퍼의 마지막 포즈가 풀리기 직전

    위 정의는 항상 다른 프래임에 존재하는게 아니다. 예를 들어 골퍼는 finish 포즈를 취하지 않을 수 있고, 이러한 event frame 선택은 주관적이다.

# 5. SwingNet: A Swing Sequencing Network
### 5.1. Network Architecture
<img src="../../img/golf2.png" width=700>

    1. image classification으로 MobileNetV2를 쓰며, 이 때 event는 위에 정의한 8가지와 non-event로 총 9가지로 구성된다.
    2. temporal information을 얻기 위해 MobileNetV2로 얻은 벡터를 biLSTM에 넣는다.
    3. biLSTM의 output은 FC layer의 input으로 쓰인다.

# 6. Conclusion
    본 논문은 골프 스윙 시퀀싱이라는 trimmed video에서 key event detection task를 소개한다. 골프 스윙 시퀀싱의 목적은 모바일 장비로 key frames의 자동 추출을 통해 현장에서 즉각적인 피드백을 얻을 수 있도록 골프 스윙을 분석하는데 있다. 이를 지원하기 위해 GolfDB가 구축되었고, 모바일 진출을 위해 SwingNet 또한 제안한다. event label 뿐만 아니라 GolfDB는 bb, 클럽 종류, 성별, 각도 등의 라벨을 포함한다.

















































