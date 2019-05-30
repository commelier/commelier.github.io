---
layout: post
title: fork 뜬 jekyll-github page 블로그 Jekyll 로컬 환경 세팅하기
date:   2019-05-14
comments: true
---


이 글은 나중에 또 랩탑을 싹 밀고 새로 세팅하고 싶어질 변덕스러운 미래의 나에게 바치는 헌정글이다.

OS : MacOS Mojave 10.14.4  
사용한 Tool : Terminal

깃허브 페이지 블로그를 세팅하면서 많이 마주쳤던 이야기는 '포크만 떠서 환경설정만 바꿔 주면 아주 편해요.'였다.

포크 떠서 원샷원킬로 블로그를 포스팅할 사람들에게는 아주 적합한 조언이다.  

하지만 나처럼 기분에 따라 테마를 마음껏 바꾸고 그때그때 글이 실제 페이지에서 어떻게 보일지 확인하고 싶은 사람이라면...?
  
커밋과 푸시를 수시로 해서 블로그가 퍼블리싱 될 때마다 확인을 해줘야 한다.         

그렇기 때문에 우리는 로컬호스트에서 꼭 확인할 수있도록 환경 설정을 할 필요가 있다.   

---

#### 일단 포크는 떠서 `_config.yml`까지는 설정이 되었다고 가정한다.  

지킬을 세팅하는 데에 필요한 준비물(?)들이 있다. 지킬 홈페이지에서 간단하게 확인 가능하다.  


>[설치 준비물 링크 바로가기](https://jekyllrb-ko.github.io/docs/installation/#requirements)          


1. 루비 2.2.5버전 또는 그 이상
2. RubyGems
3. GCC, Make(gcc -v, g++ -v, make-v 명령어로 설치 확인 가능)



맥OS high sierra 이상에서는 루비 2.3.3이 함께 제공된다고 하는데 ruby-v 명령어로 버전을 확인 후 설치가 되지 않았거나 2.2.5버전 이하라면 설치해야 한다.

>*루비 설치 링크*
[Download Ruby](https://www.ruby-lang.org/en/downloads/)



RubyGem도 `gem -v` 명령어로 확인 후 설치가 되지 않았으면 설치해준다.

>*RubyGems 설치 링크*
[Download RubyGems | RubyGems.org | your community gem host](https://rubygems.org/pages/download)



아마 갓 맥OS를 설치한 사람이라면 gcc -v 명령어를 쳤을 때 **명령줄 도구 소프트웨어를 설치**하겠다는 **팝업**이 뜰 것이다. **설치**해준다.

이제 위에 언급된 모든 명령어 (ruby -v, gem -v, gcc -v, g++ -v, make -v)를 체크해본다.



그 다음 **지킬을 설치**해준다.

`gem install bundler jekyll`



fork 뜬 git 레파지토리로 디렉토리 이동한다.

우리는 이미 포크를 떠온 아주 잘 되는 깃허브 페이지 레파지토리를 사용하는 거지만 Gemfile이나 Jekyll이 로컬 환경에서는 설정이 안 되어있기 때문에 초기화 하는 단계가 필요하다.

`bundle init`

`bundle add jekyll`

`bundle install`




위 명령어를 친 후 _config.yml 파일을 확인해 로컬에 설치하지 않은 플러그인이 있는지 확인한다. 파일을 열어 확인하기 귀찮다면

`bundle exec jekyll serve`

명령어를 쳐서 로컬 호스트에 serve를 해본다. 

이것저것 플러그인을 많이 설치해 둔 페이지라면 이런 메세지를 보게 될 확률이 높다.

    Dependency Error: Yikes! It looks like you don't have jekyll-gist or one of its dependencies installed. In order to use Jekyll as currently configured, you'll need to install this gem. The full error message from Ruby is: 'cannot load such file -- jekyll-gist' If you run into trouble, you can find helpful resources at https://jekyllrb.com/help/! 
    jekyll 3.8.5 | Error:  jekyll-gist

>*해석 : 너 jekyll-gist 쓴다고 환경설정 파일에는 설정해놓고 로컬에는 세팅이 안 된 거 같은데...?*



jekyll-gist를 설치 안 했다는 단서를 얻었으니 설치해주고 또 serve 명령어를 쳤을 때 다른 플러그인의 Dependency Error가 나면 그것도 똑같은 방법으로 설치해준다.

`sudo gem install jekyll-paginate`

`sudo gem install jekyll-gist`

`bundle add jekyll-paginate`

`bundle add jekyll-gist`



Add가 완료되면

`bundle exec jekyll serve`

명령어를 입력한다.



    Configuration file: /프로젝트 경로/_config.yml
                Source: /프로젝트 경로/프로젝트명
           Destination: /프로젝트 경로/_site
     Incremental build: disabled. Enable with --incremental
          Generating... 
                        done in 0.135 seconds.
     Auto-regeneration: enabled for '/프로젝트 경로'
        Server address: http://127.0.0.1:4000//
      Server running... press ctrl-c to stop.

이런 결과가 보인다면 허겁지겁 브라우저를 켜서

[localhost:4000](http://localhost:4000) 으로 접속해본다! 👏👏👏👏👏 

---





이제 로컬에서 한 땀 한 땀 수정한 글들을 확인할 수 있게 되었다! 예이~!
