---
layout  : wiki
title   : activeadmin index-column-width-변경하는-방법
summary : 
date    : 2023-03-06 15:25:37 +0900
updated : 2023-03-06 15:39:59
tag     : 
toc     : true
public  : true
parent  : [[_wiki/ruby]]
latex   : false
resource: 5CADD7F5-1FE1-44AB-A6D8-EDC1F7B0316E
---
* TOC
{:toc}

<img width="262" alt="Screen Shot 2023-03-06 at 15 38 53" src="https://user-images.githubusercontent.com/104121915/223036864-07a89790-78b5-452e-8da9-c44877caa3e2.png">
컨텐츠가 매우 옹졸하게 나와서 width를 늘리고자 했다.

# 방법

1. 적용하고자 하는 컬럼에 div class 이름을 설정
  ```
index
   column :title do |object|
      div(class: "title") do
        span object.title
      end
   end
end
  ```

2. assets/stylesheets/active_admin.scss 에 width 설정 추가
  ```
div.title { width: 300px; }
  ```

# 결과

<img width="858" alt="Screen Shot 2023-03-06 at 15 38 33" src="https://user-images.githubusercontent.com/104121915/223036942-c7c68bda-7b32-4617-897b-ab397bd5953b.png">
