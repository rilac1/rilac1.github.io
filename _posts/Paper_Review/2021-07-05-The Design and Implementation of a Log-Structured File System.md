---
title: The Design and Implementation of a Log-Structured File System
date: 2021-07-05 12:00:00 +/-TTTT
categories: [Paper Review, LFS]
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