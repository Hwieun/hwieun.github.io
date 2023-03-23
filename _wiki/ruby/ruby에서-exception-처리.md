---
layout  : wiki
title   : ruby에서 exception 처리
summary : begin-rescue-else-ensure-end 
date    : 2023-03-23 18:01:40 +0900
updated : 2023-03-23 18:02:34
tag     : 
toc     : true
public  : true
parent  : [[_wiki/ruby]]
latex   : false
resource: 18C18A36-19AC-476B-BD88-26E4E298A8A3
---
* TOC
{:toc}


```ruby
begin
  # 일반적인 코드 영역. exception 발생 시 rescue 로 넘어감
rescue A_ExceptionClass => a_var
  # A Exception 발생. 처리 
rescue B_ExceptionClass => b_var
  # B Exception 발생. 처리
else
  # Exception이 raise 되지 않은 경우
ensure
  # Exception 발생 여부와 상관없이 무조건 실행됨
end
```
