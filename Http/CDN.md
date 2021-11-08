## CDN  

CDN은 Content Delivery Network의 줄임말로 컨텐츠 전송 네트워크를 뜻한다.  

지리적 제약 없이 전 세계 사용자에게 콘텐츠를 빠르고 안전하게 전송할 수 있는 콘텐츠 전송 기술을 말한다.  

<img src="/Http/image/cdn.png" width="600" height="300">  

```text
CDN(Content Delivery Networks) : 정적 컨텐츠를 전송하기 위해 구축된 네트워크
POP(Points Of Presence) 형태로 지역 곳곳에 캐시 서버로 배치되어 있다.

Origin Server: 메인 서버
Cache Server(Edge Server): CDN 서버
```

CDN을 사용함으로써 서버와 사용자 사이의 물리적인 거리를 줄여 콘텐츠가 로딩되는 시간을 최소화한다.  

## CDN 원리  

사용자가 최초 요청시 Origin Server로 부터 콘텐츠를 가져와 사용자에게 제공함과 동시에 CDN Cache Server에 저장한다. (CDN 캐싱 방식에 따라 달라 질 수 있다.)  

이후 사용자의 요청은 사용자의 위치에서 가장 가까운 CDN Cache Server로 접속시키게 되며 콘텐츠 만료시점까지 저장된 콘텐츠를 사용자에게 제공한다.  

Origin Server가 Cache Server에서 요청된 파일을 찾는데 실패했거나 너무 오래되어 만료기간이 지났을 경우  

콘텐츠가 삭제 되어있을 것이므로 Origin Server에서 파일을 조회하여 사용자에게 전달하고 CDN은 새로운 콘텐츠를 저장한다.  

## CDN 캐싱 방식  

#### 1. Static Caching  

- Origin Server에 있는 Content를 운영자가 미리 Cache Server에 복사한다.
- 미리 복사해뒀기 때문에 사용자가 Cache Server에 콘텐츠 요청시 무조건 Cache Server에 있다.
- 사용자는 보존된 콘텐츠를 CDN을 통해 효율적으로 전달 받을 수 있다.

#### 2. Dynamic Caching  

- 사용자가 Content 요청시 해당 Content가 없다면 Origin Server로 부터 다운로드 받아 전달한다.
- Origin Server로 부터 가져온 콘텐츠는 Cache Server가 관리한다.
- 이후 사용자에게 동일한 요청이 온다면 Cache Server에서 콘텐츠를 제공한다.
- 콘텐츠는 일정 시간이후 Cache Server에서 삭제 될 수도 있다.

---

#### Reference  

- <https://ko.wikipedia.org/wiki/콘텐츠_전송_네트워크>
- <https://libertegrace.tistory.com/entry/Network-CDNContents-Delivery-Network-이해하기>
- <https://velog.io/@chun_gil/CDN-Content-Delivery-Networks-개념-정리>
- <https://goddaehee.tistory.com/173>
- <https://library.gabia.com/contents/infrahosting/8985/>