---
layout  : wiki
title   : 단방향 암호화 algorithm
summary : 
date    : 2023-02-27 09:57:56 +0900
updated : 2023-03-05 14:23:20
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
resource: AD2D6F65-0696-4642-9740-AB7282FAA9B6
---
* TOC
{:toc}

# 단방향 암호화란?

* 암호화된 데이터를 다시 복호화할 수 없다.
* 보통 데이터가 변조되지 않았음을 확인할 때 즉 데이터 무결성을 검증하기 위해 사용한다.
* 대표적으로 hash 알고리즘이 있다.

# Hash 알고리즘

임의의 크기를 가진 데이터를 고정된 크기의 데이터로 변환하는 함수.

### MD5 알고리즘(Message-Digest algorithm 5)

* 임의의 길이의 메시지를 받아 128 비트의 값을 출력한다.
* 입력 값의 길이 제한이 없다.
* 주로 파일이나 프로그램의 무결성 검사에 사용된다.
* 그 외 보안 관련 용도로는 권장하지 않는다.

### SHA 알고리즘 (Secure Hash Algorithm)

* 해시 값의 크기는 SHA 뒤에 붙는 bit 수가 된다.
* 버전은 0 ~ 3 이 있으며, 0과 1은 사용하지 않고, 2는 사용 가능하며 3이 권장되고 있다.
