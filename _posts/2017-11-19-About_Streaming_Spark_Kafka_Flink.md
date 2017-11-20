---
layout: post
title: Spark, Flink, Kafka Streaming ...
---
![_config.yml]({{ site.baseurl }}/images/config.png)
## Streaming 101
ㅁ Latency & Throughput
{{ 사진 }}



#### ㅁ SQL Streaming
 - Spark's Structured Streaming
 - Flink's Data Stream SQL
 - Kafka's kSQL
 
 
#### ㅁ 그 이외에 Streaming 에서 중요한 개념들!
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
 
 
#### ㅁ 위에서 한 얘기들 실제 Streaming 시스템에서 어떻게 처리하는지가 궁금하시다면! :)
참조 : http://slides.com/yonghweekim/streaming-system# <br/>
오늘 얘기에서는 위에 내용들을 어떻게 처리하고 관리하는지를 보기 위함은 아니라 패스 하겠습니다!<br/>
나중에 기회가 되면 Spark Streaming 운영과 회고 발표 슬라이드도 글로 옮겨야 겠네요 ^^;<br/>


<br/>
## Streaming Service
{{ 사진 }} <br/>


<br/>
## Kafka streaming
{{ 카프카 github 사진 }} <br/>
Kafka 0.9 부터 Kafka Streaming Client 를 지원합니다.<br/>
현재는 1.0 버전을 드디어! 런칭하면서 그 발전속도가 세상을 깜짝 놀라게 합니다.

Streaming 이 나온지 얼마 되지 않아 ksql 이라는 어마 무시한 kafka sql streaming 오픈소스가 나옵니다. <br/>
(링크 : https://github.com/confluentinc/ksql ) <br/>
최근에는 LINE Corperation 에서 상용서비스에 Kafka Streams 를 적용했고, 덕분에 Kafka 개발자들은  <br/>
신이납니다. ( 보통 상용에 대한 검증을 큰 회사에서 한번 해주면 믿고 가면 되거든요 ㅋㅋ ) <br/>
(링크 : https://engineering.linecorp.com/ko/blog/detail/80 ) <br/>

#### ㅁ Resource Manager
카프카 스트림즈는 yarn 이나 mesus 같은 리소스 매니저를 통해 띄우지 않습니다. <br/>
( 물론 apache slider 나 다른 방법을 통해 띄우는 것들은 제외 하겠습니다. 기본 docs 에 없음을 말할 뿐 입니다) <br/>
{{사진}} <br/>
그게 꼭 나쁜걸까요? Yarn 이나 Mesus 나 Network Resource Managing 은 하지 못합니다. <br/>
누군가 큰 쿼리를 돌리면 Streaming 서비스가 정상적으로 돌지 않는 ( 클러스터 전체가 정상적으로 돌지 않는 )
상태가 발생하기도 합니다
 Streaming 서비스 같은 Long Running Service 들은 Stand Alone 형태로 띄울때가 ( = 네트워크 사용이나 리소스 사용이
 예측이 안되는 클러스터와는 별도의 존에서 ) 나을 수도 있다는 생각이 듭니다. <br/>
 
#### ㅁ Client's Service Discovery
{{ 인프라에즈코드책 }} <br/>
이 책을 인용하자면 "인프라에서 동작중인 애플리케이션과 서비스는 종종 다른 애플리케이션이나 서비스를 찾는 방법을 알아야한다" <br/>
동일 토픽을 동일 group id 로 컨슘하고 있는 서버를 찾는 방법이 명령어 한줄에 뽝! 되는 그런 클린한 방법이 없습니다. <br/>
즉, 관리하던사람이 아닌 잘 모르는 사람, 인수인계 받아야 하는 사람이 오면 문서 없이는 꽤 고생하겠죠 <br/>

#### ㅁ Monitoring
Kafka Streams Client 에 대한 모니터링이 존재하지 않습니다. ( = 별도로 붙여야 합니다. )
요샌 APM 이 쩌는게 워낙 많아서리... ㅎㅎ 
VM 이나 Application 에 대한 모니터링이 워낙 잘 되어 있어 그런 부분의 솔루션이 회사에 존재한다면
이부분도 해결은 가능합니다 :)
Kafka Cluster 의 상태를 살펴 볼수 있는 Cruise Control for Apache Kafka 과 함께 쓰면 더 좋을것 같기도 하네요 :) <br/>
( 링크 : https://engineering.linkedin.com/blog/2017/08/open-sourcing-kafka-cruise-control ) <br/>

#### ㅁ Streaming SQL Engine
{{ 사진 }}

Data Streaming 을 SQL 을 이용해서 Table 처럼 정의하고 Window 크기 만큼
빼서 사용이 가능하도록 만든 Kafka 만의 SQL Engine 입니다.
InfluxDB + Grafana 를 사용해서 Visualization 쉽게 가능하도록 되어 있네요!
자세한 설명은 아래 링크에서 튜토리얼 영상을 보세요 :)
( 링크 : https://github.com/confluentinc/ksql )


## Spark Streaming
짱이에요. 그저 말이 필요없습니다.
{{ spark github 사진 }}

#### ㅁ This is not native streaming. Just "Micro Batch"
스팍 스트리밍은 스트리밍이 아니죠. 마이크로 배치 입니다.
event loop 가 돌며 batch job 을 계속 submit 하는 식으로 구현되어져 있습니다.
그래서 느리죠. 느려요. 느립니다 
{{ 뭣이 중헌디 }}
근데 서비스하면서 많이 느끼는게 정말 님들의 서비스는
"1초도 못 기다림."
"2초도 못 기다림."
"3초도 못 기다림."
수 초도 지연되면 안되는 서비스 인가요? 물론 그런 서비스이실수도 있고,
아닐수도 있습니다. 수초도 지연되선 안된다면 Spark Streaming 은 절대 쓰시면 안됩니다.
단 그 부분만 Okay 된다면 Spark Streaming 만큼 괜찮은 서비스가 없습니다.
그 이유는 Micro Batch 특성 때문인데요. Native Streaming 과 Micro Batch 를 둘다 코딩해보신 분들은
왜 Micro Batch 가 좋은지 느낄 수도 있을것 같아요 ( 아! 물론 개인차가 있을순 있습니다 ㅎㅎ )
이렇게 Micro Batch 로 나눠져 있다는게 코딩할때 생각보다 생각을 덜 하게 해줍니다. :)

#### ㅁ Spark UI
{{ Spark UI 사진 }}
거의... 이거때문에 Spark 쓴다고 해도 과언이 아닐 정도로 잘 되어 있습니다.
이거 없이 Spark 운영한다고 하는 사람은 Spark 운영을 하지 않은 사람일 것입니다.
근데 이 부분 때문에 많은 오해가 생기기도 하더라구요.
그 예로 하나가 Delay Time 입니다.
Dealy Time 과 Streaming Latency 는 같은 값일까요? 또는 서로 비슷한 추세라도 보일까요?
답은 아닙니다. 

Streaming Latency 는 보통( 살짝 다르게 쓰기도 하지만 ) 
Streaming Latency = 메시지(로그)의 Processing 처리 완료 시간 - Event 발생 시간 
입니다.
Spark UI 에 나오는 Delay Time 은
Delay Time = Real Processing Time(실제 배치를 프로세싱 하는데 걸린 시간 ) - Micro Batch's duration 
입니다. :)
잘 만들어진 UI 는 편하긴 하지만 그 의미를 잘 모르면 오해를 불러일으키기 쉽습니다.



#### ㅁ Log 보는법
힘듭니다. {{ 쫌자세히쓰기 }}

<br/>
## Flink
{{ 사진 _ }}
순수 혈통의 Streaming F/W Flink 입니다. <br/>
Kafka 는 Message Queue 로 부터 Spark 는 Distributed Computing for Batch Job 으로 부터 나왔습니다. 
하지만 Flink 는 다르죠. 처음부터 Streaming 을 위해 나왔습니다. <br />
Flink... 짱입니다.. <br />
로고가 동물인게 마음에 듭니다. 역사적으로 로고가 동물인게 잘 되더라구요.<br />
Docker, Go, Linux 다 동물입니다 :) <br />
{{ 사진 }} <br />

아래에서 Flink 특징들 보면서 기능상 장단점을 한번 볼게요. :) <br />

#### ㅁ 모니터링

#### ㅁ Log finder

#### ㅁ Job Start & Cancel

#### ㅁ Docs..... good...



<br />
## 마지막으로...
아래는 개인적인 의견을 한번 정리해보았습니다.
ㅁ 꼭 Resource manager 가 필요하다고는 생각 안해요


ㅁ 스트림즈만 하고 싶다면 Flink 를!
{{ 사진 }}

아래 영상을 보시면 Spark 은 Streaming Join 이 불가능하지만,
Flink 는 가능하다. 라는 설명이 나옵니다. 그만큼 스트리밍 관련되서 많이 발전된건 아직 Flink 인것 같네요. 그리고 Spark 의 Micro batch 또한 Streaming 에서는 그 한계를 보이는것 같습니다.
( 링크 : https://www.youtube.com/watch?v=ZZevulsXp0g )

ㅁ 수초의 Latency 도 견딜수 없다면 Kafka or Flink 를!
위에서 설명했듯이 Spark 의 Micro Batch 구조상 1초 아래로 Duration 을 내리는게 거의 불가능하다 보시면 됩니다 :)

ㅁ 쫌 더 세분화된 Windowed 기능을 이용하고 싶다면 Flink, Kafka 를!


ㅁ 딥러닝과의 Integration 을 고민한다면... 현재시점에선 Spark 일듯!


ㅁ 난 하나밖에 못하오.... 라고 한다면 Spark 를?!




