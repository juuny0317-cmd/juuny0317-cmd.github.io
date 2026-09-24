---
permalink: /projects/hl-fma2026/
title: "HL FMA 2026 · 1/5 스케일 자율주행 차량"
excerpt: "듀얼 안테나 RTK GNSS, 전역경로, 미션 FSM, Stanley 제어와 NUCLEO-H723ZG를 통합한 실차 프로젝트."
author_profile: true
---

<p class="case-kicker">자율주행 · Team SVE · 완료</p>

<p class="project-result project-result--blue case-result">RESULT · 장려상</p>

<p class="intro">HL FMA 2026을 위해 1/5 스케일 전동 차량의 하드웨어를 제작하고, ROS 2 기반 위치추정·인지·미션 판단·경로 추종·안전 제어를 통합한 프로젝트다. 대회에서 장려상을 받았다.</p>

<figure class="case-hero case-hero--portrait-source">
  <img src="{{ '/images/projects/hl-fma2026/competition-vehicle-wide.jpg' | relative_url }}" alt="HL FMA 2026 대회 현장에 배치된 듀얼 GNSS 안테나 장착 1/5 스케일 자율주행 차량" />
  <figcaption>대회 현장의 Team SVE 차량. 두 GNSS 안테나, 센서 지지대와 차내 제어·전원 계통을 하나의 플랫폼에 통합했다.</figcaption>
</figure>

{% assign hl_fma_video = site.static_files | where: "path", "/videos/projects/hl-fma2026/drive-run.mp4" | first %}
{% if hl_fma_video %}
<section class="case-video case-video--player" id="drive-video" aria-label="HL FMA 차량 주행 영상">
  <video id="hl-fma-drive-video" autoplay muted loop playsinline controls preload="metadata">
    <source src="{{ '/videos/projects/hl-fma2026/drive-run.mp4' | relative_url }}" type="video/mp4" />
    브라우저가 HTML5 영상을 지원하지 않습니다.
  </video>
  <p class="media-note">음소거·반복 재생되며 기본 재생 속도는 2.0배다. 자동 재생이 제한되면 재생 버튼을 사용할 수 있다.</p>
</section>
<script>
  document.addEventListener("DOMContentLoaded", function () {
    var video = document.getElementById("hl-fma-drive-video");
    if (!video) return;
    var keepDoubleSpeed = function () {
      video.defaultPlaybackRate = 2.0;
      video.playbackRate = 2.0;
    };
    ["loadedmetadata", "canplay", "play", "ratechange"].forEach(function (eventName) {
      video.addEventListener(eventName, function () {
        if (video.playbackRate !== 2.0) keepDoubleSpeed();
      });
    });
    keepDoubleSpeed();
    var autoplayAttempt = video.play();
    if (autoplayAttempt && typeof autoplayAttempt.catch === "function") {
      autoplayAttempt.catch(function () {
        video.removeAttribute("autoplay");
      });
    }
  });
</script>
{% else %}
<section class="case-video-placeholder" id="drive-video" aria-label="주행 영상 준비 상태">
  <span>DRIVE VIDEO</span>
  <strong>영상 파일 대기 중</strong>
  <p>제공된 사진 번들에는 주행 영상이 없어 깨진 플레이어를 표시하지 않는다. 웹용 MP4를 <code>/videos/projects/hl-fma2026/drive-run.mp4</code>에 추가하면 자동 재생·음소거·반복·모바일 인라인 재생·2.0배속 컨트롤이 활성화된다.</p>
</section>
{% endif %}

<nav class="case-nav" aria-label="프로젝트 상세 목차">
  <a href="#drive-video">주행 영상</a>
  <a href="#overview">소개</a>
  <a href="#hardware">하드웨어</a>
  <a href="#system-architecture">시스템</a>
  <a href="#gnss-rtk">GNSS·RTK</a>
  <a href="#route-control">경로·제어</a>
  <a href="#perception-missions">인지·미션</a>
  <a href="#field-tools">TUI·Foxglove</a>
  <a href="#validation">시험·대회</a>
  <a href="#retrospective">문제 분석</a>
  <a href="#repository">저장소</a>
</nav>

## 프로젝트 소개 {#overview}

차량은 Ubuntu 22.04와 ROS 2 Humble에서 동작한다. UM982 듀얼 안테나 RTK GNSS로 전역 위치와 절대 heading을 얻고, 카메라와 RPLIDAR A3 인지 결과를 CSV 전역경로·미션 FSM과 결합한다. Linux 주 컴퓨터가 속도와 조향 목표를 계산하면 안전 감독을 거쳐 NUCLEO-H723ZG가 모터 드라이버와 실제 차량을 제어한다.

<dl class="fact-list">
  <div><dt>플랫폼</dt><dd>1/5 스케일 전동 차량</dd></div>
  <div><dt>실행 환경</dt><dd>Ubuntu 22.04 · ROS 2 Humble</dd></div>
  <div><dt>센서</dt><dd>UM982 · 듀얼 GNSS · 전방 카메라 · RPLIDAR A3</dd></div>
  <div><dt>제어</dt><dd>전진 Stanley 100% · feed-forward + PI 속도 제어</dd></div>
  <div><dt>임베디드</dt><dd>NUCLEO-H723ZG · MDD20A · MD10C</dd></div>
</dl>

### 주요 개발 내용

차체 분해와 센서 지지 구조 제작부터 전원·모터 제어 배선, 조향 위치 피드백, 듀얼 GNSS 장착, RTK waypoint 기록, ROS 2 주행 스택과 TUI·Foxglove 기반 현장 운용까지 함께 개발하고 시험했다.

## 차량 하드웨어 제작 {#hardware}

완구형 차체의 내장 부품과 배선 공간을 확인한 뒤 센서, 전원, 제어 보드가 들어갈 구조를 만들었다. 카메라·LiDAR·GNSS 안테나는 알루미늄 프로파일에 장착해 기준 자세와 안테나 baseline을 유지했다.

<div class="case-gallery case-gallery--two">
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/platform-build.jpg' | relative_url }}" alt="내장 부품을 분리하고 새 배선을 준비하는 초기 차량 차체" loading="lazy" />
    <figcaption>초기 차체 분해와 배선 공간 확인.</figcaption>
  </figure>
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/embedded-drive-control.jpg' | relative_url }}" alt="NUCLEO-H723ZG와 MDD20A 모터 드라이버를 연결해 확인하는 모습" loading="lazy" />
    <figcaption>NUCLEO-H723ZG와 구동 모터 드라이버 단품 연결.</figcaption>
  </figure>
</div>

최종 제어 보드는 `NUCLEO-H723ZG`다. 저장소의 `nucleo_h743zi2` 폴더는 별도 이식용 자료이며 실차 최종 구성으로 보지 않는다. NUCLEO는 조향 위치 센서의 ADC 값을 읽어 MD10C 조향모터를 폐루프로 제어하고, MDD20A에는 후륜 구동 명령을 전달한다. watchdog, 출력 제한과 fault feedback은 주 컴퓨터의 안전 감독과 함께 동작한다.

<figure class="evidence-figure evidence-figure--portrait">
  <img src="{{ '/images/projects/hl-fma2026/nucleo-wiring.jpg' | relative_url }}" alt="PWM, DIR, 전원과 접지가 라벨링된 차량 내부 NUCLEO 배선" loading="lazy" />
  <figcaption>전원·PWM·DIR·피드백을 구분해 연결한 차량 내부 배선.</figcaption>
</figure>

### 전력 계통

구동모터·조향모터의 24V 차량 전원과 컴퓨터·센서 전원을 분리했다. 별도 12V 100Ah LiFePO₄ 배터리는 600W 인버터를 거쳐 노트북과 USB 센서 허브에 전원을 공급한다. 전원 극성, 퓨즈, 케이블 규격과 체결 상태를 확인한 뒤 부하를 단계적으로 연결했다.

<div class="case-gallery case-gallery--two">
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/power-battery.jpg' | relative_url }}" alt="컴퓨터와 센서 전원에 사용한 12볼트 100암페어시 LiFePO4 배터리" loading="lazy" />
    <figcaption>컴퓨팅·센서용 12V 100Ah LiFePO₄ 배터리.</figcaption>
  </figure>
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/power-inverter-cabling.jpg' | relative_url }}" alt="600와트 인버터와 12AWG 전원 케이블을 준비하는 과정" loading="lazy" />
    <figcaption>600W 인버터와 전원 케이블 제작 과정.</figcaption>
  </figure>
</div>

## 시스템 아키텍처 {#system-architecture}

<ol class="architecture-flow architecture-flow--extended" aria-label="HL FMA 종단간 시스템 구성">
  <li><strong>센서 입력</strong><span>UM982 · Camera · RPLIDAR</span></li>
  <li><strong>위치추정</strong><span>RTK · dual heading · ENU</span></li>
  <li><strong>인지</strong><span>차선 · 신호 · 장애물</span></li>
  <li><strong>전역경로</strong><span>CSV · route progress · FSM zone</span></li>
  <li><strong>경로 추종</strong><span>Stanley · 속도 정책</span></li>
  <li><strong>미션 판단</strong><span>HILL · S · TRAFFIC · PARK</span></li>
  <li><strong>안전 제어</strong><span>preflight · freshness · watchdog</span></li>
  <li><strong>차량</strong><span>NUCLEO · MDD20A · MD10C</span></li>
</ol>

핵심 ROS 2 데이터 흐름은 `/gnss/fix`·`/gnss/status` → `/localization/gnss_pose`, 카메라·LiDAR → `/perception/state`, 경로 추종 → `/planning/path_command`, 미션 중재 → `/mission/command`, 차량 제어 → `/vehicle/actuator_command_raw`, 안전 감독 → `/vehicle/actuator_command_safe`, MCU feedback → `/vehicle/feedback` 순서다.

## GNSS·RTK와 듀얼 안테나 Heading {#gnss-rtk}

UM982의 주 안테나 위치와 두 안테나 사이의 baseline heading을 결합했다. NTRIP client가 보정 서버의 RTCM stream을 `/gnss/rtcm`으로 발행하면 UM982 serial node가 이를 수신기에 주입한다. 수신 상태는 `SINGLE`, `DGPS`, `RTK FLOAT`, `RTK FIXED`로 구분하고 position·velocity·heading의 age를 별도로 감시한다.

<figure class="evidence-figure evidence-figure--portrait">
  <img src="{{ '/images/projects/hl-fma2026/dual-gnss-antennas.jpg' | relative_url }}" alt="차량 진행 방향으로 떨어뜨려 배치한 두 개의 GNSS 안테나" loading="lazy" />
  <figcaption>차량 종방향 baseline과 고정 자세를 확보한 듀얼 GNSS 안테나.</figcaption>
</figure>

수신기의 진북 기준 시계방향 heading은 `yaw = π/2 − heading + mount offset`으로 ROS ENU yaw로 바꾼다. 이후 고정 datum으로 위도·경도를 local East/North로 투영하고, ANT1에서 후륜축 중심 `base_link`까지의 lever arm을 회전해 위치를 보정한다. 정지 상태에서도 dual heading을 사용할 수 있다는 점이 저속·정차 미션에 중요하다.

실제 NTRIP host, mountpoint, 계정과 비밀번호는 공개하지 않는다. 저장소에는 빈 설정과 `.example` 파일만 포함했다.

## 전역경로와 주행 알고리즘 {#route-control}

RTK 상태를 확인한 뒤 현장 waypoint를 기록하고, CSV에 좌표·누적 거리·속도·진행방향·미션 구역·FSM event를 함께 저장했다. 시험에서는 전체 Course 07과 직선·곡선·S자·주차·종점 구간을 같은 기준 좌표계에서 잘라 사용했다. 경로 전환부는 smoothing과 1.0m heading baseline으로 작은 waypoint 굴곡이 조향 명령에 직접 반영되는 것을 줄였다.

<figure class="evidence-figure evidence-figure--portrait">
  <img src="{{ '/images/projects/hl-fma2026/route-sections.jpg' | relative_url }}" alt="Course 07 전역경로에 신호등, S자와 종점 차선 구역을 색상으로 표시한 화면" loading="lazy" />
  <figcaption>실측 전역경로에 미션 구역과 YOLO pre-enable 구간을 매핑한 과정.</figcaption>
</figure>

최종 전진 주행은 직선과 곡선 모두 `STANLEY_100`을 사용한다. Pure Pursuit와 곡률도 계산하지만 Foxglove 분석용 진단 토픽이며 전진 조향 명령에 혼합하지 않는다. Stanley는 차량 앞쪽 control point의 heading error와 cross-track error를 실제 속도에 따라 결합하고 조향 한계로 제한한다.

## 인지·판단·미션 {#perception-missions}

<dl class="fact-list fact-list--wide">
  <div><dt>Camera</dt><dd>차선 offset·heading, 정지선, 신호등, 종점 차선 신호. baseline OpenCV와 YOLO route-zone gate를 함께 제공한다.</dd></div>
  <div><dt>LiDAR</dt><dd>scan cluster를 map 좌표 장애물로 투영하고, dummy 정지·S자 local detour·주차 경로 선택에 사용한다.</dd></div>
  <div><dt>HILL</dt><dd>CSV `HILL_STOP` 위치에서 정지·hold 후 adaptive hill-hold를 거쳐 재출발한다.</dd></div>
  <div><dt>TRAFFIC</dt><dd>정지선에 접근하고 red/unknown에서 정지한 뒤 green 확인을 지속해 출발한다.</dd></div>
  <div><dt>PARK</dt><dd>T/평행 주차 후보와 종점 차선을 2×2×2 route variant로 관리하고 진행방향 전환 전에 완전정지를 확인한다.</dd></div>
</dl>

본선에서는 좌회전 신호등 구간 이후 직선 복귀 중 오실레이션이 커져 연석에 충돌했다. 당시 주행 결과와 개선 방향은 아래 문제 분석에 정리했다.

## TUI와 Foxglove 대시보드 {#field-tools}

현장 TUI는 datum 측정, RTK 상태, 시나리오 선택, 목표 속도·PWM, MCAP 기록, Foxglove 연결과 전체 중지를 한 화면에서 관리한다.

<figure class="evidence-figure evidence-figure--wide">
  <img src="{{ '/images/projects/hl-fma2026/field-console.jpg' | relative_url }}" alt="직선, 곡선, S자, 주차와 구간별 시험을 선택하는 HL Mando Field Console" loading="lazy" />
  <figcaption>직선·곡선·S자·주차·Course 07 구간을 선택하는 현장 TUI.</figcaption>
</figure>

Foxglove에는 8개 경로 variant, 현재 waypoint의 FSM zone, camera, target/actual 속도·조향, CTE와 RTK 상태를 배치했다. 아래 화면은 기록 데이터를 다시 확인하는 **rosbag replay**이며 실제 본선 완주 화면이 아니다.

<div class="case-gallery case-gallery--stacked">
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/foxglove-mission-monitor.png' | relative_url }}" alt="S자 FSM과 카메라, 속도, 조향, CTE를 표시하는 Foxglove rosbag replay 대시보드" loading="lazy" />
    <figcaption>미션 FSM과 vehicle state를 함께 보는 replay monitor.</figcaption>
  </figure>
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/foxglove-route-integration.png' | relative_url }}" alt="8개 route variant와 waypoint 미션 구간을 비교하는 Foxglove rosbag replay 화면" loading="lazy" />
    <figcaption>2×2×2 route variant와 waypoint 미션 구간 통합 점검.</figcaption>
  </figure>
</div>

## 실차 시험과 대회 과정 {#validation}

개발은 차체·배선 제작 → 실내 구동·조향 시험 → 듀얼 GNSS 확인 → datum·waypoint 기록 → 직선/곡선/S자 실외 시험 → TUI·Foxglove 통합 → 대회 현장 운용 순서로 진행했다.

<div class="case-gallery case-gallery--two">
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/outdoor-field-test.jpg' | relative_url }}" alt="차량 위 노트북으로 GNSS와 경로를 점검하는 실외 시험" loading="lazy" />
    <figcaption>차량 위 노트북에서 GNSS와 경로를 확인한 실외 시험.</figcaption>
  </figure>
  <figure class="evidence-figure">
    <img src="{{ '/images/projects/hl-fma2026/night-integration-test.jpg' | relative_url }}" alt="야간에 차량 센서와 ROS 2 시스템을 함께 점검하는 모습" loading="lazy" />
    <figcaption>야간 센서·제어·운영 도구 통합 점검.</figcaption>
  </figure>
</div>

<figure class="evidence-figure evidence-figure--wide">
  <img src="{{ '/images/projects/hl-fma2026/team-and-vehicle.jpg' | relative_url }}" alt="HL FMA 대회 현장에서 차량과 함께 촬영한 건국대학교 Team SVE 팀원들" loading="lazy" />
  <figcaption>대회 현장의 Team SVE와 완성 차량.</figcaption>
</figure>

## 문제 분석과 배운 점 {#retrospective}

본선 두 번의 시도에서 좌회전 신호등 구간 이후 직선으로 복귀할 때 좌우 오실레이션이 커졌고 연석에 충돌했다. 이 문제를 숨기지 않고, 로그 부재로 원인을 확정할 수 없다는 한계를 포함해 다음 가설을 정리했다.

<div class="iteration-list">
  <section class="iteration">
    <h3>GNSS heading 갱신과 위치추정 지연</h3>
    <dl>
      <div><dt>가설</dt><dd>회전 종료 직후 heading 또는 pose 지연이 남아 제어기가 이전 자세에 반응했을 수 있다.</dd></div>
      <div><dt>검증</dt><dd>heading/pose age, 센서 timestamp와 command latency를 같은 시간축으로 replay한다.</dd></div>
      <div><dt>개선</dt><dd>시간 정렬, 지연 보상과 stale threshold를 실제 갱신주기 기준으로 검증한다.</dd></div>
    </dl>
  </section>
  <section class="iteration">
    <h3>실제속도와 Stanley 파라미터의 불일치</h3>
    <dl>
      <div><dt>가설</dt><dd>속도 대비 gain·softening이 커서 직선 복귀 중 보정 명령이 과도했을 수 있다.</dd></div>
      <div><dt>검증</dt><dd>actual/target speed, CTE, heading error와 steering command를 속도 구간별로 비교한다.</dd></div>
      <div><dt>개선</dt><dd>폐쇄 코스에서 gain sweep을 수행하고 속도별 파라미터를 검증한다.</dd></div>
    </dl>
  </section>
  <section class="iteration">
    <h3>경로 CSV의 작은 굴곡과 전환부</h3>
    <dl>
      <div><dt>가설</dt><dd>곡선→직선 전환에 남은 작은 굴곡이 heading reference를 좌우로 바꿨을 수 있다.</dd></div>
      <div><dt>검증</dt><dd>waypoint curvature와 1.0m tangent를 원본·smoothed 경로에서 비교한다.</dd></div>
      <div><dt>개선</dt><dd>곡률 제한 smoothing과 직선 정렬 구간 재기록을 별도로 평가한다.</dd></div>
    </dl>
  </section>
  <section class="iteration">
    <h3>명령과 실제 조향 응답의 차이</h3>
    <dl>
      <div><dt>가설</dt><dd>조향 deadband·slew·좌우 비대칭 때문에 명령과 실제 바퀴각이 달랐을 수 있다.</dd></div>
      <div><dt>검증</dt><dd>target/actual steering과 ADC feedback을 함께 기록한다.</dd></div>
      <div><dt>개선</dt><dd>좌우 응답을 실측해 deadband, rate limit과 보정표를 분리한다.</dd></div>
    </dl>
  </section>
</div>

다음 프로젝트에서는 MCAP을 기본 수집하고, 문제 구간 전후의 pose·heading·CTE·목표/실제 조향·속도를 재현 가능한 replay로 남길 계획이다.

## GitHub 저장소 {#repository}

ROS 2 packages, NUCLEO 자료, Course 07 경로, 운영 스크립트, Foxglove dashboard와 시험 절차를 저장소에서 확인할 수 있다. NTRIP 계정·비밀번호·토큰은 제외했다.

<p class="repository-links"><a href="https://github.com/juuny0317-cmd/HL_FMA_2026">GitHub에서 HL_FMA_2026 열기 <span aria-hidden="true">↗</span></a></p>
