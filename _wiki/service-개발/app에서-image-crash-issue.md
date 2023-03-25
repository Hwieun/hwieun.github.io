---
layout  : wiki
title   : app에서 image crash issue
summary : 
date    : 2023-03-24 17:59:38 +0900
updated : 2023-03-24 18:51:22
tag     : 
toc     : true
public  : true
parent  : [[_wiki/service-개발]]
latex   : false
resource: 0BD4CB71-1EF8-4228-BC38-90585F05399E
---
* TOC
{:toc}

내가 해결한 이슈는 아니지만 문제 해결 접근이 인상적이어서 남긴다.

# 😱 Problem 

가져오려는 이미지의 용량이 큰 경우 앱이 메모리 부족으로 인해 crash가 발생

# 🧐 Approach

CDN 에서 제공하는 파라미터를 이용해 압축 및 크기 제한하여 내려주는 것으로 url을 변경했다.
