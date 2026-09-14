---
permalink: /projects/kookmin2026/
title: "국민대학교 자율주행 경진대회 2026"
excerpt: "132개 팀 중 최종 7위를 기록한 ROS 2 기반 카메라·LiDAR 자율주행 시스템 사례 연구."
author_profile: true
---

<p class="case-kicker">자율주행 · Team SVE · 완료</p>

<p class="intro">카메라와 LiDAR 인지, 미션 단위 의사결정, 경로 추종, 액추에이터 안전 제어, 실측 데이터 기반 시뮬레이션을 통합한 1/10 스케일 Xycar 자율주행 시스템이다. 132개 팀이 참가한 예선에서 9위로 22개 팀이 진출하는 본선에 올랐으며, 최종 7위를 기록했다.</p>

<figure class="case-hero">
  <img src="{{ '/images/projects/kookmin2026/vehicle.jpg' | relative_url }}" alt="본선 경기장을 주행하는 Team SVE의 Xycar" />
  <figcaption>본선 경기 중인 Team SVE의 1/10 스케일 Xycar.</figcaption>
</figure>

<nav class="case-nav" aria-label="프로젝트 상세 목차">
  <a href="#overview">개요</a>
  <a href="#competition-result">대회 결과</a>
  <a href="#my-contribution">담당 역할</a>
  <a href="#system-architecture">시스템 구성</a>
  <a href="#qualifying-round">예선</a>
  <a href="#main-round">본선</a>
  <a href="#simulation--sim-to-real">시뮬레이션·Sim-to-Real</a>
  <a href="#validation--experiments">검증·실험</a>
  <a href="#problems--solutions">문제·해결</a>
  <a href="#lessons-learned">배운 점</a>
  <a href="#repository">저장소</a>
</nav>

## 개요 {#overview}

제9회 국민대학교 자율주행 경진대회를 위해 개발한 프로젝트다. 본선 차량은 Ubuntu 22.04와 ROS 2 Humble 환경에서 어안 카메라, 2D LiDAR, VESC 액추에이터 인터페이스를 사용했다. 하나의 알고리즘을 시연하는 데 그치지 않고, 실제 차량의 제약 조건 안에서 차선 주행, 신호등 인식, 동적·정적 장애물 회피, 지름길 좌회전, 라바콘 미션을 조율하는 종단간 시스템을 구축하는 것이 목표였다.

<dl class="fact-list">
  <div><dt>플랫폼</dt><dd>1/10 스케일 Xycar</dd></div>
  <div><dt>실행 환경</dt><dd>Ubuntu 22.04 · ROS 2 Humble · Fast DDS</dd></div>
  <div><dt>센서</dt><dd>어안 카메라 · 2D LiDAR</dd></div>
  <div><dt>제어</dt><dd>Stanley · Pure Pursuit · PID · PI 모터 제어</dd></div>
</dl>

## 대회 결과 {#competition-result}

<div class="result-strip" aria-label="대회 결과">
  <div><span>예선</span><strong>9위 / 132팀</strong><small>3바퀴 · 151.32초</small></div>
  <div><span>본선 진출</span><strong>22개 팀</strong><small>예선 통과</small></div>
  <div><span>최종 결과</span><strong>7위 / 132팀</strong><small>총 기록 149.65초</small></div>
</div>

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/final-ranking.png' | relative_url }}" alt="건국대학교 Team SVE가 149.65초로 7위를 기록한 최종 순위 화면" loading="lazy" />
  <figcaption>최종 순위 화면: Team SVE는 총 기록 149.65초로 7위를 기록했다.</figcaption>
</figure>

## 담당 역할 {#my-contribution}

팀 프로젝트에서 아래 영역의 구현과 통합, 파라미터 튜닝을 담당했다. 아래 내용은 전체 시스템을 단독으로 개발했다는 의미가 아니다.

- **카메라 인지:** 차선용 YOLO와 객체용 YOLO 파이프라인을 분리하고 신뢰도 임계값, 입력 해상도, 실행 주기를 조정했다.
- **차선 기하와 제어:** YOLO와 OpenCV를 결합한 차선 기하 추정과 속도 구간별 Stanley 제어기를 개발했다.
- **미션 통합:** 데이터 최신성 검사와 VESC fail-safe 동작을 포함해 `STOP`, `PAUSED`, `LANE`, `OVERTAKE`, `CONE` 상태를 조율했다.
- **장애물 인지:** 동적·정적 장애물 YOLO 검출 결과와 LiDAR 연관 정보를 미션 파라미터에 연결했다.
- **지름길 좌회전:** 신호등 인식 결과와 진입·회전·복귀 상태 전이를 통합했다.
- **Sim-to-Real:** 차량 제원, 조향, 속도를 실측하고 방향별 lookup table과 Gazebo 인터페이스를 보정했다.

## 시스템 구성 {#system-architecture}

인지, 제어, 액추에이터 안전 계층 사이에 명확한 ROS 2 인터페이스를 두었다. 카메라 처리는 작업별로 분리해, 차선 추종에는 작고 빠른 입력을 사용하고 객체 인지에는 더 많은 영상 정보를 유지했다.

<ol class="architecture-flow" aria-label="종단간 시스템 구성">
  <li><strong>센서</strong><span>카메라 · LiDAR</span></li>
  <li><strong>라우팅·융합</strong><span>최신 프레임 · DBSCAN</span></li>
  <li><strong>인지</strong><span>이중 YOLO · OpenCV</span></li>
  <li><strong>계획·제어</strong><span>Stanley · Pure Pursuit</span></li>
  <li><strong>미션 관리자</strong><span>상태·안전 중재</span></li>
  <li><strong>차량</strong><span>VESC 어댑터 · watchdog</span></li>
</ol>

## 예선 {#qualifying-round}

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/qualifying-simulator.jpg' | relative_url }}" alt="공식 예선 시뮬레이터와 ROS 2 터미널 출력" loading="lazy" />
  <figcaption>3바퀴 자율주행 평가에 사용된 공식 예선 시뮬레이터.</figcaption>
</figure>

### 인지

시뮬레이터 영상에 ROI를 설정하고 bird’s-eye-view 변환, 흰색·노란색 차선용 HSV 마스크, sliding window 추적을 적용했다. 검출한 차선 점을 목표 경로로 변환하고, 디버깅 과정에서 RViz로 시각화했다.

### 판단

규칙 기반 유한상태기계로 라바콘 구간에서 차선 주행으로의 전환, 경찰차와 신호등 처리 순서, 지름길 좌회전, 바퀴 수 계산, 완주 판정을 관리했다. 동일한 결승선 검출이 여러 바퀴로 중복 집계되지 않도록 상태와 전이 조건을 구성했다.

### 제어

초기 Pure Pursuit 실험에서는 직선 구간에서 진동이 발생했다. 최종 예선 주행에는 PID 차선 제어를 사용했고, 경로 안정화, 미션 전이, 좌회전 복귀, 속도 정책을 함께 조정했다.

### 문제와 해결

3바퀴 총 기록을 276.00초에서 151.32초로 단축했다. 이 개선은 제어기 변경 하나의 결과가 아니라 전체 파이프라인을 반복적으로 수정한 결과다. 공개 저장소에는 주행 증거와 복원한 설계 기록이 남아 있지만, 당시 예선 원본 소스 코드는 공개되어 있지 않다.

## 본선 {#main-round}

### 인지

실차 시스템에서 차선 인지 분기는 320 px·최대 15 Hz, 객체 인지 분기는 640 px·최대 10 Hz로 분리했다. YOLO가 찾은 ROI 안에서 adaptive thresholding, Canny edge, Hough line, 다항식 피팅을 적용해 차선 기하를 복원했다. 장애물과 라바콘 처리를 위해 LiDAR cluster를 영상 검출 결과와 연관했다.

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/perception.jpg' | relative_url }}" alt="차선과 객체 검출 결과가 표시된 실차 카메라 프레임" loading="lazy" />
  <figcaption>최종 차선·객체 인지 검증 과정에서 기록한 실차 카메라 프레임.</figcaption>
</figure>

### 판단

동적 장애물, 정적 장애물, 지름길 진입을 각각 별도의 로직으로 처리했다. 인지 결과가 액추에이터를 직접 제어하지 않도록 목표 차선, 속도, 이벤트 파라미터로 변환해 전달했다.

### 제어

차선 주행에는 속도와 경로 상황에 따라 gain을 조정하는 Stanley 제어를 사용했다. 라바콘 미션에는 spline으로 생성한 중심 경로와 Pure Pursuit를 적용했다. 곡률의 크기와 S자 방향 전환 정보를 이용해 어려운 구간에 진입하기 전에 속도를 낮췄다.

### 미션 관리자

Mission Manager가 `STOP`, `PAUSED`, `LANE`, `OVERTAKE`, `CONE` 모드를 중재했다. 일시정지와 정지 조건이 일반 차선 주행 및 장애물 명령보다 항상 높은 우선순위를 갖도록 구성했다.

### 안전

카메라 timestamp를 제어 인터페이스 끝까지 유지했다. 오래된 상태가 전달되면 속도 0 명령을 생성했으며, 액추에이터 어댑터에는 명령 변환, 변화율 제한, feed-forward와 PI 보정, 0.30초 watchdog을 적용했다.

## 시뮬레이션과 Sim-to-Real {#simulation--sim-to-real}

차량 제원을 추측하지 않고 직접 측정해 모델에 반영했다. wheelbase 0.355 m, track 0.250/0.266 m, wheel radius 0.050 m, 질량 4.1 kg을 사용했다. 5 m 구간 주행 시간으로 속도 lookup table을 만들었고, 좌우 조향 응답이 다르게 측정되어 방향별 곡률 table을 분리했다.

<figure class="evidence-figure">
  <img src="{{ '/images/projects/kookmin2026/gazebo-course.jpg' | relative_url }}" alt="Gazebo로 재구성한 국민대학교 대회 코스" loading="lazy" />
  <figcaption>실측 데이터를 기반으로 Gazebo에 구성한 대회 코스와 차량 환경.</figcaption>
</figure>

이 환경은 완전한 digital twin이 아니라 실제적인 보정 체계에 가깝다. 타이어 거동, 액추에이터 지연, 카메라 위치, 추론 지연, 영상 domain gap은 여전히 중요한 한계로 남는다.

## 검증과 실험 {#validation--experiments}

원시 보정 측정값, 기록 영상 기반 인지 replay, 시뮬레이터 검사를 함께 사용해 시스템을 검증했다.

- 5 m 시험 구간에서 명령값 4는 0.399 m/s, 명령값 25는 2.222 m/s로 측정됐다.
- 조향 명령 크기 40에서 좌회전 반경이 우회전 반경보다 48.4% 크게 측정되어 방향별 table을 분리했다.
- Gazebo 최대 조향 반경 replay에서 보정한 조향 명령 ±40 기준 절대 오차는 우측 0.9%, 좌측 1.9%였다.
- 두 S자 구간 replay에서 속도 정책 수정 후 위험한 10→16 가속 이벤트가 각각 7→0회, 3→0회로 감소했다.

<figure class="evidence-figure evidence-figure--wide">
  <img src="{{ '/images/projects/kookmin2026/s-curve-validation.png' | relative_url }}" alt="차선 중심 오차와 속도 명령을 비교한 S자 구간 replay 그래프" loading="lazy" />
  <figcaption>S자 구간의 추종 및 속도 명령을 확인하기 위해 사용한 기록 영상 기반 replay. 영상 평면 오차는 차량 자세 오차와 동일하지 않다.</figcaption>
</figure>

## 문제와 해결 {#problems--solutions}

<div class="iteration-list">
  <section class="iteration">
    <h3>카메라 처리 적체와 오래된 제어 명령</h3>
    <dl>
      <div><dt>문제</dt><dd>카메라 작업이 FIFO queue에 쌓이면 제어기가 과거 장면에 반응할 수 있었다.</dd></div>
      <div><dt>원인</dt><dd>최신 프레임 우선 정책이 없었고, 명령 인터페이스가 데이터 생성 시각을 끝까지 전달하지 않았다.</dd></div>
      <div><dt>접근</dt><dd>처리 대기 중인 프레임을 가장 최신 프레임으로 교체하고 timestamp를 보존했으며, 오래된 상태에서는 차량을 정지하도록 했다.</dd></div>
      <div><dt>결과</dt><dd>프레임 적체가 구조적으로 발생하지 않도록 했고, 검증 가능한 0.30초 stale-state 정지 동작을 추가했다. 동일 조건의 전후 benchmark가 없어 근거 없는 지연 개선 비율은 제시하지 않았다.</dd></div>
    </dl>
  </section>

  <section class="iteration">
    <h3>서로 다른 두 작업을 하나의 검출기로 처리</h3>
    <dl>
      <div><dt>문제</dt><dd>차선 위치 추정에는 작고 빠른 입력이 유리했지만, 도로 객체를 인식하려면 더 많은 영상 정보가 필요했다.</dd></div>
      <div><dt>원인</dt><dd>하나의 multi-class 설정이 두 작업을 동일한 해상도와 주기로 처리하도록 강제했다.</dd></div>
      <div><dt>접근</dt><dd>차선 추론과 객체 추론을 분리하고, 각 worker에는 가장 최신 프레임만 전달했다.</dd></div>
      <div><dt>결과</dt><dd>최종 설정은 차선 320 px·최대 15 Hz, 객체 640 px·최대 10 Hz였다. 동등한 조건의 benchmark가 없어 전후 FPS 향상 수치는 제시하지 않았다.</dd></div>
    </dl>
  </section>

  <section class="iteration">
    <h3>대회 현장에서 처음 본 빨간색 광원</h3>
    <dl>
      <div><dt>문제</dt><dd>중계 카메라의 빨간 표시등을 신호등으로 오인해 차량이 `STOP` 상태에 머물렀다.</dd></div>
      <div><dt>원인</dt><dd>낮은 confidence threshold는 먼 거리 신호등의 recall에는 도움이 됐지만, 학습하지 않은 빨간 물체를 구분할 위치·기하·시간적 맥락이 부족했다.</dd></div>
      <div><dt>접근</dt><dd>최종 threshold를 높이고 반복 검출을 요구했으며, ROI와 상황 맥락 조건을 다음 검증 항목으로 정의했다.</dd></div>
      <div><dt>결과</dt><dd>해결이 끝났다고 과장하지 않고 실패 사례로 기록했다. 안전 상태뿐 아니라 안전 상태에서 벗어나는 복구 정책도 함께 검증해야 한다는 점을 확인했다.</dd></div>
    </dl>
  </section>
</div>

## 배운 점 {#lessons-learned}

- 시스템 성능은 대표 알고리즘 하나보다 인지, 판단, 제어, 하드웨어 사이의 인터페이스와 계약에 크게 좌우된다.
- 측정과 replay 결과는 한계를 명확히 적을 때 의미가 있다. 영상 평면 오차를 실제 차량의 물리적 추종 오차처럼 해석해서는 안 된다.
- Sim-to-Real 과정에서는 시뮬레이터 제어기를 조정하기 전에 방향별 비대칭성과 액추에이터 응답을 먼저 보정하는 것이 효과적이다.
- 인지가 처음 보는 환경을 만났을 때를 고려해 fail-safe 동작과 정상 상태로의 복구 동작을 함께 검증해야 한다.

## 저장소 {#repository}

저장소에는 상세 시스템 구조, 알고리즘, 보정 데이터, 검증 manifest, 대회 회고, 시뮬레이터 환경, ROS 2 workspace가 정리되어 있다.

<p class="repository-links"><a href="https://github.com/juuny0317-cmd/Kookmin2026">GitHub에서 Kookmin2026 열기 <span aria-hidden="true">↗</span></a></p>

주요 구현 자료: [카메라 인지](https://github.com/juuny0317-cmd/Kookmin2026/tree/main/ros2_ws/src/cam), [미션 관리자](https://github.com/juuny0317-cmd/Kookmin2026/blob/main/ros2_ws/src/mission_cone_drive/mission_cone_drive/mission_manager_node.py), [시뮬레이션](https://github.com/juuny0317-cmd/Kookmin2026/tree/main/simulation/xycar_gz_sim), [검증 자료](https://github.com/juuny0317-cmd/Kookmin2026/blob/main/docs/VALIDATION_EVIDENCE.md).
