---
layout: post
title: "Jupyter Notebook and RStudio on GCP"
date:   2019-03-28 09:46:59
author: Juyoung Ahn
categories: GCP
tags: GCP ananconda3 JupyterNotebook Rstudio
---

구글의 클라우드 컴퓨팅 서비스인 GCP에서 아나콘다와 R-Studio를 설치하고 Clinent환경에서 web browser를 통해서 접속해보자

* Google Cloud Platform([https://cloud.google.com/](http://cloud.google.com/))에 접속하여 해외결제가 가능한 카드를 등록하여야 한다
* 최초 가입시 1년간 사용가능한 300$ credit을 제공한다
* VM 인스턴스 생성과 방화벽 설정은 향후 포스트에서 다룰 예정
* OS의 경우 Ubuntu LTS 16.04를 기준으로 진행

## anaconda3 설치 및 Jupyter Notebook 접속

1. **anaconda3 설치**  
최신버전의 anaconda3의 경우 python3.7을 지원하는데 tensorflow-gpu 등의 모듈을 설치할 때 문제가 생기는 경우가 발생하므로 Anaconda3-5.2.0를 설치한다. 우선 GCP에서 지원하는 SSH 콘솔에 접속
```
$ mkdir tmp
$ cd tmp
$ wget https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
$ bash Anaconda3-5.2.0-Linux-x86_64.sh
$ source ~/.bashrc
```
설치파일을 받기위하여 tmp라는 폴더를 생성하고 해당폴더에 .sh 설치파일을 다운로드 한 후 install하는 과정이다. 과정중에 사용하기 위한 약관을 보여주는 부분에서 Enter를 쳐서 진행하고  
**1)** 설치를 할 것인지 묻는 곳에서 y혹은 yes를입력  
**2)** path와 관련된 질문에서 yes를 입력  
**3)** VS code를 설치할 것인지 묻는 창에서는 no를 입력해 주면된다.

2. **Jupyter Notebook 접근을 위한 비밀번호 생성**  
$로 시작하는 코드는 linux console에 명령어를 입력하는 부분이고, \>\>\>로 시작하는 부분은 python 명령어를 입력하라는 부분이다
```
$ python
>>> from notebook.auth import passwd
>>> passwd()
Enter password:
Veryfy password:
'sha1:761980024bdb:3379e2ab50f8dd8333f99c2f46b6eb1fa81cde1b'
>>> quit()
```
비밀번호를 입력하는 창에 \*\*\*같은 표시가 나타나지 않더라도 실제로는 입력되고 있으므로 당황하지 말고 비밀번호를 설정한다
'sha1:761980024bdb:3379e2ab50f8dd8333~'부분은 본인이 입력한 비밀번호를 암호화한 부분이다. 이 값을 복사해 두도록 한다

3. **Jupyter Notebook 설정파일 생성 및 설정 변경**  
외부에서 Jupyter Notebook에 접근하기 위해서 설정을 해주는 부분이다
```
$ jupyter notebook --generate-config
$ sudo nano ~/.jupyter/jupyter_notebook_config.py
```
주피터 노트북 설정 파일을 생성해준후 nano 에디터로 접근하여 내부의 정보를 수정해준다. text에디터로는 주로 vi에디터를 사용하지만 vi에디터 사용이 익숙하지 않다면 nano에디터를 사용하는 것을 추천한다
```
c.NotebookApp.ip = ‘*’
c.NotebookApp.open_browser = False
c.NotebookApp.port = 5000
c.NotebookApp.password = u'sha1:761980024bdb:3379e2ab50f8dd8333f99c2f46b6eb1fa81cde1b'
```
위에서부터 설명하면 
	* 외부에서 접근가능한 ip주소를 모든 ip에서 접근가능하게 설정해주고
	* 주피터 노트북을 실행할때 자동으로 브라우져가 켜지지 않도록 설정한다.
	* 주피터노트북의 기본 포트는 8888이지만 연세대학교에서 8888포트	의 접근을 막고있기 때문에 임시로 포트번호를 5000번으로 변경해준다
	* 위에서 복사해둔 값을 u를 앞에 붙여서 지정해준다. u의 의미는 unicode를 의미한다
	* nano에디터를 사용하기 위해서는 콘솔창 아래부분에 나와있는 단축키를 보면 이해하기 쉽다  ^키는 Ctrl키를 의미한다. nano에디터를 종료하		기위해서 Ctrl+x키를 누르고 저장여부를 물어보면 y 그리고 동일한 파일에 덮어쓰기위해 Enter를 치면 nano에디터가 종료된다.

4. **Jupyter Notebook 실행 및 client 접속**  
```
$ jupyter notebook
```
을 입력하면 콘솔창에서 jupyter notebook이 실행되고 GCP에서 지정되어있는 ip주소를 이용하여 외부에서 접근가능하다 예를들어 본인의 GCP VM인스턴스의 ip주소가 0.0.0.0이라면 http://0.0.0.0:5000 이라는 주소를 웹 브라우저에 입력하면 접속가능하다. 비밀번호를 입력하라는 창에서는 본인이 python에서 passwd()에서 입력한 비밀번호를 입력하면 된다.
```
$ nohup jupyter notebook &
```
그러나 본인이 console창을 닫을경우 jupyter notebook도 동시에 종료되어서 접속이 불가능한 문제가 발생한다 이러한 경우에는 위와 같은 코드를 입력하면 console창을 종료하더라도 접근이 가능하다.

## R설치와 R-Studio Server 설치

1. **linux계정 비밀번호 설정**  
본인의 GCP 콘솔창을 확인하면 **{계정명}@{인스턴스명}:~$** 와 같은 표시를 확인할수 있다. 이때 R-Studio Server에 접속하기 위한 아이디와 비밀번호는 본인의 계정명과 계정 비밀번호이다. 따라서 본인의 linux계정의 비빌먼호를 우선 설정해 주어야한다
```
$ sudo passwd 계정명
```
본인의 계정에 대한 비밀번호를 설정해 준다. 앞의 경우와 마찬가지로 console창에 \*\*\*와 같은 비밀번호가 입력되고 있다는 코드가 표시되지 않지만 침착하게 본인의 비밀번호를 입력하고 확인하면 된다.

2. **R 설치**  
Window에서 R-Studio를 설치하고 사용하기 앞서서 R을 설치하는 것과 마찬가지로 linux에서 R-Studio Server를 설치하기 이전에 R이 우선 설치되어있어야한다.
```
$ cd ~
$ cd tmp
$ echo "deb http://cran.cnr.Berkeley.edu/bin/linux/ubuntu `lsb_release -sc`/" | sudo tee --append /etc/apt/sources.list.d/cran.list
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9
$ sudo apt update
$ sudo apt install r-base
```
위의 명령어를 차례대로 입력하고 설치할꺼냐고 y/n을 묻는 과정에 동의를 하다보면 설치가 완료될 것이다.

3. **R-Studio Server 설치**  
이제 외부 client에서 접근 가능한 R-Studio Server를 설치할 차례이다.
```
$ sudo apt-get install gdebi-core
$ wget https://download2.rstudio.org/rstudio-server-1.1.383-amd64.deb
$ sudo gdebi rstudio-server-1.1.383-amd64.deb
$ rstudio-server start
```
위의 명령어를 차례로 입력하면서 진행하면 R-Studio Server가 설치되고 시작될 것이다.

4. **R-Studio Server 접속**  
Jupyter notebook과 비슷한 방식으로 웹 브라우저를 통해서 접속할 수 있다. 이때 R-Studio Server의 포트는 8787포트이다. 따라서 본인의 ip주소가 0.0.0.0이라면 http://0.0.0.0:8787로 접속하면 R-Studio Server에 접속하기 위한 로그인 창을 볼 수 있을 것이다.
이때 username은 앞에서 확인했던 linux계정명이고 password는 앞에서 수정한 password가 된다.

## Cloud관리 및 추가사항

* GCP의 클라우드 컴퓨터에 파일을 업로드/다운로드가 불편한 문제점이 있다. 이때 본인의 OS가 윈도우라면 **mobaXterm**을 사용하여 SSH접속을 한다면 ftp기능까지 지원하므로 파일 입출력이 용이해진다.
* Cloud 컴퓨터는 사용하지 않을경우 중지시켜좋는것이 원칙이다. 계속 VM인스턴스를 켜둘경우 시간에 비례해서 사용요금이 지불되지만, 종료한다면 저장공간에 대한 사용요금만 지불하므로 300$ Credit을 아낄수 있다.

##Codes
```
## 아나콘다 설치

$ mkdir tmp
$ cd tmp
$ wget https://repo.anaconda.com/archive/Anaconda3-5.2.0-Linux-x86_64.sh
$ bash Anaconda3-5.2.0-Linux-x86_64.sh

$ source ~/.bashrc
$ python

>>> from notebook.auth import passwd
>>> passwd()
##비밀번호 입력후 나오는
##'sha1:e1ce4747245a:3946d978361582171122d8275ef857edfb011254' 이 부분 복사
>>> quit()

$ jupyter notebook --generate-config
$ sudo nano ~/.jupyter/jupyter_notebook_config.py


c.NotebookApp.ip = ‘*’
c.NotebookApp.open_browser = False
c.NotebookApp.port = 5000
c.NotebookApp.password = u'sha1:e1ce4747245a:3946d978361582171122d8275ef857edfb011254'

Ctrl+X
y
Enter

##콘솔을 끈 상태에서도 유지하고싶을때
$nohup jupyter notebook &


##계정 비빌번호 설정
$sudo passwd 본인계정명

##R 설치
$ cd ~
$ cd tmp
$ echo "deb http://cran.cnr.Berkeley.edu/bin/linux/ubuntu `lsb_release -sc`/" | sudo tee --append /etc/apt/sources.list.d/cran.list
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys E084DAB9
$ sudo apt update
$ sudo apt install r-base

##R studio 서버 설치
$ sudo apt-get install gdebi-core
$ wget https://download2.rstudio.org/rstudio-server-1.1.383-amd64.deb
$ sudo gdebi rstudio-server-1.1.383-amd64.deb
$ rstudio-server start
```
