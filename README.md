# Project-InstructionDetectionsystem
# 출입 감지 기반 실시간 보안 모니터링 시스템


### 프로젝트 개요
무단 침입 및 원격 개방 등 출입 통제 실패로 인한 보안 위협을 방지하기 위해  
**Button Sensor + Wi-Fi + Bluetooth 기반의 실시간 출입 감지 시스템**을 구현하였습니다.

- 버튼 센서를 통한 출입 감지  
- LCD, 부저(Buzzer), LED를 활용한 침입 상황 알림  
- 관리실에서 직관적으로 출입 상태 파악 및 즉각 대응 가능  

---

###  개발 환경

#### **측정부**
- Button Sensor  
- STM32  
- HC-06 (Bluetooth)  
- ESP-01 (Wi-Fi)

#### **메인 서버**
- Raspberry Pi  
- MariaDB  
- PHP 기반 웹 서버  

#### **동작부**
- Arduino  
- LCD  
- Buzzer  
- LED  

---

###  시스템 구성도

- **Button Sensor 1** → DOOR OPEN 감지 (침입 발생)  
- **Button Sensor 2** → 관리자 CLOSE (경보 해제)  
- **STM32** → 센서 신호 감지 후 Wi-Fi로 데이터 전송  
- **ESP-01** → Raspberry Pi 서버로 신호 전달  
- **Raspberry Pi** → 출입 로그 DB 저장 + 웹 서버 운영  
- **HC-06 (Bluetooth)** → Arduino로 명령 전달  
- **Arduino** → LCD, Buzzer, LED로 침입 상태 표시  

---

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
