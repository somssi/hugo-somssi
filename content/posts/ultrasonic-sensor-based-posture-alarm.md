+++
title = "자세 교정용 거리 측정기"
author = ["azurelysium"]
date = 2016-11-16
lastmod = 2018-06-05T10:45:51+09:00
tags = ["dont-export-during-make-test"]
categories = ["posts"]
draft = false
weight = 2013
+++

회사에 앉아 일하다 보면 어느 순간 나쁜 자세로 일하고 있는 자신을 발견한다. 좋은 자세를 취하며 일하기 위해 간단한 장치를
만들고자 생각했다. 자세를 측정하기엔 부족하지만 적어도 모니터와 머리 사이의 거리를 재면 지나치게 가깝게 다가가는 걸 막을
수 있으리라 생각했다.

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-1.jpg" >}}

이를 위해 집에서 가져온 아두이노 우노 그리고 초음파 센서를 이용하여 간단한 장치를 만들었다. 초음파 센서는 정해진
주기마다 소리를 내어 거리를 재고 측정된 거리는 시리얼 통신을 통해 리눅스 터미널에 출력된다.

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-2.jpg" >}}

모니터 뒷편에 붙여놓은 아두이노 우노

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-3.jpg" >}}

모니터 상단에 위치하여 머리의 위치를 측정한다

코드는 아주 간단하다.

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-4.jpg" >}}

아래 그림을 보면 거리가 들쑥날쑥 나온다. 이런 문제가 발생하는 이유는 초음파 센서에서 나온 음파가 제대로 되돌아가지
못해서 그런 것 같다. 노트와 같이 평평한 물체를 음파 방향과 수직 방향으로 위치시켜 거리를 재보면 안정적으로 거리가
측정되지만 둥근 모양의 내 머리라던가 신체 일부를 대상으로 거리를 측정하면 잘못된 거리가 나오기도 한다. 음파의 반사
방향이 엉뚱해져서 제대로 감지하지 못하나보다.

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-5.jpg" >}}

데이터 처리를 위해 유용한 정보를 얻어볼 수 없을까 생각하여 간단히 살펴본 결과, 자리를 비우면 비교적 편차가 적은 거리가
측정되고 반대로 사람이 자리에 앉아 일하는 경우엔 편차가 큰 거리가 측정된다.

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-6.jpg" >}}

3미터 이하의 측정값만 남긴 경우

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-7.jpg" >}}

50개 길이의 Moving Window로 구한 표준편차

{{< figure src="/ox-hugo/ultrasonic-sensor-based-posture-alarm-8.jpg" >}}

표준편차가 0.2보다 작은 경우, 0 아니면 1로 처리한 결과

자세 교정에 당장 활용할 수는 없지만 자리 앞에 사람이 앉아있는 경우와 아닌 경우를 판단하는데는 이용할 수 있을 것
같다. 다른 방법을 고민해보면 노이즈들을 없애면서 의미있는 거리값만 남길 수 있지 않을까?