+++
title = "손글씨 폰트"
author = ["azurelysium"]
date = 2016-11-16
lastmod = 2018-06-05T10:45:52+09:00
tags = ["dont-export-during-make-test"]
categories = ["posts"]
draft = false
weight = 2014
+++

만들기에 대한 짧은 글을 남기면서, 이곳에 표시될 글자들도 나의 손글씨로 하면 어떨까 하는 생각이 들었다.
그래서 손글씨를 컴퓨터 폰트로 바꿀 수 있는 방법을 찾아보았다. 이곳저곳을 둘러보다 FontForge라는 오픈소스
소프트웨어를 찾았고 이를 이용해 폰트를 만들어 보았다.

FontForge 사이트: <https://fontforge.github.io/en-US/>

먼저 글씨를 적을 격자를 인쇄한 다음, 회사 동료 링링에게 글씨를 적어달라고 부탁했다.

{{< figure src="/ox-hugo/hand-written-font-1.jpg" >}}

적힌 글씨를 스캔하여 이미지로 만든 다음, 격자를 참고하여 각각의 글자 이미지로 잘라낸다. 이 과정을 손으로
하나씩 하기에는 어려움이 있어, 나는 명령행 이미지 편집 도구를 활용했다. 먼저 GIMP를 이용하여 글씨가 적힌
격자 영역을 잘라내었다. 그리고 명도와 대비를 조정하여 글씨 부분이 두드러지게 처리했다.

{{< figure src="/ox-hugo/hand-written-font-2.jpg" >}}

위의 이미지를 잘라내기 위해 ImageMagick 도구를 사용했고, 다음과 같은 명령을 이용했다.

{{< highlight shell >}}
$ convert -crop 92x92 linghe.png tile\_%d.png
{{< /highlight >}}

FontForge에서 여러 이미지 파일을 일괄적으로 불러오기 위해선 파일이름을 uni(유니코드).png 형태로 바꾸어야 한다. 짧은 파이선 코드를 작성하여 이름변경 작업을 수행했다.

{{< highlight shell >}}
$ cat make-uni-hex-name.py
\#!/usr/bin/env python2
import sys
import glob
import subprocess
def run\_shell\_command(cmd):
  return subprocess.check\_output([‘sh’, ’-c’, cmd])

filenames = glob.glob('tile\*png’)
filenames = sorted(filenames, key=lambda x: int(x.split(’\_’)[1].split(’.’)[0]))
code = 0x30
for filename in filenames: cmd = 'mv %s uni%04x.png’ % (filename, code)
print cmd
run\_shell\_command(cmd)
code += 1
{{< /highlight >}}

자, 이제 FontForge를 이용하여 폰트를 만들어보자. FontForge를 실행하면 수정할 폰트를 선택하거나, 새로 만들 폰트 파일을 지정하는 창이 나온다. New 버튼을 눌러 새로운 폰트를 만들자.

{{< figure src="/ox-hugo/hand-written-font-3.jpg" >}}

각 글자에 해당하는 코드가 나와있고 실제 폰트 상의 글자 이미지가 아래에 나온다. 지금은 아무것도 채워져 있지 않아 전부 흐릿한 X 모양으로 되어 있다.

{{< figure src="/ox-hugo/hand-written-font-4.jpg" >}}

File - Import를 눌러 이미지를 불러온다.

{{< figure src="/ox-hugo/hand-written-font-5.jpg" >}}

앞서 만들어 둔 손글씨 글자들의 이미지를 선택한다. 형식은 Image template 이고 이렇게 하면 파일 이름을 참고하여 해당하는 글자 코드에 맞춰 이미지를 로드한다.

{{< figure src="/ox-hugo/hand-written-font-6.jpg" >}}

이미지가 로드된 문자들이 파랗게 표시된다. 이 문자들을 드래그하여 선택한 다음, Element - Autotrace 항목을 클릭한다.

{{< figure src="/ox-hugo/hand-written-font-7.jpg" >}}

{{< figure src="/ox-hugo/hand-written-font-8.jpg" >}}

자동으로 윤곽을 추출하여 생성된 글자 이미지들이 채워진다.

{{< figure src="/ox-hugo/hand-written-font-9.jpg" >}}

마지막으로 File - Generate Fonts 항목을 눌러 새로 만든 폰트를 저장한다.

{{< figure src="/ox-hugo/hand-written-font-10.jpg" >}}

이것으로 손글씨 폰트 만들기 끝! 영문의 경우 알파벳이 적어 수월하지만, 한글 폰트의 경우 손글씨로 적어야 할 글자의 개수가 어마어마하다. 여유로운 분이라면 한번 도전해봐도 좋겠습니다!