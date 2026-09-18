# LG Display Production & Quality Monitoring Web Service

> **Vibe Coding을 활용한 생산·품질 데이터 조회 및 검사 결과 관리 웹 서비스**

## 📅 날짜

**09월 10일**

---

## 🛠 사용 라이브러리

### Python

* **Python**

  * 전체 웹 서비스 실행
  * 생산·품질 데이터 처리
  * JSON 및 CSV 데이터 관리

* **Flask 3.1.2**

  * 웹 서버 구성
  * 생산·품질 데이터 조회 API 구현
  * 검사 결과 저장 API 구현
  * 라인별 설비 상태 제어

* **Werkzeug**

  * Flask 웹 서버 실행 및 관리

* **IPython.display**

  * Google Colab에서 Cloudflared 외부 접속 주소 출력

### Front-End

* **HTML5**

  * 생산·품질 조회 화면 구성
  * 검사 결과 입력 폼 구성

* **CSS3**

  * 대시보드 UI 디자인
  * 반응형 웹 화면 구성

* **JavaScript**

  * 라인 및 LOT 필터 처리
  * 생산·품질 데이터 조회
  * 입력 데이터 전송
  * `async/await` 기반 Flask 서버와 비동기 통신

* **Three.js 0.170.0**

  * WebGL 기반 패널 검사·이송 설비 3D 시각화

* **OrbitControls**

  * 3D 설비 회전
  * 확대 및 축소
  * 사용자 카메라 조작

### Server / Network

* **Cloudflared**

  * Google Colab에서 실행 중인 Flask 서버에 외부에서 접속할 수 있도록 Cloudflare Tunnel 구성

### Data

* **JSON**

  * 검사 결과 데이터 저장
  * Colab 런타임 내 기록 유지

* **CSV**

  * 현재 조회 조건의 검사 결과 다운로드
  * 전체 생산·품질 기록 보관

### Python Standard Library

프로젝트 실행 및 데이터 처리를 위해 다음 Python 기본 라이브러리를 사용하였습니다.

* `asyncio`
* `csv`
* `importlib.util`
* `io`
* `json`
* `os`
* `pathlib`
* `re`
* `socket`
* `subprocess`
* `sys`
* `threading`
* `urllib.request`
* `zipfile`

추가로 Colab 파일 다운로드를 위해 다음 기능을 사용하였습니다.

* `google.colab.files`

---

## 📌 설명

본 프로젝트는 Google Colab 환경에서 **Python Flask, HTML, CSS, JavaScript, Three.js, Cloudflared**를 이용하여 제작한 디스플레이 생산·품질 정보 조회 웹 서비스입니다.

사용자는 생산 라인과 LOT를 선택하여 해당 범위의 **생산량, 생산 달성률, 검사 수량, 불량 수량, 불량률 및 불량 유형별 수량**을 확인할 수 있습니다.

Day 1에서 구현한 기본적인 생산·품질 현황 및 3D 설비 모니터링 기능에서 확장하여 Day 2에서는 다음과 같은 기능을 추가하였습니다.

* 라인별 조회
* LOT별 조회
* 불량 유형 비교
* 신규 LOT 검사 결과 입력
* 입력 데이터 검증
* 검사 결과 JSON 저장
* CSV 내보내기
* 라인별 3D 설비 상태 관리
* 재실행 시 기존 검사 기록 유지

본 프로젝트에서 사용되는 데이터 및 3D 설비는 모두 **교육 목적의 가상 데이터와 모의 설비**입니다.

---

## 🔍 주요 기능

### 1. 라인 및 LOT별 생산·품질 조회

사용자는 다음과 같은 조건을 선택하여 생산 및 품질 정보를 조회할 수 있습니다.

```text
전체 라인
 ├─ 전체 LOT
 │
라인 A
 ├─ 전체 LOT
 ├─ A01
 ├─ A02
 └─ 추가 LOT
 │
라인 B
 ├─ 전체 LOT
 ├─ B01
 └─ B02
```

라인을 변경하면 LOT 선택은 자동으로 **전체 LOT**로 초기화되고, 선택한 라인에 포함된 LOT만 표시됩니다.

---

### 2. 생산 달성률 계산

생산 달성률은 선택한 범위의 전체 생산량과 목표 생산량을 이용하여 계산합니다.

```text
생산 달성률
= 생산 합계 ÷ 목표 합계 × 100
```

기본 가상 데이터 전체 기준으로 다음 결과를 확인할 수 있습니다.

| 구분     |       값 |
| ------ | ------: |
| 목표 생산량 |  1,800개 |
| 생산량    |  1,450개 |
| 생산 달성률 | 약 80.6% |

---

### 3. 불량률 계산

불량 수량은 다음 세 가지 불량 유형을 합산하여 계산합니다.

```text
불량 수량
= 스크래치 + 얼룩 + 라인 불량
```

불량률은 다음과 같이 계산합니다.

```text
불량률
= 불량 합계 ÷ 검사 합계 × 100
```

검사 수량이 0개인 경우에는 불량률을 임의로 `0%`로 표시하지 않고 다음과 같이 표시합니다.

```text
—
```

---

### 4. 불량 유형 분석

불량 유형은 다음 세 종류로 구성하였습니다.

* 스크래치
* 얼룩
* 라인 불량

선택한 라인 또는 LOT에 따라 각 불량 유형의 수량이 함께 변경됩니다.

예를 들어 `B01`의 경우 다음과 같습니다.

| 불량 유형 | 수량 |
| ----- | -: |
| 스크래치  | 3개 |
| 얼룩    | 2개 |
| 라인 불량 | 0개 |
| 총 불량  | 5개 |

화면의 불량 유형별 수량 합계와 상단의 전체 불량 수량이 동일한 데이터를 사용하도록 구성하였습니다.

---

## 💾 검사 결과 입력 및 저장

### 5. 새로운 LOT 검사 결과 입력

웹 화면에서 새로운 LOT의 검사 결과를 직접 입력할 수 있도록 구현하였습니다.

입력 항목은 다음과 같습니다.

| 항목          | 설명      |
| ----------- | ------- |
| Line        | 생산 라인   |
| LOT         | LOT 번호  |
| Target      | 목표 생산량  |
| Produced    | 생산량     |
| Inspected   | 검사 수량   |
| Scratch     | 스크래치 불량 |
| Spot        | 얼룩 불량   |
| Line Defect | 라인 불량   |

---

### 6. 입력 데이터 검증

잘못된 생산·품질 데이터가 저장되지 않도록 서버에서도 데이터를 검증합니다.

주요 검증 조건은 다음과 같습니다.

```text
목표 생산량 ≥ 1

생산량 ≥ 0

검사 수량 ≥ 0

각 불량 수량 ≥ 0

검사 수량 ≤ 생산량

전체 불량 수량 ≤ 검사 수량
```

또한 같은 라인과 LOT가 이미 존재할 경우 중복 저장을 거부합니다.

예를 들어,

```text
라인 A / LOT A03
```

이 이미 저장되어 있다면 다시 같은 LOT를 입력해도 새로운 행이 생성되지 않습니다.

---

### 7. JSON 데이터 저장

새로운 검사 결과는 다음 파일에 저장됩니다.

```text
/content/02_lgd_web/02_records.json
```

웹 서비스를 다시 실행할 때 해당 파일이 존재하면 기존 검사 결과를 다시 불러옵니다.

따라서 같은 Colab 런타임 내에서는 서버를 재실행해도 기존에 저장한 LOT 데이터를 유지할 수 있습니다.

단, Colab 런타임 자체가 초기화되면 `/content`에 저장된 파일은 삭제될 수 있습니다.

---

## 📊 CSV 내보내기

### 8. 조회 결과 CSV

현재 선택한 라인 및 LOT의 결과를 CSV 파일로 다운로드할 수 있도록 구현하였습니다.

CSV 파일명은 다음과 같습니다.

```text
02_LGD_Inspection_Results.csv
```

CSV의 주요 데이터 구조는 다음과 같습니다.

```text
line
lot
target
produced
inspected
scratch
spot
line_defect
defects
```

`defects` 값은 다음 세 가지 불량 유형을 더하여 계산합니다.

```text
defects
= scratch + spot + line_defect
```

CSV 파일은 한글 호환성을 고려하여 **UTF-8 BOM**을 포함하도록 구성하였습니다.

---

### 9. 조회 화면과 CSV 조건 일치

화면에서 선택한 조회 조건과 CSV의 데이터 범위가 동일하도록 구성하였습니다.

예를 들어,

```text
Line : A
LOT  : A03
```

을 선택한 경우 CSV에는 A03 데이터만 포함됩니다.

전체 조회를 선택한 경우에는 저장된 전체 LOT 데이터가 포함됩니다.

---

## 📦 기록 파일 보관

현재 저장된 JSON 및 전체 검사 결과 CSV를 ZIP 파일로 묶어 다운로드할 수 있도록 구현하였습니다.

생성되는 파일은 다음과 같습니다.

```text
02_LGD_Inspection_Data.zip
```

ZIP 내부 구조는 다음과 같습니다.

```text
02_LGD_Inspection_Data.zip
│
├── 02_records.json
└── 02_LGD_Inspection_Results.csv
```

이를 통해 Colab 런타임이 종료되기 전에 검사 결과 데이터를 로컬 환경에 보관할 수 있습니다.

---

## 🏭 Three.js 기반 3D 설비

### 10. 패널 검사·이송 설비 시각화

Three.js와 WebGL을 이용하여 디스플레이 패널 검사·이송 설비를 3D로 표현하였습니다.

설비는 다음 요소로 구성됩니다.

* 금속 외장
* 프레임
* 패널 이송 롤러
* 디스플레이 패널
* 검사 헤드
* 유리 덮개
* 상태등

패널은 롤러 위에서 이동하도록 구현하였으며 검사 헤드는 패널 상단에 배치하였습니다.

---

### 11. 3D 설비 조작

OrbitControls를 적용하여 사용자가 3D 설비를 직접 확인할 수 있습니다.

```text
마우스 드래그 → 설비 회전

마우스 휠 → 확대 / 축소
```

화면 크기가 변경될 경우 Three.js의 카메라 Aspect Ratio와 Renderer 크기도 함께 변경됩니다.

---

## ⚙️ 라인별 설비 상태 관리

라인 A와 라인 B의 가동 상태를 각각 별도로 관리합니다.

예를 들어 다음과 같이 사용할 수 있습니다.

```text
라인 B 선택
     ↓
정지
     ↓
라인 A 선택
     ↓
A는 가동 상태
     ↓
라인 B 재선택
     ↓
B는 기존 정지 상태 유지
```

따라서 특정 라인을 정지한 후 다른 라인을 조회하더라도 각 라인의 상태가 개별적으로 유지됩니다.

전체 라인을 조회할 경우 3D 설비의 표시 및 조작 대상은 **라인 A**로 설정합니다.

---

## 🔄 웹 서비스 구조

전체 시스템 구성은 다음과 같습니다.

```text
사용자 Browser
       ↓
HTML / CSS / JavaScript
       ↓
Fetch API / async-await
       ↓
Cloudflare Tunnel
       ↓
Cloudflared
       ↓
Python Flask Server
       ↓
생산·품질 데이터 처리
       ↓
JSON Data
```

3D 설비 화면은 다음 구조로 동작합니다.

```text
JavaScript
     ↓
Three.js
     ↓
WebGL
     ↓
3D 패널 검사·이송 설비
```

---

## ☁️ Google Colab 외부 접속

본 프로젝트는 Google Colab에서 실행되기 때문에 기본적으로 로컬 Flask 서버에 외부 사용자가 직접 접근할 수 없습니다.

이를 해결하기 위해 Cloudflared를 사용하여 임시 Cloudflare Tunnel을 생성합니다.

```text
Google Colab
      ↓
Flask Server
      ↓
localhost
      ↓
Cloudflared
      ↓
Cloudflare Quick Tunnel
      ↓
외부 접속 URL
```

생성된 URL을 웹 브라우저에서 열면 Colab에서 실행 중인 웹 서비스를 확인할 수 있습니다.

Cloudflared에서 생성한 주소는 임시 주소이며 Colab 런타임 또는 터널이 종료되면 기존 주소를 다시 사용할 수 없습니다.

---

## 🎯 프로젝트 목적

본 프로젝트의 목적은 생성형 AI를 활용한 **Vibe Coding 방식으로 생산·품질 관리 웹 서비스를 구현하는 과정**을 학습하는 것입니다.

단순히 화면을 만드는 것뿐만 아니라 다음과 같은 생산·품질 관리의 기본적인 데이터 처리 과정을 웹 서비스에 적용하였습니다.

```text
생산·품질 데이터 발생
        ↓
라인 / LOT 조회
        ↓
생산 및 품질 지표 계산
        ↓
불량 유형 분석
        ↓
신규 검사 결과 입력
        ↓
입력 데이터 검증
        ↓
JSON 데이터 저장
        ↓
CSV 결과 출력
        ↓
3D 설비 상태 확인
```

이를 통해 생산 및 품질 데이터를 웹 환경에서 **조회 → 분석 → 입력 → 저장 → 출력**하는 기본적인 MES 형태의 업무 흐름을 경험할 수 있도록 구성하였습니다.

---

## ⚠️ 주의사항

본 프로젝트에서 사용한 다음 정보는 모두 **교육용 가상 데이터**입니다.

* 생산량
* 목표 생산량
* 검사 수량
* LOT
* 불량 수량
* 불량 유형
* 설비 상태

Three.js를 이용하여 표현한 패널 검사·이송 설비 역시 실제 LG디스플레이 설비의 구조, CAD 데이터 또는 내부 설비 정보를 사용하지 않은 **모의 3D 설비**입니다.

또한 검사 실적 및 불량 수량은 3D 설비의 모의 동작으로 증가하지 않으며, 저장된 검사 기록만을 기준으로 계산합니다.

---

## 📚 참고 문헌

### Google Colab

**Google Colab FAQ**

https://research.google.com/colaboratory/faq.html

Google Colab의 Python 실행 환경 및 런타임 구조를 참고하였습니다.

---

### Cloudflare

**Cloudflare Quick Tunnels**

https://developers.cloudflare.com/cloudflare-one/networks/connectors/cloudflare-tunnel/do-more-with-tunnels/trycloudflare/

Google Colab에서 실행한 Flask 서버를 외부에서 접속하기 위한 Cloudflare Tunnel 구성 방법을 참고하였습니다.

---

### Three.js

**Three.js Installation**

https://threejs.org/manual/en/installation.html

Three.js 모듈 구성 및 WebGL 기반 3D 환경 구현 방법을 참고하였습니다.

---

## 📂 프로젝트 파일 구조

```text
LGD-Production-Quality-Monitoring-Day2/
│
├── 02_LGD_Vibe_Coding_Day2.ipynb
│
└── README.md
```

웹 서비스 실행 후 Colab 내부에는 다음과 같은 데이터가 생성됩니다.

```text
/content/02_lgd_web/
│
├── 02_records.json
├── cloudflared
├── 02_cloudflared.log
└── static/
```

검사 데이터를 별도로 내려받을 경우 다음 파일이 생성됩니다.

```text
02_LGD_Inspection_Data.zip
│
├── 02_records.json
└── 02_LGD_Inspection_Results.csv
```

---

## 🖥 개발 환경

```text
Google Colab
Python
Flask 3.1.2
Werkzeug
HTML5
CSS3
JavaScript
Three.js 0.170.0
OrbitControls
WebGL
JSON
CSV
Cloudflared
```

---

## 📝 Summary

**LG Display Production & Quality Monitoring Web Service**는 Google Colab과 Flask를 기반으로 생산·품질 데이터를 라인 및 LOT 단위로 조회하고 관리하는 교육용 웹 프로젝트입니다.

Day 2에서는 기본 생산 현황 조회 기능에서 확장하여 **라인·LOT 필터링, 불량 유형 분석, 신규 검사 결과 입력 및 검증, JSON 저장, CSV 내보내기, 라인별 3D 설비 상태 관리** 기능을 구현하였습니다.

또한 Three.js를 활용해 패널 검사·이송 설비를 3D로 표현하고 Cloudflared를 이용하여 Colab에서 실행 중인 웹 서비스를 외부 브라우저에서 확인할 수 있도록 구성하였습니다.
