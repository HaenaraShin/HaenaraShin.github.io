---
layout: post
title:  "Git 원격 브랜치 분리되어 있을 때 해결법"
feature-img: "assets/img/post/20200114_00.jpeg"
thumbnail: "assets/img/post/20200114_00.jpeg"
description: "fatal : refusing to merge unrelated histories를 해결하는 방법"
date: 2020-01-14 23:15:00 +0900
categories: git
---

#  떠있는 브랜치를 잡아라!

#### 부제 : (fatal : refusing to merge unrelated histories)

 ![catcha]({{ "/assets/img/post/20200114_01.jpeg" | relative_url}})

<br/>
Git이 익숙치 않을 때에는 브랜치 관리하는 것이 쉽지만은 않습니다.<br/>
<br/>
흔히하는 실수 중에 하나는 원격 브랜치가 분리되는 경우입니다.<br/>
GitHub 등에서 원격저장소를 생성하고 Clone을 받아서 체크아웃을 했다면 문제가 없지만…<br/>
<br/>
<br/>

로컬에서 git init 으로 저장소를 생성하고 로컬에서 작업을 하다가 이후에 원격을 추가 하면<br/>
아래와 같이 **브랜치가 붕 떠있는 것**을 목격할 수 있습니다.<br/>

<br/><br/>

![branch]({{ "/assets/img/post/20200114_02.png" | relative_url}})

*로컬 브랜치가 붕 떠있다!*

<br/><br/><br/>

이런 식으로 로컬 브랜치와 origin/master 원격 브랜치가 아예 분리되어 있을 때에는<br/>

![branch]({{ "/assets/img/post/20200114_03.png" | relative_url}})

<br/>
master 브랜치에서 origin/master 를 병합하려 해도<br/>
 
![branch]({{ "/assets/img/post/20200114_04.png" | relative_url}})

#### *” fatal : refusing to merge unrelated histories “*

라며 병합을 거부합니다. 😣😣😣

 <br/><br/><br/><br/>

 

원격 브랜치를 체크아웃 한 다음 병합해도 마찬가지 에러가 발생하기 때문에<br/>
처음 이 에러를 겪는 경우에는 당황할 수 있습니다.<br/>

<br/><br/><br/>

# 해결방법 1
 

이 때는 침착하게 콘솔에서 (소스트리의 경우 우상단에 터미널을 눌러서) <br/>

```bash
$ git rebase origin/master
```

와 같이 rebase 를 해주면<br/>

![branch]({{ "/assets/img/post/20200114_05.png" | relative_url}})

master 브랜치에 그동안 로컬에서 작업한 내용이 깔끔하게 올라갑니다. 👏👏

<br/><br/><br/> 

 

# 해결방법 2
 

또 한가지 방법으로<br/>

```bash
$ git pull origin master –allow-unrelated-histories
```

와 같이 강제로 병합을 시키면<br/>

![branch]({{ "/assets/img/post/20200114_06.png" | relative_url}})

사진과 같이 병합할 수도 있습니다. 👏👏

<br/><br/><br/>