---
title: '[JS] 객체에 조건부 프로퍼티 적용하는 방법'
date: 2020-06-05 22:00:00
category: 'T(W)IL'
draft: false
---

## 객체에 조건부 프로퍼티 적용하는 방법
작업 중 query 객체의 특정 프로퍼티를 조건부로 적용해야하는 경우가 생겼다. 예를 들어 page라는 param을 주는 상황에서 특정 값이 있을 때 param을 주고 싶다.

```javascript
const query = {};
 
if (page) {
    query.page = page;
}
```

위 코드처럼 if문으로 query 객체에 prop을 추가해줄 수 있다. 하지만 조금 더 간단하게 짤 방법을 알아봄

```javascript
const query = {
	...(page && { page })
};
 
 
// 여러 params가 있는 경우
const query = {
	...(page && { page }),
	...(category && { category }),
	...(type && { type })
};
```

`spread 연산자`를 사용하면 조금 더 간단하게 사용할 수 있다. 또한 다른 param이 있다면 if문을 사용하는 것보다 간단하고 가독성도 나쁘지 않을 듯 하다. 이 개념을 활용해서 실무 코드에서는 아래처럼 작성했다.

### 실제 사용한 코드

```javascript
const router = useRouter();
const { pathname, query } = router;
const page = pageValue;
const nextQuery = {
	...query,
    page: pageValue
}
 
router.replace(
	{
		pathname,
		query: nextQuery,
	},
);
```

## 그렇다면 성능은?

`spread 연산자`를 사용한 방식은 사실 `syntax sugar`이다. 그렇기 때문에 성능상으로는 첫번째 if문 방식이 더 좋았다.

```javascript
function testSpreadOperatorConditionFulfilled() {
  const value = 5;
 
  console.time('testSpreadOperatorConditionFulfilled');
  for (let i = 0; i < 200000000; i++) {
    let a = {
      ...(value && {b: value})
    };
  }
  console.timeEnd('testSpreadOperatorConditionFulfilled');
}
 
function testSpreadOperatorConditionNotFulfilled() {
  const value = undefined;
 
  console.time('testSpreadOperatorConditionNotFulfilled');
  for (let i = 0; i < 200000000; i++) {
    let a = {
      ...(value && {b: value})
    };
  }
  console.timeEnd('testSpreadOperatorConditionNotFulfilled');
}
 
function testClassicConditionFulfilled() {
  const value = 5;
 
  console.time('testClassicConditionFulfilled');
  for (let i = 0; i < 200000000; i++) {
    let a = {};
    if (value)
      a.b = value;
  }
  console.timeEnd('testClassicConditionFulfilled');
}
 
function testClassicConditionNotFulfilled() {
  const value = undefined;
 
  console.time('testClassicConditionNotFulfilled');
  for (let i = 0; i < 200000000; i++) {
    let a = {};
    if (value)
      a.b = value;
  }
  console.timeEnd('testClassicConditionNotFulfilled');
}
 
testClassicConditionFulfilled(); // ~ 234.9ms
testClassicConditionNotFulfilled(); // ~493.1ms
testSpreadOperatorConditionFulfilled(); // ~2649.4ms
testSpreadOperatorConditionNotFulfilled(); // ~2278.0ms
```

## 결론
개인적 의견은 하드웨어가 점점 좋아지면서 성능 이슈가 점점 줄어들고 있다. 물론 성능을 고려하지 않아도 된다는 것이 아니다. 결국엔 트레이드 오프 관점에서 아주 미세한 ms를 개선하는 것보다는 코드 가독성을 늘려 이해하기 쉽고 간편한 코드를 짜는게 더 좋을 것 같아. 성능이 크리티컬한 프로젝트가 아니라면 spread 연산자를 사용한 방법이 더 좋다고 생각한다.
