---
layout  : wiki
title   : Trie
summary : 
date    : 2023-03-19 10:24:49 +0900
updated : 2023-03-19 11:21:56
tag     : 
toc     : true
public  : true
parent  : /Users/hwieun/hwieun.github.io/_wiki/algorithm
latex   : false
resource: D4E124B3-C1C9-49DA-BFB3-F990B28B6DA4
---
* TOC
{:toc}

# 개요

<img width="271" alt="Screen Shot 2023-03-19 at 10 54 14" src="https://user-images.githubusercontent.com/29860102/226149462-49f43ab7-6a31-499e-9e54-86459330136b.png">


탐색 트리의 일종이며 주로 단어 검색할 때 많이 사용되는 자료구조이다. 
트리의 노드는 노드 자체와 연관된 키 값은 가지고 있지 않다. 대신 노드가 트리에서 차지하는 위치가 키를 의미한다. 즉 문자 하나가 노드 하나에 대응한다. 
문자열이 키인 경우가 흔하지만, 꼭 그렇지는 않다. 

# Time Complexity

삽입과 검색 모두 키(단어)의 길이 m에 비례한다. **O(m)**


# code
다음은 Trie의 add와 search 메서드를 구현한 코드이다.

### cpp

```cpp
class Node {
public:
  bool isEndNode;
  Node* next[26];
  
  Node() {
    isEndNode = false;
    for(int i = 0; i < 26; i++)
      next[i] = nullptr;
  }
};

class Trie {
  Node* root;
  
public:
  Trie() {
    root = new Node();
  }
  
  void add(string word) {
    Node* curr = root;
    for(char c : word) {
      if(curr->next[c - 'a'] == nullptr)
        curr->next[c - 'a'] = new Node();
      curr = curr->next[c - 'a'];
    }
    curr->isEndNode = true;
  }

  bool search(string word) {
    return _search(root, word, 0);
  }

private:
  bool _search(Node* curr, string word, int i) {
    if(!curr) return false;
    if(word.length() == i) return curr->isEndNode;
    
    if(word[i] == '.') {
      for(int j = 0; j < 26; j++)
        if(_search(curr->next[j], word, i+1)) return true;
    }
    else if(_search(curr->next[word[i] - 'a'], word, i+1)) return true;
    
    return false;
  }
};
```

### java

```java

public class Trie {
    Node root;

    Trie() {
        root = new Node();
    }

    public void add(String word) {
        Node curr = root;
        for (int i = 0; i < word.length(); i++) {
            if (curr.next[word.indexOf(i) - 'a'] == null) curr.next[word.indexOf(i) - 'a'] = new Node();
            curr = curr.next[word.indexOf(i) - 'a'];
        }
        curr.isEndNode = true;
    }

    public boolean search(String word) {
        return searchHelper(root, word, 0);
    }

    private boolean searchHelper(Node curr, String word, int i) {
        if (curr == null) return false;
        if (word.length() == i) return curr.isEndNode;

        if (word.indexOf(i) == '.') {
            for (int j = 0; j < 26; j++) {
                if (searchHelper(curr.next[j], word, i+1)) return true;
            }
        }
        else if (searchHelper(curr.next[word.indexOf(i) - 'a'], word, i + 1)) return true;
        return false;
    }
}

class Node {
    boolean isEndNode;
    Node[] next = new Node[26];

    public Node() {
        isEndNode = false;
        for (int i = 0; i < 26; i++) next[i] = null;
    }
}

```

### python

```python
class Node:
    def __init__(self):
        self.isEndNode = False
        self.next = {}


class Trie:
    def __init__(self):
        root = Node()

    def add(self, word):
        curr = self.root
        for c in word:
            curr = curr.next.setDefault(c, Node())
        curr.isEndNode = True

    def search(self, word) :
        def searchHelper(curr, index) :
            if len(word) == index:
                return curr.isEndNode

            if word[index] == '.':
                for n in curr.next.values():
                    if searchHelper(n, index + 1):
                        return True
            
            if word[index] in curr.next:
                return searchHelper(curr.next[word[index]], index + 1)
            return False
        return searchHelper(self.root, 0)

```

### 참조

- [위키피디아](https://ko.wikipedia.org/wiki/%ED%8A%B8%EB%9D%BC%EC%9D%B4_(%EC%BB%B4%ED%93%A8%ED%8C%85))
