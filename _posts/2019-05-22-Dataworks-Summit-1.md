# Dataworks summit 2019 in Washington D.C

우연한 기회로 Dataworks summit 2019 in Washington D.C 를 가게 되었다. 예전에 Data strata 를 참석한 경험이 있어 당시의 좋은 기억들만 가지고 컨퍼런스를 다시 한번 가게 해준 회사에 감사할 뿐이었다. 모든 발표가 ( 당연하게도 ) 맘에 들진 않았지만 그래도 기억에 남는 발표들이 있어 이렇게 기록을 남기려 한다.

### Marmaray: Uber's Open-sourced Generic Hadoop Data Ingestion and Dispersal Framework ( Uber )
 - blog : https://eng.uber.com/marmaray-hadoop-ingestion-open-source/

첫번째 발표는 Uber. (개인적으로 발표자의 회사를 많이 본다. 회사의 서비스가 잘 나갈수록 운영경험에 대한 얘기를 Deep 하게 해주는 경우가 많다. 이점이 양날의 검인게 "이렇게까지 해야 되나.." 할 정도로 과하게 운영하는 회사들도 있다. 회사가 그만큼 크니까 아주 마이크로한 단위까지 신경쓰는 경우도 있기에 이건 본인들 회사의 사정과 비지니스의 특성을 고려하여 적당히 걸러들어야 할지도...)

![_config.yml]({{ site.baseurl }}/images/dataworkssummit/Marmaray_Figure_1_new.png) <br/>

다른 그림이 굳이 필요 없을듯... Source/Sink 의 F/W이 다양한데 그 사이에 ETL 작업이 많다면.. 클러스터 or IDC 별로 분리되어 있다면.. 이런 작업들이 많이 필요 할 듯 하다. 그러면 당연히 Data Platform(?) 만드는 팀이라면 이런 니즈를 느꼈을 듯 싶다.

링크드인의 Gobblin 프로젝트를 통해 영감을 받았다 한다.
<br/>
##### ㅁ Main Feature
 - Automated Schema Management.
 - Monitoring and Alerting Systems.
 - Fully Integrated with workflow orchestration tool.


### Real-time Analytics at PayPal ( PayPal )

이번 컨퍼런스에서 가장 재밌게 들은 발표이다. 


### Event-Driven Messaging and Actions using Apache Flink and Apache NiFi ( Comcast Corporation )


### Introducing MlFlow: An Open Source Platform for the Machine Learning Lifecycle for On-Prem or in the Cloud ( microsoft )


### Learning with Limited Labeled Data ( Cloudera Fast Forward Labs )