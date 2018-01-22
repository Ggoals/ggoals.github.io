---
layout: post
title: Spark, Flink, Kafka Streaming ...
---

## Overview
오늘 다룰 내용은 Intel 에서 spark 에서 추천 서비스를 위해 제공하는 ALS 알고리즘에서 있었던 문제들과 이를 어떻게 튜닝했고, 얼마나 성능이 좋아졌는지에 대해 발표한 내용을 공유하는 자리입니다. 

사실 이 부분에 대해서 공부하고, 발표를 준비 하면서 많이 든 생각은 "스파크 정도 되는 오픈소스를 하시는 분들도 이런 실수를 하는구나...” 를 느끼면서 대용량 데이터를 이용하여 추천 서비스를 하는게 얼마나 어려운지와 "과연 AI 에서는 알고리즘이 다 일까? 빅데이터를 넘어서 AI 의 시대로 가고 있는 이 상황에서 엔지니어들이 positioning, contribute 할 수 있는 부분은 어떤 부분이 있을까?”에 대한 어느정도의 방향이 되었다 생각하여 정말 기쁩니다.

 ![_config.yml]({{ site.baseurl }}/images/als-algorithm-tuning/1.png) <br/>
링크 : [Google’s Hidden Technical Debt in Machine Learning Systems](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf)

구글에서 2015년에 발표한 문서입니다. 대략적인 내용은 "Machine Learning 은 우리에게 판타스틱한 기능을 제공함은 분명하지만 대충대충 빠르게 만든 ML 서비스는 매우 비싸고 힘든 유지비용이 들것이다." 라는 것입니다.

으잉?? 왠 갑자기 ALS 알고리즘 튜닝 얘기하다 말고 이런 걸 설명하죠?? +_+??
Intel 에서 Spark 의 ALS 알고리즘을 튜닝한게, 과연 단순 알고리즘의 문제였을까요?

오늘의 얘기는 제가 한 보여드린 이 그림을 마음속 한편에 두고 한번 들어보시면 어떨까 싶습니다 :)


## Spark Recommendation System
당연히 Spark 문서에도 잘 나와있구요 :)
링크 : [Spark Collaborative Filtering](http://spark.apache.org/docs/latest/ml-collaborative-filtering.html)
<br/>
알고리즘에 대해서는 많은 분들이 잘 설명해주셨습니다. ㅎㅎ
특히 아래 slide share 가 깔끔하게 잘 설명 되어 있더라구요.
링크 : [ALS WS에 대한 이해 자료](https://www.slideshare.net/madvirus/als-ws)
<br/>


## ALS summary
사실 오늘 얘기는 ALS 알고리즘에 대해 자세히 다루진 않을 겁니다. 설명이 잘되어 있는 블로그도 많고 워낙 오래된 알고리즘(?) 이라 저보다 더 많은 고수들이 많으실 것이기에... +_+... ( 절대 귀찮아서 아닙니다... ㅋㅋ )

![_config.yml]({{ site.baseurl }}/images/als-algorithm-tuning/2.png) <br/>
CF 의 한 종류인 MF 를 하는 방법중 하나 인데요. User to Item 의 Score 을 ( 이때 스코어가 explicit 일수도 implicit 일수도 있습니다. ) 표현되어진 Matrix 가 있다면 이를 적당한 Rank 를 가진 User Latent Feature Matrix 와 Item Latent Feature Matrix 로 분해하는 것입니다. 보통 Rank 를 구하는 방법도 여러가지 있지만 Spark 의 ALS 알고리즘 에서는 이를 하나의 하이퍼 파리미터로 생각하고 User 가 Rank 값을 적절하게 정해주도록 되어 있습니다. ( Convex Relaxation 을 통해 구하는 방법도 있는거로 아는데 이건 검색 부탁드려요 😃 )

![_config.yml]({{ site.baseurl }}/images/als-algorithm-tuning/3.png) <br/>
이때 Iteration 한번에 User Vector 를 고정시킨 후 Item Vector 를 변경하고, Item Vector 를 고정시키고 User Vector 값을 변경하고 … 이렇게 여러번 하다보면 상당히 그럴싸한(?) User Vector 와 Item Vector 가 나온다~~ 이런 얘기 입니다. ㅎㅎ 
<br/>
사실 앞 수식이 제일 critical 하고 뒤에는 User 와 Item 의 성향에따라 노멀라이제이션을 할건지, 또 Feature 의 성향에 따라 negative 한 값을 줘도 되는지 아니면 all positive 한 값으로 Feature 를 구해야 하는 지 등 변형해서 쓸 수 있습니다. ( Spark 에서는 nonnegative 는 false 가 default 입니다 ㅎㅎ )
<br/>
<br/>
<br/>

## Problem
###ㅁ GC Problem and OOM frequently in recommendForAll method
링크 : [SPARK-20446](https://issues.apache.org/jira/browse/SPARK-20446)
<br/>
내용은 간단합니다. User Vector * Item Vector 계산 시에 Top Item 을 뽑아오는 로직에서 계산된 모든 결과를 저장하지 않고 가져올 Top N 의 갯수만 저장하겠다는 것입니다. 이전에는 User 별로 Item Prediction Score 를 전부 저장하고 그 걸 sorting 해서 top N 을 가져오는 것이였는데, Item 갯수가 많을 경우 당연히 시스템이 뻗겠죠 ^^;
<br/>
 ㅁ mllib/src/main/scala/org/apache/spark/mllib/recommendation/MatrixFactorizationModel.scala
**변경전**
```scala
...
val srcBlocks = blockify(rank, srcFeatures)
    val dstBlocks = blockify(rank, dstFeatures)
    val ratings = srcBlocks.cartesian(dstBlocks).flatMap {
      case ((srcIds, srcFactors), (dstIds, dstFactors)) =>
        val m = srcIds.length
        val n = dstIds.length
        val ratings = srcFactors.transpose.multiply(dstFactors)
        val output = new Array[(Int, (Int, Double))](m * n)
        var k = 0
        ratings.foreachActive { (i, j, r) =>
          output(k) = (srcIds(i), (dstIds(j), r))
          k += 1
...
```

<br/>
<br/>
**변경후** 
```scala
...
val srcBlocks = blockify(srcFeatures)
    val dstBlocks = blockify(dstFeatures)
    val ratings = srcBlocks.cartesian(dstBlocks).flatMap { case (srcIter, dstIter) =>
      val m = srcIter.size
      val n = math.min(dstIter.size, num)
      val output = new Array[(Int, (Int, Double))](m * n)
      var j = 0
      val pq = new BoundedPriorityQueue[(Int, Double)](n)(Ordering.by(_._2))
      srcIter.foreach { case (srcId, srcFactor) =>
        dstIter.foreach { case (dstId, dstFactor) =>
...
```
<br/>
<br/>
###ㅁ Block-Size is static 
링크 : [SPARK-20443](https://issues.apache.org/jira/browse/SPARK-20443)



SPARK-20446: https://issues.apache.org/jira/browse/SPARK-20446
SPARK-20443: https://issues.apache.org/jira/browse/SPARK-20443
SPARK-20638: https://issues.apache.org/jira/browse/SPARK-20638
SPARK-21305: https://issues.apache.org/jira/browse/SPARK-21305



링크 : [ Spark ALS Example ] (https://github.com/apache/spark/blob/master/examples/src/main/scala/org/apache/spark/examples/ml/ALSExample.scala)


