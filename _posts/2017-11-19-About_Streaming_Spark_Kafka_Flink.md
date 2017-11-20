---
layout: post
title: Spark, Flink, Kafka Streaming ...
---

## Streaming 101
ㅁ Latency & Throughput
{{ 사진 }}



ㅁ SQL Streaming
 - Spark's Structured Streaming
 - Flink's Data Stream SQL
 - Kafka's kSQL
 
 
ㅁ 그 이외에 Streaming 에서 중요한 개념들!
 - Exactly Once, At most once, At least once
 - Time Windowed
 - How to manage State! ( in Stateful Streaming )
 - How to manage log
 - How to Fail-over, Alert, Restart
 - How to Scale out
 - How to Monitoring Metric
 결국 어려운건 운영... Streaming 시스템에서 Latency & Throughput 도 
 매우 중요한 요소이지만 "어떻게 운영할 것인가? 운영포인트를 줄여갈 것인가?"도
 매우매우 중요한 요소입니다. 이게 없으면 Streaming F/W 이라 할 수 없죠.
 
 
ㅁ 위에서 한 얘기들 실제 Streaming 시스템에서 어떻게 처리하는지가 궁금하시다면! :)
참조 : http://slides.com/yonghweekim/streaming-system#/
오늘 얘기에서는 위에 내용들을 어떻게 처리하고 관리하는지를 보기 위함은 아니라 패스 하겠습니다!
나중에 기회가 되면 Spark Streaming 운영과 회고 발표 슬라이드도 글로 옮겨야 겠네요 ^^;



## Streaming Service Trend
{{ 사진 }}



## Kafka streaming
{{ 카프카 github 사진 }}
Kafka 0.9 부터 Kafka Streaming Client 를 지원합니다.
현재는 1.0 버전을 드디어! 런칭하면서 그 발전속도가 세상을 깜짝 놀라게 합니다.

Streaming 이 나온지 얼마 되지 않아 ksql 이라는 어마 무시한 kafka sql streaming 오픈소스가 나옵니다.
(링크 : https://github.com/confluentinc/ksql )
최근에는 LINE Corperation 에서 상용서비스에 Kafka Streams 를 적용했고, 덕분에 Kafka 개발자들은 
신이납니다. ( 보통 상용에 대한 검증을 큰 회사에서 한번 해주면 믿고 가면 되거든요 ㅋㅋ )
(링크 : https://engineering.linecorp.com/ko/blog/detail/80 )





