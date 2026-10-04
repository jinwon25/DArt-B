# SQL Assignment 5주차


## 1. 날짜 및 시간 데이터 이해하기(2)(EXTRACT, DATETIME_TRUNC, PARSE_DATETIME, FORMAT_DATETIME)

### DATETIME 함수 - CURRENT_DATETIME
- **CURRENT_DATETIME([time_zone]):** 현재 DATETIME출력
![스크린샷](../image/screenshot44.png)

### DATETIME 함수 - EXTRACT
- DATETIME에서 특정 부분만 추출하고 싶은 경우
- **EXTRACT(part FROM datetime_expression)**
![스크린샷](../image/screenshot45.png)

**요일을 추출하고 싶은 경우**
- EXTRACT(**DAYOFWEEK** FROM datetime_col)
- 한 주의 첫날이 일요일인 [1, 7] 범위의 값을 반환
![스크린샷](../image/screenshot46.png)

### DATETIME 함수 - DATETIME_TRUNC
- DATE과 HOUR만 남기고 싶은 경우 => **시간 자르기**
- DATETIME_TRUNC(datetime_col, **HOUR**)
![스크린샷](../image/screenshot47.png)

### DATETIME 함수 - PARSE_DATETIME
- 문자열로 저장된 DATETIME을 DATETIME 타입으로 바꾸고 싶은 경우
- PARSE_DATETIME('문자열의 형태', 'DATETIME 문자열') AS datetime
![스크린샷](../image/screenshot48.png)

### DATETIME 함수 - FORMAT_DATETIME
- DATETIME 타입 데이터를 특정 형태의 문자열 데이터로 변환하고 싶은 경우
- 문자열 => DATETIME : PARSE_DATETIME
- DATETIME => 문자열 : FORMAT_DATETIME

### DATETIME 함수 - LAST_DAY
- 마지막 날을 알고 싶은 경우 : 자동으로 월의 마지막 값을 계산해서 특정 연산을 할 경우
![스크린샷](../image/screenshot49.png)

### DATETIME 함수 - DATETIME_DIFF
- 두 DATETIME의 차이를 알고 싶은 경우
- DATETIME_DIFF(첫번째 DATETIME, 두번째 DATETIME, 궁금한 차이)
![스크린샷](../image/screenshot50.png)


## 2. 시간데이터 연습문제

### 1) 트레이너가 포켓몬을 포획한 날짜(catch_date)를 기준으로, 2023년 1월에 포획한 포켓몬의 수를 계산해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 포켓몬의 수
- 쿼리 계산 방법: COUNT
- 데이터의 기간: 2023년 1월!
- 사용할 테이블: trainer_pokemon
- Join KEY: X
- 데이터 특징: 직접 봐야함!
    - catch_datetime: UTC. TIMESTAMP 타입 => 컬럼의 이름은 datetime인데 TIMESTAMP 타입으로 저장되어 있다!
    - catch_date => KR 기준? UTC 기준?

**데이터 검증을 위한 쿼리**
```js
SELECT
  *
FROM (
SELECT
  id,
  catch_date,
  DATE(DATETIME(catch_datetime, 'Asia/Seoul')) AS catch_datetime_kr_date
FROM basic.trainer_pokemon
)
WHERE
  catch_date != catch_datetime_kr_date
  catch_date = catch_datetime_kr_date
```

```js
SELECT
  COUNT(DISTINCT id) AS cnt
FROM basic.trainer_pokemon
WHERE
  EXTRACT(YEAR FROM DATETIME(catch_datetime, 'Asia/Seoul)) = 2023
  AND EXTRACT(MONTH FROM DATETIME(catch_datetime, 'Asia/Seoul)) = 1
```

### 2) 배틀이 일어난 시간(battle_datetime)을 기준으로, 오전 6시에서 오후 6시 사이에 일어난 배틀의 수를 계산해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 오전 6시 \~ 오후 6시 배틀의 수
- 쿼리 계산 방법: COUNT
- 데이터의 기간: 일자는 상관없고, 오전 6시 \~ 오후 6시
- 사용할 테이블: battle
- Join KEY: X
- 데이터 특징: 확인
    - battle_date: battle_datetime 기반으로 만들어진 DATE
    - battle_datetime: DATETIME
    - battle_timestamp: TIMESTAMP

**데이터 검증을 위한 쿼리**
```js
SELECT
  id,
  battle_datetime,
  DATETIME(battle_timestamp, 'Asia/Seoul') AS battle_timestamp_kr
  COUNTIF(battle_datetime = DATETIME(battle_timestamp, 'Asia/Seoul')) AS battle_datetime_same_battle_timestamp_kr
  COUNTIF(battle_datetime != DATETIME(battle_timestamp, 'Asia/Seoul')) AS battle_datetime_not_same_battle_timestamp_kr
FROM basic.battle
```

```js
SELECT
  COUNT(DISTINCT id) AS battle_cnt
FROM basic.battle
WHERE
  EXTRACT(HOUR FROM battle_datetime) >= 6
  AND EXTRACT(HOUR FROM battle_datetime) <= 18
  EXTRACT(HOUR FROM battle_datetime) BETWEEN 6 and 18
```

**시간대 별로 몇 건이 있는가?**
```js
SELECT
  hour,
  COUNT(DISTINCT id) AS battle_cnt
FROM (
SELECT
  *,
  EXTRACT(HOUR FROM battle_datetime) AS hour
FROM basic.battle
)
GROUP BY
  hour
ORDER BY
  hour
```

### 3) 각 트레이너별로 그들이 포켓몬을 포획한 첫 날(catch_date)을 찾고, 그 날짜를 'DD/MM/YYYY' 형식으로 출력해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 날짜를 특정 형태로 변경! + 포획한 첫 날
- 쿼리 계산 방법: DATE => 문자열. FORMAT_DATETIME + MIN
- 데이터의 기간: X
- 사용할 테이블: trainer_pokemon
- Join KEY: X
- 데이터 특징: catch_date는 UTC 기준의 데이터. 한국 기준으로 하려면 catch_datetime을 사용해야 한다!

```js
SELECT
  trainer_id,
  FORMAT_DATE('%d/%m/%Y', min_catch_date) AS new_min_catch_date
FROM (
SELECT
  trainer_id,
  MIN(DATE(catch_datetime, 'Asia/Seoul')) AS min_catch_date
FROM basic.trainer_pokemon
GROUP BY
  trainer_id
)
ORDER BY
  trainer_id
=> ORDER BY 위치는 SELECT의 가장 바깥에서 실행
```

### 4) 배틀이 일어난 날짜(battle_date)를 기준으로, 요일별로 배틀이 얼마나 자주 일어났는지 계산해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 요일별로 배틀이 얼마나 자주 일어났는가? 배틀의 건!
- 쿼리 계산 방법: 요일별로 COUNT
- 데이터의 기간: X
- 사용할 테이블: battle
- Join KEY: X
- 데이터 특징: battle_date가 정상적임

```js
SELECT
  day_of_week,
  COUNT(DISTINCT id) AS battle_cnt
FROM (
SELECT
  *,
  EXTRACT(DAYOFWEEK FROM battle_date) AS day_of_week
FROM basic.battle
GROUP BY
  day_of_week
)
ORDER BY
  day_of_week
```

### 5) 트레이너가 포켓몬을 처음으로 포획한 날짜와 마지막으로 포획한 날짜의 간격이 큰 순으로 정렬하는 쿼리를 작성해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 처음과 마지막의 diff 큰 순으로 정렬!
- 쿼리 계산 방법: 처음 포획한 날짜(min) + 마지막으로 포획한 날짜(??) -> 차이를 구하고(DATETIME_DIFF) -> 차이가 큰 순으로 정렬(ORDER BY)
- 데이터의 기간: X
- 사용할 테이블: trainer_pokemon
- Join KEY: X
- 데이터 특징: catch_date는 UTC 기반으로 만들어진 일자. catch_datetime을 사용해야 한다

```js
SELECT
  *,
  DATETIME_DIFF(max_catch_datetime, min_catch_datetime, DAY) AS diff
FROM (
SELECT
  trainer_id,
  MIN(DATETIME(catch_datetime, 'Asia/Seoul')) AS min_catch_datetime,
  MAX(DATETIME(catch_datetime, 'Asia/Seoul')) AS max_catch_datetime,
FROM basic.trainer_pokemon
GROUP BY
  trainer_id
)
ORDER BY
  diff DESC
```


## 3. 조건문(CASE WHEN, IF)

### 조건문 함수
**조건문**
- 만약 특정 조건이 충족되면, 어떤 행동을 하자
- 특정 조건이 참일때 A, 아니면 B
- 조건에 따른 분기 처리가 필요한 경우
- 조건에 따라 다른 값을 표시하고 싶을 때 사용

**조건문을 사용하는 방법**
1) CASE WHEN
2) IF

### 조건문 함수가 사용되는 이유
- 데이터 분석을 하다보면, 특정 카테고리를 하나로 합치는 전처리가 필요할 수 있음
- 데이터 분석을 하다보면, 특정 카테고리를 하나로 합치는 전처리가 필요할 수 있음
- 이런 일이 발생하는 이유
    - 데이터를 저장하는 쪽과 분석하는 쪽이 나뉘고(팀 등으로),
    - 분석할 때 필요한 부분에서 조건 설정해서 변경하는 것이 더 유용
        - 저장할 때부터 특정 카테고리를 합쳐서 저장하면, 쪼개서 보고 싶을 때 볼 수 없음

### 조건문: 1) CASE WHEN
- 여러 조건이 있을 경우 유용
```js
문법
SELECT
  CASE
    WHEN 조건1 THEN 조건1이 참일 경우 결과
    WHEN 조건2 THEN 조건2가 참일 경우 결과
    ELSE 그 외 조건일 경우 결과
  END AS 새로운_컬럼_이름
```

**예시: Rock(바위) 타입과 Ground(땅) 타입이 결국 비슷한데 'Rock&Ground'라는 타입을 새로 만들면 어떨까?**
- 쿼리를 작성하는 목표, 확인할 지표: type이 Rock 또는 Ground면 => Rock&Ground라고 수정하겠다!
- 쿼리 계산 방법: CASE WHEN
- 데이터의 기간: X
- 사용할 테이블: pokemon
- Join KEY: X
- 데이터 특징: type이 type1, type2로 나뉘어서 두가지 타입을 모두 고려해야 한다!

```js
SELECT
  new_type1,
  COUNT(DISTINCT id) AS pokemon_cnt
FROM (
SELECT
  *,
  CASE
    WHEN (type1 IN ('Rock', 'Ground')) OR (type2 IN ('Rock', 'Ground')) THEN 'Rock&Ground'
    WHEN 조건2 THEN 조건2가 참일 경우 결과
    ELSE type1
  END AS new_type1
FROM basic_pokemon
)
GROUP BY
  new_type1
```

**순서**
- 각 포켓몬의 공격력(attack)을 기준으로, 50 이상이면 'Strong', 100 이상이면 'Very Strong', 그 이하면 'Weak'으로 분류해주세요.
- 조건1, 조건2에 둘 다 해당하면 앞선 순서를 따름
- 문자열 함수(특정 단어 추출)에서 이슈가 자주 발생 

```js
SELECT
  eng_name,
  attack,
  CASE
    WHEN attack >= 100 THEN 'Very Strong'
    WHEN attack >= 50 THEN 'Strong'
    ELSE 'Weak'
  END AS attack_level
FROM basic_pokemon
```

### 조건문: 2) IF
- 단일 조건일 경우 유용
```js
문법
IF(조건문, True일 때의 값, False일 때의 값) AS 새로운_컬럼_이름
```


## 4. 조건문 연습 문제

### 1) 포켓몬의 'Speed'가 70 이상이면 '빠름', 그렇지 않으면 '느림'으로 표시하는 새로운 컬럼 'Speed_Category'를 만들어 주세요.
- 쿼리를 작성하는 목표, 확인할 지표: Speed 컬럼을 사용해 새로운 Speed_Category 만들어야 함
- 쿼리 계산 방법: CASE WHEN, IF => 조건이 단일이다. IF. 70 이상
- 데이터의 기간: X
- 사용할 테이블: pokemon
- Join KEY: X
- 데이터 특징: MIN speed : 5. Max speed : 140

```js
SELECT
  id,
  kor_name,
  speed,
  IF(speed >= 70, '빠름', '느림') AS Speed_Category
FROM basic.pokemon
```

### 2) 포켓몬의 'type1'에 따라 'Water', 'Fire', 'Electric' 타입은 각각 '물', '불', '전기'로, 그 외 타입은 '기타'로 분류하는 새로운 컬럼 'type_Korean'을 만들어 주세요.
- 쿼리를 작성하는 목표, 확인할 지표: type1을 사용해서 특정 조건을 만족하는 것은 값을 변경, 기타 => type_Korean
- 쿼리 계산 방법: CASE WHEN, IF => 여러 조건이 있음. Water => 물, Fire => 불, Electric => 전기. CASE WHEN
- 데이터의 기간: X
- 사용할 테이블: pokemon
- Join KEY: X
- 데이터 특징: 타입이 여러가지가 있다.

```js
SELECT
  id,
  kor_name,
  type1,
  CASE
    WHEN type1 = 'Water' THEN '물'
    WHEN type1 = 'Fire' THEN '불'
    WHEN type1 = 'Electric' THEN '전기'
    ELSE '기타'
  END AS type1_Korean
FROM basic.pokemon
```

### 3) 각 포켓몬의 총점(total)을 기준으로, 300 이하면 'Low', 301에서 500 사이면 'Medium', 501 이상이면 'High'로 분류해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: total 컬럼 => 조건에 맞는 값을 변경!
- 쿼리 계산 방법: CASE WHEN
- 데이터의 기간: X
- 사용할 테이블: pokemon
- Join KEY: X
- 데이터 특징: total 컬럼이 정수(INTEGER)

```js
SELECT
  id,
  kor_name,
  total,
  CASE
    WHEN total >= 501 THEN 'High'
    WHEN total BETWEEN 300 AND 500 THEN 'Medium'
    ELSE 'Low'
  END AS total_grade
FROM basic.pokemon
```

### 4) 각 트레이너의 배지 개수(badge_count)를 기준으로, 5개 이하면 'Beginner', 6개에서 8개 사이면 'Intermediate', 그 이상이면 'Advanced'로 분류해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: badge_count => 조건에 만족하는 값을 변경
- 쿼리 계산 방법: CASE WHEN
- 데이터의 기간: X
- 사용할 테이블: trainer
- Join KEY: X
- 데이터 특징: X

```js
SELECT
  id,
  name,
  badge_count,
  CASE
    WHEN badge_count >= 9 THEN 'Advanced'
    WHEN badge_count BETWEEN 6 AND 8 THEN 'Intermediate'
    ELSE 'Beginner'
  END AS trainer_level
FROM basic.trainer
```

### 5) 트레이너가 포켓몬을 포획한 날짜(catch_date)가 '2023-01-01' 이후이면 'Recent', 그렇지 않으면 'Old'로 분류해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 포획한 날짜 기준으로 값을 변경!
- 쿼리 계산 방법: IF
- 데이터의 기간: X
- 사용할 테이블: trainer_pokemon
- Join KEY: X
- 데이터 특징: catch_date는 UTC 기준, catch_datetime은 TIMESTAMP

```js
SELECT
  id,
  trainer_id,
  pokemon_id,
  catch_datetime,
  IF(DATE(catch_datetime, 'Asia/Seoul') >= '2023-01-01', 'Recent', 'Old') AS recent_or_old
FROM basic.trainer_pokemon
```

### 6) 배틀에서 승자(winner_id)가 player1_id와 같으면 'Player 1 Wins', player2_id와 같으면 'Player 2 Wins', 그렇지 않으면 'Draw'로 결과가 나오게 해주세요.
- 쿼리를 작성하는 목표, 확인할 지표: 승패 여부를 알 수 있는 컬럼을 만들고 싶다!
- 쿼리 계산 방법: CASE WHEN
- 데이터의 기간: X
- 사용할 테이블: battle
- Join KEY: X
- 데이터 특징: X

```js
SELECT
  id,
  winner_id,
  player1_id,
  player2_id,
  CASE
    WHEN winner_id = player1_id THEN 'Player 1 Wins'
    WHEN winner_id = player2_id THEN 'Player 2 Wins'
  ELSE 'Draw'
  END AS battle_result
FROM basic.battle
```

## 5. 정리

### 컬럼 변환하기 정리
![스크린샷](../image/screenshot51.png)
- **숫자:** 사칙연산/SAFE_DIVIDE
- **문자:** CONCAT/SPLIT/REPLACE/TRIM/UPPER
- **시간, 날짜:** EXTRACT(HOUR FROM datetime)/DATETIME_TRUNC/PARSE_DATETIME
- **부울(Bool):** 데이터 타입 변경하기/조건문: 특정 조건이면 변경하기/CASE WHEN/IF

## 5. BigQuery 공식 문서 확인하는 법

### 개발 공식 문서
- 프로그래밍 언어, 라이브러리 등은 해당 기술을 어떻게 사용하면 좋은지에 대해 문서를 제공함
- 완성도는 모두 다르지만, 많이 참고하는 참고서 느낌(수학의 정석)
- 처음엔 어려워 보일 수 있지만, 익숙해지면 빠르게 파악할 수 있음
- BigQuery에 해당되는 것은 아니고 대부분의 프로그래밍 관련 내용에 적용할 수 있음

**찾는 방법**
- '기술명 + documentation'으로 검색

### 구글에서 BigQuery Documentation으로 검색
https://cloud.google.com/bigquery/docs?hl=ko

### 공식 문서를 꼭 봐야 하나?
- 공식 문서'도' 볼 수 있어야 함
- ChatGPT, 블로그에도 참고할 수 있는 자료가 있을 수 있지만, 잘못된 경우나 과거 문법인 경우가 존재
- 구글에서 관리하는 문서이므로 기준점으로 삼아보기
- 다만 공식 문서로 시작하면 방대한 양에 압도될 수 있으므로, 필요할 때마다(목적에 맞게) 공식 문서를 보는 방식으로 학습 추천

### 공식 문서를 꼭 봐야 하나?
- BigQuery xxx 라고 검색
- xxx : 특정 행위

### 공식 문서 Slack RSS Feed 추가하기
- BigQuery가 조용히 새로운 기능을 출시하기도 함
- 이걸 빠르게 알 수 있는 방법은? => RSS Feed 구독
- **RSS Feed(Really Simple Syndication)** - 새 기사들의 제목 또는 새 기사들 전체를 뽑아서 하나의 파일로 만들어 놓은 것
- **슬랙에서 알람 받아보기**
    - 개인 목적으로 사용할 슬랙 워크스페이스 생성
    - 채널에 아래 명령어 입력<br>
      /feed subscribe https://cloud.google.com/feeds/bigquery-release-notes.xml


## 과제 인증샷
![스크린샷](../image/screenshot52.png)