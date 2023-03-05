---
layout  : wiki
title   : vimwiki 사용 팁
summary : 
date    : 2023-02-22 21:55:43 +0900
updated : 2023-03-05 14:22:57
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
resource: 532DAAE4-4AC7-475E-AF7D-E274E699B585
---
* TOC
{:toc}


## command
d # 디렉토리 생성


## command in file (prefix :)
- :edit [file name]  => 파일 생성 혹은 수정
- :b# => go back to the previously editted buffer
- :e# => go back to the previously editted file
- Ctrl + O => jump back to the previous(older) location, not necessarily buffer
- Ctrl + w then q => split window close
- Ctrl + Space => 체크박스 생성, 한 번 더 누르면 체크박스 채워짐

# vim -> clipboard 복사하기

1. vim에서 v를 눌러서 visual 모드로 진입
2. 복사할 영역을 드래그
3. "+y 입력

반대로 clipboard -> vim은 "+p 입력

# vim 화면 세로 split

ctrl + w + v
