---
layout  : wiki
title   : algolia
summary : 
date    : 2023-03-24 19:01:33 +0900
updated : 2023-03-24 19:03:45
tag     : 
toc     : true
public  : true
parent  : /Users/hwieun/hwieun.github.io/_wiki
latex   : false
resource: 2DB68D19-2409-4C30-91DC-0FFC675974E1
---
* TOC
{:toc}

*검색 기능을 도입하면서 algolia를 사용하게 되어 indexing에 대해 궁금해져서 작성한 문서이다.*

- Index의 의미
    - DB에서 사용하는 index와 목적과 의미는 같다. 검색을 빠르게 하기 위해서 미리 만들어두는 데이터이다.
- Redix Tree (Trie의 나은 버전) 를 사용하여 인덱스를 만든다
    
    ![1](https://user-images.githubusercontent.com/29860102/227491215-b7ab673a-8e81-4ea8-a175-0cc2ba187143.png)

    - 인 메모리에서 구성 후 디스크로 덤프 뜨는 방법이 있지만 메모리 사용량을 적게 하기 위해 사용하는 트릭: 디스크에 다이렉트로 만드는 방법.
        
        > Here’s the trick: we can build this data-structure on-disk directly from the list of words and with only a few kilobytes of memory. To do that, we’ll flush from the RAM the nodes as soon as possible, by building the tree on the fly.
        > 
    - DFS로 만든다.
        ![2](https://user-images.githubusercontent.com/29860102/227491272-cca7bdad-de39-4bfe-a6d3-baa84f30202a.png)
        
        ![3](https://user-images.githubusercontent.com/29860102/227491288-782a282d-1f40-40e0-b03b-7ead144743da.png)
