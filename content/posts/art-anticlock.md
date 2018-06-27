+++
title = "반시계 Anticlock"
author = ["azurelysium"]
date = 2018-06-17
lastmod = 2018-06-19T01:36:37+09:00
tags = ["dont-export-during-make-test"]
categories = ["posts"]
draft = false
weight = 2001
foo = "bar"
baz = "zoo"
alpha = 1
beta = "two words"
gamma = 10
+++

**_COMPUTER X ART 시리즈 - 연산 Computation_**

컴퓨터의 연산 기능을 활용하여 컴퓨터-예술 작품을 만들어 본다.
<hr>

시계의 용도는 시간을 정확하게 알려주기 위함이다. 하지만 반시계는 시간을 모호하게 지시한다. 시간이 흘러 감에 따라
달라져 가는 풍경처럼 명멸하는 이미지를 재료 삼아 시간 정보를 전한다. 분명 시침분침초침의 이미지 보다 시간의 본질에 가까운
이미지일 것이다.

기본적인 아이디어는 현재 시각을 간접적으로 보여줄 수 있는 이미지로 시계를 대신하는 것이다. 이를 위해 각 시각을
표시하기에 적합한 이미지들을 찾아야 한다. 예전 대학원 다니던 시절, 여러 실험을 위해 데이터셋을 구했던 경험이
떠올랐다. 그래서 컴퓨터 비전 혹은 이미지 프로세싱 쪽의 데이터셋을 뒤져보았고 다음 주소에서 이미지 세트를 구할 수
있었다.

<http://placeschallenge.csail.mit.edu/>

약 3천여장의 이미지가 있는데, 우선 이미지들의 크기를 동일하게 만든다. 처음에 에러가 발생해서 왜 그런가 했더니 컬러
표시하기 위한 특수코드? 때문에 그랬다. ls 옵션에 색 사용 안하는게 있어서 그걸 추가하니 된다.

{{< highlight shell >}}
% ls --color=never -1 | parallel --no-notice -X -n 1 convert '{}' -resize 400x400\\! ../resized/'{}'
{{< /highlight >}}

이제 각 시각을 나타내 줄 이미지가 필요하다. 오픈소스 이미지 편집도구인 GIMP를 이용하여 아래 시침 이미지를 만들었다.

<table>

<tr>
<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-00.png" alt="anticlock-hands-00.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-01.png" alt="anticlock-hands-01.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-02.png" alt="anticlock-hands-02.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-03.png" alt="anticlock-hands-03.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-04.png" alt="anticlock-hands-04.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-05.png" alt="anticlock-hands-05.png" width="100" />
</td>
</tr>

<tr>
<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-06.png" alt="anticlock-hands-06.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-07.png" alt="anticlock-hands-07.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-08.png" alt="anticlock-hands-08.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-09.png" alt="anticlock-hands-09.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-10.png" alt="anticlock-hands-10.png" width="100" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-hands-11.png" alt="anticlock-hands-11.png" width="100" />
</td>
</tr>

</table>

시침 이미지와 이미지 세트에 들어있는 후보 이미지 사이의 유사도를 정의한 다음, 유사도 점수를 구한다. 이 유사도 점수는
나중에 시각별 이미지를 구성하는데 쓰인다. 시침 이미지와 가장 유사한 후보 이미지 N개를 선택한 다음, 가중치를 주어
합산하면 해당 시각 이미지가 만들어 진다. 만들어진 이미지의 특징은, 선택한 시각의 시침 부분이 전체 이미지의 다른 부분보다
밝다는 것이다.

유사도 계산 알고리즘은 다음과 같다.

1.  시침 이미지와 후보 이미지를 그레이스케일로 변환한다.
2.  시침 이미지를 반전 시킨다 (시침 부분이 1.0의 값을, 나머지 부분은 0.0의 값을 갖는다)
3.  시침 이미지에서 0.01을 뺀다 (시침 부분은 0.09의 값을 나머지 부분은 -0.01의 값을 갖는다)
4.  시침 이미지와 후보 이미지를 Pixelwise Multiplication 한 다음, 그 값을 전부 더한다.

곧, 시침 부분이 밝을 수록 (1.0에 가까운 값), 나머지 부분이 어두울 수록 (0.0에 가까운 값) 유사도가 높아진다.

아래의 결과 예시를 보면, 00:00 시각의 경우 상단 가운데 부분이 밝고, 09:00 시각의 경우 왼쪽 중앙이 밝다. 이 예시는 비교적
뚜렷하게 결과가 보이는 것이지만 때로 두드러지게 명암 차이가 나지 않는 경우도 있다. 이 결과는 이미지 세트 내의 후보
이미지들에 종속적이기 때문에 다양한 이미지를 확보할 수록 보다 명확한 결과 이미지를 얻을 수 있다.

<table><tr>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-output-0000.jpg" alt="anticlock-output-0000.jpg" width="300" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-output-0500.jpg" alt="anticlock-output-0500.jpg" width="300" />
</td>

<td style="border: 1px solid gray;">

<img src="/ox-hugo/anticlock-output-0900.jpg" alt="anticlock-output-0900.jpg" width="300" />
</td>

<tr></table>

anticlock.py
{{< highlight python >}}
\#!/usr/bin/env python3

from operator import itemgetter
from glob import glob
import numpy as np
import pickle

import matplotlib as mpl
mpl.use('TkAgg')

import matplotlib.pyplot as plt
plt.ion()

import skimage
from skimage.io import imread, imsave, imshow
from skimage.util import invert
from skimage.color import rgb2gray
from skimage.exposure import rescale\_intensity

skimage.io.use\_plugin('matplotlib', 'imshow')

def compute\_scores():
    clocks = {}
    for fname in glob('clock-hour-hands/\*png'):
        clock = imread(fname)
        clock = clock[:,:,:3]
        clock = invert(clock)
        clock = rgb2gray(clock)
        clocks[fname] = clock

def compute\_similarity(clock, img):
    return ((clock - 0.01) \* img).sum()

scores = {}

for fname in glob('resized/\*jpg'):
    print('>> processing {} ..'.format(fname))
    image = rgb2gray(imread(fname))

for clock\_key in clocks.keys():
    if clock\_key not in scores:
        scores[clock\_key] = []

score = compute\_similarity(clocks[clock\_key], image)
scores[clock\_key].append( (fname, score) )

for key in scores:
    scores[key] = sorted(scores[key], key=itemgetter(1), reverse=True)

with open('scores.pckl', 'wb') as f:
    pickle.dump(scores, f)

def make\_layered\_image\_hourly():
    scores = None
    with open('scores.pckl', 'rb') as f:
        scores = pickle.load(f)

for key in scores:
    print('>> processing {} ..'.format(key))

layered = np.zeros((400, 400), dtype=np.float64)
candidates = [fname for fname, \_ in scores[key][:30]]

for i, fname in enumerate(candidates):
    image = rgb2gray(imread(fname))
    layered = layered + (image / (i + 1))

layered = rescale\_intensity(layered)
imsave('hourly/{}'.format(key.split('/')[-1]), layered)

def make\_layered\_image\_per\_minute():
    scores = None
    with open('scores.pckl', 'rb') as f:
        scores = pickle.load(f)

for hour in range(12):
    for minute in range(60):

print('>> processing {:02}:{:02} ..'.format(hour, minute))
n = 20

key\_curr = 'clock-hour-hands/{:02}.png'.format(hour)
key\_next = 'clock-hour-hands/{:02}.png'.format((hour + 1) % 12)

candidates\_curr = [fname for fname, \_ in scores[key\_curr][:n]]
candidates\_next = [fname for fname, \_ in scores[key\_next][:n]]

ratio = 1 - (float(minute) / 60)

layered = np.zeros((400, 400), dtype=np.float64)
for i in range(n):
    image1 = rgb2gray(imread(candidates\_curr[i]))
    image2 = rgb2gray(imread(candidates\_next[i]))

layered = layered + ratio \* (image1 / (i + 1))
layered = layered + (1 - ratio) \* (image2 / (i + 1))

layered = rescale\_intensity(layered)
imsave('per-minute/{:02}{:02}.jpg'.format(hour, minute), layered)

if <span class="underline"><span class="underline">name</span></span> == '<span class="underline"><span class="underline">main</span></span>':
    #compute\_scores()
    #make\_layered\_image\_hourly()
    make\_layered\_image\_per\_minute()

{{< /highlight >}}

anticlock-labeling.sh
{{< highlight shell>}}
\#!/bin/bash

echo 0000; convert ./per-minute/0000.jpg -pointsize 20 -fill black -annotate +20+30 00:00 ./with-label-per-minute/0000.jpg
echo 0001; convert ./per-minute/0001.jpg -pointsize 20 -fill black -annotate +20+30 00:01 ./with-label-per-minute/0001.jpg
echo 0002; convert ./per-minute/0002.jpg -pointsize 20 -fill black -annotate +20+30 00:02 ./with-label-per-minute/0002.jpg
echo 0003; convert ./per-minute/0003.jpg -pointsize 20 -fill black -annotate +20+30 00:03 ./with-label-per-minute/0003.jpg
..
{{< /highlight >}}

가중치를 구한 다음 곱하는 방식으로 분 단위 결과 이미지를 생성할 수 있었다. 그리고 생성한 이미지들을 시간 순서대로 나열한 후
애니메이션을 만들었다. 왼쪽 상단에 시:분 형태로 시각이 표시된다. 프레임 간 지연시간을 짧게 했기 때문에 시간은 몇 배로
빨리 흐르고 그에 따라 변화하는 이미지를 쉽게 확인할 수 있다.

{{< figure src="/ox-hugo/anticlock-output-animated.gif" width="400" >}}

반시계 완성!