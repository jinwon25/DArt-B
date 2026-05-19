# SQL Assignment 3주차


## 1. 연습 문제

### 데이터 탐색 - 조건, 추출, 요약 연습 문제

#### 1. 포켓몬 중에 type2가 없는 포켓몬의 수를 작성하는 쿼리를 작성해주세요.
- 조건: type2가 없는
- 어떤 테이블?: pokemon
- 어떤 컬럼: 따로 없음. 포켓몬의 수만 남기면 됨
- 어떻게 집계: 포켓몬의 수 => COUNT
![스크린샷](../image/screenshot15.png)
```js
SELECT
  COUNT (id) AS cnt
FROM basic.pokemon
WHERE
  type2 IS NULL
```

#### 2. type2가 없는 포켓몬의 type1과 type1의 포켓몬 수를 알려주는 쿼리를 작성해주세요. 단, type1의 포켓몬 수가 큰 순으로 정렬해주세요.
- 테이블: pokemon
- 조건: type2가 없는 포켓몬
- 컬럼: type1
- 집계: 포켓몬 수 => COUNT
- 정렬: type1의 포켓몬 수가 큰 순으로 정렬 => ORDER BY 큰 순으로 : 큰 것부터 작은 것으로 => 내림차순(DESC) => ORDER BY 포켓몬 수 DESC
![스크린샷](../image/screenshot16.png)
```js
SELECT
  type1,
  COUNT(id) AS pokemon_cnt
FROM basic.pokemon
WHERE
  type2 IS NULL
GROUP BY
  type1
ORDER BY
  pokemon_cnt DESC
```

#### 3. type2 상관없이 type1의 포켓몬 수를 알 수 있는 쿼리를 작성해주세요.
- 테이블: pokemon
- 조건: type2 상관없이 => 조건인가? 아닌가? => 조건이 아님
- 컬럼: type1
- 집계: 포켓몬 수 => COUNT
![스크린샷](../image/screenshot17.png)
```js
SELECT
  type1,
  COUNT(id) AS pokemon_cnt
FROM basic.pokemon
GROUP BY
  type1
```

#### 4. 전설 여부에 따른 포켓몬 수를 알 수 있는 쿼리를 작성해주세요.
- 테이블: pokemon
- 조건: 없음
- 컬럼: 전설(is_legendary)
- 집계: 포켓몬 수
![스크린샷](../image/screenshot18.png)
```js
SELECT
  is_legendary,
  COUNT(id) AS pokemon_cnt
FROM basic.pokemon
GROUP BY
  is_legendary
```
> - GROUP BY 1 => SELECT의 첫 컬럼의 의미
> - ORDERY BY에도 1, 2 등을 사용할 수 있음
> - 1, 2 => 쿼리를 빠르게 작성하고, 결과를 보는 과정. 완성된 쿼리문에서는 1, 2 같은 표현보다는 명확하게 컬럼을 명시하는게 좋음(가독성 관점)

#### 5. 동명이인이 있는 이름은 무엇일까요?
- 테이블: trainer
- 조건: 같은 이름이 2개 이상(동명이인) => COUNT(name) => 2개 이상
- 컬럼: 이름
- 집계: COUNT
![스크린샷](../image/screenshot19.png)
```js
SELECT
  name,
  COUNT(name) AS trainer_cnt
FROM basic.trainer
GROUP BY
  name
HAVING
  trainer_cnt >= 2
```
> - WHERE: 원본 데이터 FROM절에 있는 데이터에 조건을 설정하고 싶은 경우
> - HAVING: GROUP BY와 함께 집계 결과에 조건을 설정하고 싶은 경우
> - 서브쿼리: 쿼리문을 한 번 감싸서 다른 쿼리문에서 사용할 수 있음

#### 6. trainer 테이블에서 "Iris" 트레이너의 정보를 알 수 있는 쿼리를 작성해주세요.
- 테이블: trainer
- 조건: 트레이너의 이름 = "Iris"
- 컬럼: 정보 => 모든 컬
- 집계: X
![스크린샷](../image/screenshot20.png)
```js
SELECT
  *
FROM basic.trainer
WHERE
  name = "Iris"
```

#### 7. trainer 테이블에서 "Iris", "Whitney", "Cynthia" 트레이너의 정보를 알 수 있는 쿼리를 작성해주세요.
- 테이블: trainer
- 조건: 이름 = "Iris", "Whitney", "Cynthia" 중에 있으면 추출
- 컬럼: 정보 -> *
- 집계: 없음
![스크린샷](../image/screenshot21.png)
```js
SELECT
  *
FROM basic.trainer
WHERE
  (name = "Iris")
  OR (name = "Cynthia")
  OR (name = "Whitney")
  # OR 조건으로 쓰는 거 너무 귀찮다. => IN => name의 괄호 안에 Value가 있는 Row만 추출
  name IN ("Iris", "Cynthia", "Whitney")
```

#### 8. 전체 포켓몬 수는 얼마나 되나요?
- 테이블: pokemon
- 조건: 없음
- 컬럼: 없음
- 집계: 포켓몬 수 => COUNT(id)
![스크린샷](../image/screenshot22.png)
```js
SELECT
  COUNT(id) AS pokemon_cnt 
FROM basic.pokemon
```
  
#### 9. 세대별로 포켓몬 수가 얼마나 되는지 알 수 있는 쿼리를 작성해주세요.
- 테이블: pokemon
- 조건: 없음
- 컬럼: 세대(generation)
- 집계: 포켓몬 수 => COUNT
![스크린샷](../image/screenshot23.png)
```js
SELECT
  generation,
  COUNT(id) AS pokemon_cnt 
FROM basic.pokemon
GROUP BY
  generation
```

#### 10. type2가 존재하는 포켓몬의 수는 얼마나 되나요?
- 테이블: pokemon
- 조건: type2가 존재하는! => type2 IS NOT NULL
- 컬럼: X
- 집계: 포켓몬 수 => COUNT
![스크린샷](../image/screenshot24.png)
```js
SELECT
  COUNT(id) AS pokemon_cnt
FROM basic.pokemon
WHERE
  type2 IS NOT NULL
```

#### 11. type2가 있는 포켓몬 중에 제일 많은 type1은 무엇인가요?
- 테이블: pokemon
- 조건: type2가 있는
- 컬럼: type1
- 집계: 제일 많은 => COUNT
![스크린샷](../image/screenshot25.png)
```js
SELECT
  type1,
  COUNT(id) AS pokemon_cnt
FROM basic.pokemon
WHERE
  type2 IS NOT NULL
GROUP BY
  type1
ORDER BY
  pokemon_cnt DESC
LIMIT 1
```

#### 12. 단일(하나의 타입만 있는) 타입 포켓몬 중 많은 type1은 무엇일까요?
- 테이블: pokemon
- 조건: 단일 타입 => 하나의 타입만 존재 => type2가 NULL(값이 없어야 한다)
- 컬럼: type1
- 집계: COUNT
![스크린샷](../image/screenshot26.png)
```js
SELECT
  type1,
  COUNT(id) AS pokemon_cnt
FROM basic.pokemon
WHERE
  type2 IS NULL
GROUP BY
  type1
ORDER BY
  pokemon_cnt DESC
LIMIT 1
```

#### 13. 포켓몬의 이름에 "파"가 들어가는 포켓몬은 어떤 포켓몬이 있을까요?
- 테이블: pokemon
- 조건: name에 "파"가 들어가는 포켓
- 컬럼: 어떤 포켓몬이 있을까요? name
- 집계: 없음
![스크린샷](../image/screenshot27.png)
```js
SELECT
  kor_name
FROM basic.pokemon
WHERE
  kor_name LIKE "파%"
```
> - 칼럼 LIKE "특정단어%". %는 앞에도 붙을 수 있고, 뒤에도 붙을 수 있음.

#### 14. 뱃지가 6개 이상인 트레이너는 몇 명이 있나요?
- 테이블: trainer
- 조건: 뱃지가 6개 이상(badge_count >= 6)
- 컬럼: 없음
- 집계: 트레이너의 수(COUNT)
![스크린샷](../image/screenshot28.png)
```js
SELECT
  COUNT(id) AS trainer_cnt
FROM basic.trainer
WHERE
  badge_count >= 6
```

#### 15. 트레이너가 보유한 포켓몬이 제일 많은 트레이너는 누구일까요?
- 테이블: trainer_pokemon
- 조건: 없음
- 컬럼: trainer_id
- 집계: 포켓몬의 수 => COUNT
![스크린샷](../image/screenshot29.png)
```js
SELECT
  trainer_id,
  COUNT(pokemon_id) AS pokemon_cnt
FROM basic.trainer_pokemon
GROUP BY
  trainer_id
ORDER BY
  pokemon_cnt DESC
LIMIT 1
```

#### 16. 포켓몬을 많이 풀어준 트레이너는 누구일까요?
- 테이블: trainer_pokemon
- 조건: status = "Released" (풀어준)
- 컬럼: trainer_id
- 집계: 많이 풀어준 => COUNT
![스크린샷](../image/screenshot30.png)
```js
SELECT
  trainer_id,
  COUNT(pokemon_id) AS pokemon_cnt
FROM basic.trainer_pokemon
WHERE
  status = "Released"
GROUP BY
  trainer_id
ORDER BY
  pokemon_cnt DESC
LIMIT 1
```

#### 17. 트레이너 별로 풀어준 포켓몬의 비율이 20%가 넘는 포켓몬 트레이너는 누구일까요? 풀어준 포켓몬의 비율 = (풀어준 포켓몬 수/전체 포켓몬 수)
- 테이블: trainer_pokemon
- 조건: 풀어준 포켓몬의 비율이 20%가 넘어야 한다
- 컬럼: trainer_id
- 집계: COUNTIF(조건): COUNTIF(컬럼 = "3")
![스크린샷](../image/screenshot31.png)
```js
SELECT
  trainer_id,
  COUNTIF(status = "Released") AS released_cnt,
  COUNT(pokemon_id) AS pokemon_cnt,
  COUNTIF(status = "Released") / COUNT(pokemon_id) AS released_ratio
FROM basic.trainer_pokemon
GROUP BY
  trainer_id
HAVING
  released_ratio >= 0.2
```


## 2. 정리

### 데이터 탐색 - 조건, 추출, 요약 정리
![스크린샷](../image/screenshot32.png)


## 3. 새로운 집계 함수 소개(GROUP BY ALL, 2024-02-06에 나온 함수)

**GROUP BY ALL:** 모든 그룹화 가능한 컬럼을 자동으로 추론하여 그룹화함
![스크린샷](../image/screenshot33.png)


## 4. SQL 쿼리 작성하는 흐름

![스크린샷](../image/screenshot34.png)


## 5. 쿼리 작성 템플릿과 생산성 도구

### 쿼리 작성 템플릿
- 쿼리를 작성하는 목표, 확인할 지표:
- 쿼리 계산 방법:
- 데이터의 기간:
- 사용할 테이블:
- Join KEY:
- 데이터 특징:

```js
SELECT

FROM
WHERE
```
이렇게 글로 작성하면 쿼리 작성이 더 수월

### 생산성 도구: 템플릿 쉽게 사용하기
템플릿을 사용하자!라고 제시하면 생기는 일

템플릿을 사용하는 것을 까먹음 => 습관 형성이 되지 않음<br>
이 부분을 개선하기 위해 **생산성 도구**를 활용

### 생산성 도구: Espanso
https://espanso.org/

- 윈도우 10, MAC, Linux 모두 사용 가능한 프로그램(무료)
- 특정 단어를 입력하면 원하는 문장(템플릿)으로 변경

### 생산성 도구: Espanso 핵심 로직
특정 단어가 감지되면 정의된 것으로 바꾼다!
> Match<br>
> trigger ":date"<br>
> replace "October 11, 2021"

### 생산성 도구: Espanso 설정 수정하기(base.yml)
![스크린샷](../image/screenshot35.png)

### 생산성 도구: Espanso 확인 BigQuery Console에서 확인
![스크린샷](../image/screenshot36.png)


## 과제 인증샷
![스크린샷](../image/screenshot37.png)
