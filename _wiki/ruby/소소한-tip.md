---
layout  : wiki
title   : 소소한 tip
summary : ruby 소소한 tip 
date    : 2023-03-23 18:28:54 +0900
updated : 2023-03-24 19:00:59
tag     : 
toc     : true
public  : true
parent  : [[_wiki/ruby]]
latex   : false
resource: 13000D90-1ED3-4D10-B7C0-FE0A4EA8B78A
---
* TOC
{:toc}

- the simple way if check value is null
  - object**&.**spouse&.name == [object.spouse.name](http://object.spouse.name) if object && object.spouse
  - `class << self`
    1. Method defined within `class << obj` block will be added to the `obj` object.
    2. `obj` can be any object.

- hash array sort by multiple condition

 ```ruby
  arr = [{"count"=>2, "hit"=>2, "name"=>"1"},
    {"count"=>2, "hit"=>1, "name"=>"1"},
    {"count"=>1, "hit"=>5, "name"=>"1"}
  ]

  # sort
  sorted_arr = arr.sort_by {|data| [data["count"], data["hit"]]}

 ```

- 결과 
 ```ruby
  [{"count"=>1, "hit"=>5, "name"=>"1"}, {"count"=>2, "hit"=>1, "name"=>"1"}, {"count"=>2, "hit"=>2, "name"=>"1"}]
 ```

- it's different type

```ruby
hash = {"count"=>2, "hit"=>2, "name"=>"1"}

hash.keys[0].class # => String

hash = {:count=>2, :hit=>2, :name=>"1"}

hash.keys[0].class # => Symbol
```

- console에서 module reload
    - `load "#{Rails.root}/lib/yourfile.rb"`
- module 도 변수를 가질 수 있다. 하지만 static member 와 유사
- hash 를 다른 hash 로 만들 때 `hash.inject({}) |m,e|` 와 같이 사용
- byebug
    - rake 든 rails 에서 실행하든 byebug 가 있으면 걸림
    - c : 다음 byebug 까지 실행
    - step: 한 줄씩 실행
- is it good style to explicitly return in Ruby
    - 굳이 return 을 빼서 명료하지 않게 할 이유는 없음. return 을 명시해주는게 다른 개발자가 이해하기에도 좋음
    
- find_by vs where
    - where 로 하면 string 으로 조건절 작성. 마지막에 take 를 하거나 each do end 문을 돌려야 함
    - find_by 는 하나만 가져옴
- class vs module
  ![Screen Shot 2023-01-30 at 11 46 15](https://user-images.githubusercontent.com/29860102/227164595-73ee5b20-e760-4b3c-acb3-270c24ea66d0.png)
 - [https://stackoverflow.com/questions/151505/difference-between-a-class-and-a-module](https://stackoverflow.com/questions/151505/difference-between-a-class-and-a-module)

- ruby hash array sort
  - [https://stackoverflow.com/questions/3154111/how-do-i-sort-an-array-of-hashes-by-a-value-in-the-hash](https://stackoverflow.com/questions/3154111/how-do-i-sort-an-array-of-hashes-by-a-value-in-the-hash)
  - hash = {"five" => 5, "ten" => 10}
    - hash.keys[0].class // String
  - hash = {:five => 5, :ten => 10}
    - hash.keys[0].class // Symbol
