---
layout: post
title:  "Github Pages에 jekyll 설치하기"
date:   2017-04-03 01:20:00
author: smilewjp
categories: Dev
tags:	jekyll
cover:  "/assets/instacode.png"
---

GitHub Pages를 사용하여 블로그를 운영할 수 있다는 것은 알고 있었지만 실제로 해본적은 없었습니다.

그런데 회사 회의중 블로그를 만들어서 경험을 남겨가는 것이 어떻겠느냐는 의견이 나와서 github에 jekyll를 적용하여 만들었습니다.
이미 Github을 사용하고 있었기에 가급적 관리가 편한 방법을 선택했습니다.
(회사가 워낙에 작아서 흔한 홈페이지 하나 없습니다. 그래도 개발자들이 있으니 블로그 틀이라도 만들어 놓으면 어쩌다 글하나라도 쓰지 않을까 하는 막연한 기대감이 있습니다.)

Github에 jekyll를 적용해서 설치하는 글은 많으니 저는 적용하면서 겪었던 문제점들에 대해서 적겠습니다.
(아무 생각없이 설치를 완료하고 나서 첫 글로 설치에 관해서 적으려고 하니 스샷을 찍어놓은게 없다는 사실!!ㅠ)

## Ruby 버전 변경
jekyll는 github 기반에서 돌아기므로 gibhub에서 원하는 버전을 사용해줘야 제대로 설치가 됩니다. 그래서 mac에서 ruby 버전을 맞게 해줘야 제대로 설치할 수 있습니다.
저의 경우에는 맥에 설치된 Ruby 버전은 v2.2.3 버전이었습니다.
```
ruby 2.2.3p173 (2015-08-18 revision 51636) [x86_64-darwin15]
```

저는 jekyll 3.4.3 버전을 설치하려고 했는데 이를 위해서는 Ruby v2.3.3 버전이 필요했습니다.

그래서 rbenv를 사용하여 루비를 설치했습니다.

{% highlight sh %}
brew install rbenv ruby-build

# Add rbenv to bash so that it loads every time you open a terminal
echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
source ~/.bash_profile

# Install Ruby
rbenv install 2.3.3
rbenv global 2.3.3
ruby -v
{% endhighlight %}
출처 - [https://gorails.com/setup/osx/10.12-sierra][ruby-install]

## jekyll 설치를 위한 의존성 확인
jekyll는 Github에서 돌아가기 때문에 Github에서는 jekyll 설치를 위해 필요한 라이브리의 버전을 알려주고 있습니다.

[https://pages.github.com/versions/][jekyll-versions]

페이지에는 각 jekyll 버전별로 필요한 라이브러리들의 버전들을 확인할 수 있습니다.

## nokogiri 1.7.1 설치 중 실패
nokogiri 1.7.1을 설치하는 중에 실패가 발생해서 확인해보니 `libxml2 2.9.4` 를 설치하면서 에러가 발생했습니다.
긴 메시지에서 에러인 부분을 찾아봤습니다.
{% highlight sh %}
error: use of undeclared identifier 'LZMA_OK'
{% endhighlight %}
이게 무슨 에러일까 검색을 하다보니 다른 버전의 nokogiri에서 같은 에러가 나오는 것이 보고된 것을 찾았습니다.
https://github.com/sparklemotion/nokogiri/issues/1483 그곳이 답변중
{% highlight sh %}
$ xcode-select --install
{% endhighlight %}
를 실행하여 xcode 명령도구를 설치한 후 다시 시도하니 문제없이 설치가 되었습니다.

## Could not find i18n-1.1.0 in any of the sources 에러
jekyll를 설치하고 실행을 했는데 `i18n-1.1.0`를 찾을 수 없다는 에러가 났습니다. [stackoverflow][stackoverflow]에는 언제나 답이 존재했습니다.
bundler를 사용하여 설치하면서 gem과 연관되어 발생하는 버그 인것 같다고 합니다.
{% highlight sh %}
bundle pack
bundle install --path vendor/cache
{% endhighlight %}

## 테마(theme) 적용
jekyll에 적절한 테마를 적용해서 사용하는게 관리 부담이 줄어들것 같아서 테마를 검색했고 [centrarium 테마][centrarium]가 적절해 보여서 적용했습니다.
테마는 별도의 skin이나 플러그인 형태가 아니라 jekyll를 커스텀하여 미리 구성된 배포본 같은 것어서 다운로드 받은 파일의 내용을 복사하고 `bundle install`로 설치하여 새로운 jekyll 설정을 만들면 되는 것이었습니다.
설치후에는 사용자의 설정에 맞게 `_config.yml` 파일을 수정하면 됩니다


## 후기
* jekyll를 설치하면서 문제가 조금 있었지만 생각보다 쉽게 설치했습니다. 간단하게 테마를 적용해서 보기 좋은 사이트가 나오니 좋고, Github에서 돌아가니 별도 서버에 대한 부담도 없어서 좋은것 같습니다.
* jekyll 자체를 설치하는 부분에서 문제가 좀 있었지만 설치가 완료된 지금은 jekyll에 우리가 원하는 기능들을 추가하는 일이 또 남아있습니다. 여유가 있을 때 조금씩 진행해야겠습니다.
* 설치하면서 수정한 부분들만 적어서 각 이슈에 대해서 정확한 원인이 무엇인지를 제대로 파악하지는 못했습니다. 그리고 내용에서 오류가 있을 수도 있습니다. 좀더 공부한 다음에 잘못적었다고 생각되는 부분은 바로바로 수정하겠습니다.


[ruby-install]:     https://gorails.com/setup/osx/10.12-sierra
[jekyll-versions]:  https://pages.github.com/versions/
[stackoverflow]:    http://stackoverflow.com/questions/20599920/how-to-solve-could-not-find-i18n-0-6-5-in-any-of-the-sources-bundlergemnotfo
[centrarium]:       https://github.com/bencentra/centrarium