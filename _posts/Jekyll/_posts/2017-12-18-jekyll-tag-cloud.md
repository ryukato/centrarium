---
layout: post
title: Jekyll 블로그에 태그 클라우드 만들기
date: 2017-12-18 09:36:55
tags: [Jekyll, tag-cloud, 태그-클라우드]
categories: [Jekyll]
---

* TOC
{:toc}

# Jekyll 블로그에 태그 클라우드 만들기
![](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/Web_2.0_Map.svg/1024px-Web_2.0_Map.svg.png)
[태그 클라우드](https://ko.wikipedia.org/wiki/태그_클라우드)는 위키 피디아의 정의와 비슷하게 블로그 상에서 사용된 태그들을 한곳에 모아 표시 해놓은 것을 말합니다.

위의 이미지처럼 예쁘진 않지만 간단한 태그 클라우드를 만들어서 블로그에 추가해 보겠습니다.

## 필요한 코드
### 태그 가져오기 코드
태그를 가져오기 위한 코드는 아래와 같습니다. 제 블로그에는 태그는 아니고 카테고리를 가져오도록 했습니다.
아래의 코드에서 ```site.categories``` 이 부분을

```
<div class="collection">
  <p><strong>Skills and Interests</strong></p>
  <ul class="TagCloud">
  {% for cat in site.categories %}
    <li><a href="/search/?category={{ cat | first }}"><strong class="Tag">{{ cat | first }}</strong></a></li>
  {% endfor %}
</ul>
</div>
```

### 태그 클라우드 스타일

```
/*ul*/
.TagCloud {
  list-style: none;
  padding-left: 0;
  text-align: center;
}
.TagCloud li {
  display: inline;
}

.Tag {
  color: #111;
  text-transform: uppercase;
  font-size: 1.0rem;
  font-weight: 700;
  line-height: 2;
  position: relative;
  display: inline-block;
  padding-left: 1.0rem;
  padding-right: 1.0rem;
}
.Tag:before {
  content: "";
  position: absolute;
  z-index: -1;
  top: 0;
  left: 0;
  right: 0;
  height: 1.2em;
  bottom: 0;
  margin: auto;
  border-radius: 0.2rem;
  transition: all 0.8s 0.4s ease-in-out;
}
.Tag:hover:before {
  transform: scale(1.02, 1.50);
  background-color: #f46842;
  transition-delay: 0s;
  transition-duration: 0.2s;
}
```
