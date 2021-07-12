---
title: "The Design and Implementation of a Log-Structured File System"
date: 2021-07-05 12:00:00 +/-TTTT
categories: [Paper Review]
tags: [LFS]     # TAG names should always be lowercase
---

MENDEL ROSENBLUM and JOHN K. OUSTERHOUT  
University of California at Berkeley  

---
본 논문에서는 `log-structured file system(LFS)` 이라는 새로운 스토리지 관리 기법을 제안한다. LFS에서는 스토리지의 모든 변경사항을 sequantial한 log 형태로 기록하여  writing 성능과 crash recovery를 만족할 수 있다. LFS에서 데이터를 update하기 위해서는 이미 쓰여진 log를 수정되지 않고 뒤쪽에 새로운 log를 write한다. 따라서 시간이 지남에 따라 invalid한 log들이 쌓이게 되는데 이를 해결하기 위해 스토리지를 segment 단위로 나누고 claning 정책에 따라 segment를 비운다.

---

## 문제 정의
- 기존의 파일시스템(FAST)은 write 성능이 좋지 못하다.
- 메인 메모리의 캐시 용량이 늘어나게 되면 디스크에서의 읽기 성능이 덜 중요해지므로 디스크 트래픽은 거의 write에 의해 결정된다.
- Random / Sequantial에 따라 I/O 성능이 달라지는데 Random에 경우 seeking time으로 인해 성능이 매우 나빠진다.

## 제안 방법
LFS는 write를 sequantial하게 함으로서 write의 대역폭을 증가시킨다.  
write를 할 때 우선 메모리에 log 형태로 계속 작성하다가 메모리가 전부 차게 되면 그대로 디스크에 다시 쓴다.  
LFS가 sequantial하게 inode를 계속해서 갱신하기 때문에 inode가 여러곳에 흩어지게 된다. 따라서 흩어진 inode를 찾고 관리하기 위해서 **inode map**을 사용한다.

## Segment Cleaning Policy
LFS는 **Out-of-place Update**(값을 직접 수정하지 않고 뒤에 새롭게 씀)를 사용하기 때문에 시간이 지날수록 invalid한 블록들이 쌓이게 된다. 따라서 이러한 블록들을 지워 다시 가용한 블록으로 만들어주는 과정이 필요하다. 데이터가 지워질 때에는 세그먼트 단위로 이루어 지는데 여기서 중요하게 다뤄야 할 몇 가지 issue를 소개하겠다.

1. 언제 일어나는가?  
: 한가할 때 or 디스크 가용공간이 부족할 때
2. 얼마나 많은 세그먼트가 한 번에 지워져야 하는가?  
: 당연하게도 많이 지울수록 디스크는 깔끔해진다.
3. 어떤 세그먼트를 지워야 하는가?  
: 단편화가 많이 일어난 세그먼트를 지우면 좋을것 같지만 꼭 그렇지만은 않다.  
4. 세그먼트를 비울 때 valid한 블록들은 옮겨 적어야 하는데 이들을 어떻게 묶을 것인가?  
: 읽기를 빠르게 할 수 있게 모아둔다(locality) or 비슷한 시기에 작성된 것끼리 모아둔다(age sort)

이러한 issue들을 고려보았을 때 가장 적절한 정책은 무엇일까?  
본 논문에서 제시하는 정책을 소개하겠다.  

- 세그먼트를 비우기 위해서는 기존에 존재하던 valid한 블록들을 뒤에 다시 write해야 하는 오버헤드`cost`가 존재한다. 
- invalid한 블록이 많으면 세그먼트를 비웠을 때 새로 할당되는 가용블럭 개수`benefit`이 늘어날 것이다.  
  
이를 종합해 보면 valid한 블록이 적고 invalid한 블록이 많을수록 해당 세그먼트를 비웠을 때 이점이 많다.  
하지만 여기에는 함정이 있다.

우선 세그먼트를 Hot / Cold로 나누어서 생각해보자. Hot한 세그먼트는 데이터가 자주 update되는 세그먼트고 cold는 수정이 잘 이루어지지 않는 세그먼트이다. 결국 hot한 세그먼트에는 invalid한 블록이 많이 쌓여있을 확률이 높다. 그렇다면 앞선 설명과 같이 hot한 세그먼트를 우선적으로 비우는 것이 유리해보인다.  

하지만 hot한 세그먼트는 비워줘봤자 금방 더러워진다(invalid한 블록이 금방 쌓임).  
반면에 cold한 세그먼트는 한 번 비워주면 깔끔한 상태로 오래동안 유지할 가능성이 높다.  
그렇기에 무작정 hot한 세그먼트만 우선적으로 비워주는 것은 성능향상에 도움이 되지 않는다.  
따라서 본 논문에서는 다음과 같은 공식으로 cleaning을 진행한다.  

``(benefit * age) / cost``
> `benefit`: cleaning을 수행했을 때 얻을 수 있는 가용 block의 비율  
> `cost`: cleaning을 수행하기 위해 필요한 write 오버헤드  
> `age`: 해당 세그먼트가 쓰여진 기간

age가 높다는 것은 해당 세그먼트가 지워지지 않고 오래동안 존재했다는 뜻이다.  
`benefit/cost` 만으로 cleaning 정책을 실시하면 cold 세그먼트는 오래동안 남아있을 확률이 높다(age가 높다).  
따라서 benefit에 해당하는 부분에 age를 곱하여 cold 세그먼트도 적절하게 비워질 수 있도록 조절해주는 것이다.

