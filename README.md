# 편결이

사용자가 편의점에서 간편결제로 결제할 수 있는 시스템입니다. 
spring을 기반으로 계좌 관리 시스템을 개발∙실습 하기 위한 목적으로 만들었습니다.

## 🎯 서비스 개요

계좌 생성, 조회, 해지 서비스를 제공합니다.
뿐만 아니라 거래, 거래 조회, 거래 취소 서비스를 제공합니다.

## 🛠️ 기술 스택
![spring boot](https://img.shields.io/badge/spring%20boot-6DB33F?style=for-the-badge&logo=spring%20boot&logoColor=white)
![spring jpa](https://img.shields.io/badge/spring%20jpa-6DB33F?style=for-the-badge&logo=spring%20jpa&logoColor=white)
<br />
![h2 database](https://img.shields.io/badge/H2_Database-blue?style=for-the-badge)
![redis](https://img.shields.io/badge/redis-DC382D?style=for-the-badge&logo=redis&logoColor=white)
<br />
![jwt](https://img.shields.io/badge/jwt-black?style=for-the-badge&logo=json%20web%20tokens)
![gradle](https://img.shields.io/badge/gradle-02303A?style=for-the-badge&logo=gradle&logoColor=white)
![junit](https://img.shields.io/badge/junit-25A162?style=for-the-badge&logo=junit5&logoColor=white)
![postman](https://img.shields.io/badge/postman-FF6C37?style=for-the-badge&logo=postman&logoColor=white)

## ✅ 계좌 관련 API

### 1. 계좌 생성
- 파라미터 : 사용자 아이디, 초기 잔액 
- 결과
    1. **실패** : 사용자 없는 경우, 계좌가 10개(사용자당 최대 보유 가능 계좌) 인 경우 실패 응답
    2. **성공**
      - 계좌번호는 10자리 숫자(순차 증가), 만약 중복시 ACCOUNTNUMBER_ALREADY_USED 에러 발생
      - 응답정보 : 사용자 아이디, 생성된 계좌 번호, 등록일시(LocalDateTime)

[ 계좌 생성시 주의 사항 ]

    - 계좌 생성 시 계좌 번호는 10자리의 정수로 구성되며, 기존에 동일 계좌 번호가 있는지 중복체크를 해야 한다.
    - 기본적으로 계좌번호는 순차 증가 방식으로 생성한다.

### 2. 계좌 해지

- 파라미터 : 사용자 아이디, 계좌 번호
- 결과
    1. **실패** : 사용자 없는 경우, 사용자 아이디와 계좌 소유주가 다른 경우, 계좌가 이미 해지 상태인 경우, 잔액이 있는 경우 실패 응답
    2. **성공**
        - 응답 : 사용자 아이디, 계좌번호, 해지일시

### 3. 계좌 확인

- 파라미터 : 사용자 아이디
- 결과
    1. **실패** : 사용자 없는 경우 실패 응답
    2. **성공**
        - 응답 : (계좌번호, 잔액) 정보를 Json list 형식으로 응답

## ✅ 거래(Transaction) 관련 API

### 1) 잔액 사용

- 파라미터 : 사용자 아이디, 계좌 번호, 거래 금액
- 결과
    1. **실패** : 사용자 없는 경우, 사용자 아이디와 계좌 소유주가 다른 경우, 계좌가 이미 해지 상태인 경우, 거래금액이 잔액보다 큰 경우, 거래금액이 너무 작거나 큰 경우 실패
   응답
    2. **성공**
        - 응답 : 계좌번호, transaction_result, transaction_id, 거래금액, 거래일시

### 2) 잔액 사용 취소

- 파라미터 : transaction_id, 계좌번호, 거래금액
- 결과
    1. **실패** : 원거래 금액과 취소 금액이 다른 경우, 트랜잭션이 해당 계좌의 거래가 아닌경우 실패 응답
    2. **성공**
       - 응답 : 계좌번호, transaction_result, transaction_id, 취소 거래금액, 거래일시

### 3) 거래 확인

- 파라미터 : transaction_id
- 결과
    1. **실패** : 해당 transaction_id 없는 경우 실패 응답
    2. **성공**
        - 응답 : 계좌번호, 거래종류(잔액 사용, 잔액 사용 취소), transaction_result, transaction_id, 거래금액, 거래일시
        - 성공거래 뿐 아니라 실패한 거래도 거래 확인 가능

## 🤔 관심 가진 부분

> 일관성 있는 예외처리를 위해 errorCode, errorMessage로 **공통의 응답 형식**으로 구현

> 동시성 문제 고민의 결과 **Redis를 사용해 AOP 구현**

> mockito를 사용해 Controller, Service **유닛 테스트** 작성
