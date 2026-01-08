
#  출입 감지 기반 실시간 보안 모니터링 시스템

본 프로젝트는 **출입 통제 실패(무단 침입, 원격 개방 등)** 로 인한  
보안 위협을 방지하기 위해,  **Button Sensor + Wi-Fi + Bluetooth 기반의 실시간 출입 감지 시스템**을 구현한 프로젝트입니다.

출입 감지 이벤트를 인터럽트 기반으로 처리하고,  
네트워크를 통해 서버에 로그를 저장한 뒤  
관리실에서 **LCD, Buzzer, LED를 통해 실시간 경보**를 제공하는 구조로 설계되었습니다.

---

##  프로젝트 개요
단순 센서 감지에 그치지 않고,

- MCU에서 출입 이벤트를 **인터럽트 기반으로 감지**
- Wi-Fi를 통해 서버로 **실시간 상태 전송**
- Raspberry Pi 서버에서 **출입 로그 DB 저장 및 웹 모니터링**
- Bluetooth를 통해 **현장 알림 장치(Arduino)**로 상태 전달

까지 이어지는 **보안 모니터링 시스템**을 구현합니다.

---

##  프로젝트 목표

###  실시간 출입 감지 및 경보
- Button Sensor 기반 출입(Open) 감지
- 관리자 버튼(Close)을 통한 경보 해제
- 침입 발생 시 즉각적인 시각·청각 알림 제공

###  네트워크 기반 상태 전달
- STM32 → ESP-01(Wi-Fi) → Raspberry Pi 서버 데이터 전송
- 서버에서 출입 상태 로그 DB 저장
- 웹 페이지를 통한 실시간 출입 상태 확인

###  관리실 알림 시스템
- Bluetooth(HC-06) 기반 Arduino 통신
- LCD에 출입 상태 및 시간 표시
- Buzzer + LED를 통한 경보 출력

---

##  개발 환경

### 측정부 (출입 감지)
- **Button Sensor**
- **STM32**
- **ESP-01 (Wi-Fi)**
- **HC-06 (Bluetooth)**

### 메인 서버
- **Raspberry Pi**
- **MariaDB**
- **PHP 기반 웹 서버**

### 동작부 (관리실)
- **Arduino**
- **LCD (I2C)**
- **Buzzer**
- **LED**

---

###  시스템 구성도
<img width="600"  alt="image" src="https://github.com/user-attachments/assets/95868c66-adb6-4e08-af6e-79b6d23a1b30" />

- **Button Sensor 1** → DOOR OPEN 감지 (침입 발생)  
- **Button Sensor 2** → 관리자 CLOSE (경보 해제)  
- **STM32** → 센서 신호 감지 후 Wi-Fi로 데이터 전송  
- **ESP-01** → Raspberry Pi 서버로 신호 전달  
- **Raspberry Pi** → 출입 로그 DB 저장 + 웹 서버 운영  
- **HC-06 (Bluetooth)** → Arduino로 명령 전달  
- **Arduino** → LCD, Buzzer, LED로 침입 상태 표시  

---
##  시스템 동작 흐름

1. 출입 발생 → Button Sensor 인터럽트 발생
2. STM32에서 출입 상태 판별
3. ESP-01(Wi-Fi)을 통해 Raspberry Pi 서버로 상태 전송
4. Raspberry Pi
   - MariaDB에 출입 로그 저장
   - 웹 페이지에 실시간 상태 반영
5. Arduino
   - Bluetooth(HC-06)로 최신 상태 수신
   - LCD, Buzzer, LED로 경보 출력

---

## 기술 구현 (Implementation Details)

### 1) 인터럽트 기반 출입 감지 (STM32)
- 출입 이벤트를 **폴링이 아닌 인터럽트 방식**으로 처리
- 빠른 반응 속도 및 CPU 부하 최소화
- 침입 발생 시 즉시 네트워크 전송
- 55
### 2) Wi-Fi 통신 (ESP-01, AT Command)
- ESP-01을 AT Command 방식으로 제어
- 출입 상태를 문자열 프로토콜로 서버에 전달
- 네트워크 지연 상황에서도 상태 손실 없이 처리
- 
### 3) 서버 로그 관리 (Raspberry Pi)
- PHP + MariaDB 기반 REST 구조
- 출입 발생/해제 이력을 DB에 저장
- 시간 정보 기반 히스토리 관리
- 웹 페이지를 통해 관리자가 실시간 상태 확인 가능

### 4) Bluetooth 기반 현장 알림 시스템 (Arduino)
- HC-06 Bluetooth를 통해 서버 상태 수신
- 3초 주기로 최신 출입 로그 요청
- LCD에 날짜·시간·출입 상태 출력

### 5) Buzzer / LED 경보 제어
- 침입 상태(OPEN) 시:
  - Buzzer 주기적 ON/OFF
  - LED 동시 점등
- 정상 상태(CLOSE) 시:
  - 경보 즉시 해제
- Non-blocking 방식으로 Buzzer 토글 구현

###  구현 과정

#### 1) **STM32 – 인터럽트 기반 출입 감지 및 Wi-Fi 송신**
출입 감지를 인터럽트로 처리하고  
ESP-01(AT Command)을 통해 서버로 상태 전송  
<img width="271" height="336" alt="image" src="https://github.com/user-attachments/assets/b04dd941-5d7a-47f3-a630-7217486a2401" />


#### 2) **Raspberry Pi – 웹 서버 및 MariaDB 로그 저장**
- PHP + MariaDB 기반 REST 구조  
- 침입/해제 상태를 DB에 기록하여 히스토리 관리  
<img width="584" height="410" alt="image" src="https://github.com/user-attachments/assets/88d23f3a-7b56-4f3d-985f-a56ff1fd47a0" />

#### 3) **Arduino – 실시간 알림 표시**
- HC-06 Bluetooth로 최신 로그 수신  
- 3초마다 상태 요청 & LCD·LED·Buzzer로 알림 출력  
---

###  결과 및 시연

- 침입 발생 시 **LCD + Buzzer + LED**로 경고 알림  
- 관리실(웹 페이지)에서 **실시간 출입 상태 확인**
- Raspberry Pi에서 모든 로그가 DB에 자동 저장  
