---
title: '[JS] n개의 빈배열 리스트 만드는 법'
date: 2022-08-03 23:48:00
category: 'T(W)IL'
draft: false
---

## 문제 발견

![swipping 컨텐츠](./images/swippingUi.png)

swipping 컨텐츠에 첫번째 이미지가 노출되지 않는 문제가 있어 이를 해결하기 위해 디버깅을 하고 있었다. 원인은 이미지 리스트의 index number와 하단 페이지 인디케이터를 만들 때 index number가 제대로 매핑이 안되고 있기 때문이었다.

```jsx
const BANNER_IMAGES = [
  'banner_image_1.png',
  'banner_image_2.png'
  'banner_image_3.png'
  'banner_image_4.png'
];

const indicatorList = [1,2,3,4]; // 0부터가 아니라 1부터 배열을 만들었음

indicatorList.map(item => {
  const isCurrentPage = item === currentStep;
  return (
    <button
      key={item}
      aria-current={isCurrentPage}
      onClick={() => setCurrentStep(item)}
    />
  );
})
```

기존 작업자의 관점으로 왜 이런 실수를 했을까 생각해보면, 총 배너 이미지 개수는 4개, currentStep은 0~3으로 관리되고 있었다. 그런데 BANNER_IMAGES의 파일명 인덱싱이 1~4로 되어 있어 indicator의 인덱싱도 이에 맞춰서 1~4로 맞춘걸로 추정된다. 해결책은 `indicatorList=[0,1,2,3]` 으로 변경해주면 index가 맞게 되어 잘 동작한다.

이때 배열을 좀더 나이스하게 만들고 싶었다.

## Array.prototype.keys()
`indicatorList=[0,1,2,3]` 로 만들어줘도 상관이 없다. 하지만 n개의 배열 요소를 만들고 싶다면 어떻게 해야될까? 이를 위해 찾던 중 `Array.prototype.keys` 를 활용한 방법을 알게 되었다.

```javascript
// AS-IS
// const indicatorList = [1,2,3,4]

// TO-BE
const totalCount = BANNER_IMAGES.length;  // 4
const indicatorList = [...Array(totalCount).keys()]; // [0,1,2,3]
```

1. Array 함수로 n개의 요소를 만들고, 
2. Array.prototype.keys() 를 이용하여 [iterator 객체](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/keys)를 만들다.
3. 이를 배열 안에서 spread 연산자로 풀어준다.

## **정리**

- 두 컴포넌트(banner 이미지 리스트, 페이지네이션)에서 공통된 index state를 바라볼 경우, 꼼꼼하게 살펴보자.
- `Array(n)`
- `Array.prototype.keys()`

