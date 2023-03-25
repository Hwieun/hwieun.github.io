---
layout  : wiki
title   : rails update
summary : 
date    : 2023-03-24 18:52:51 +0900
updated : 2023-03-24 18:53:10
tag     : 
toc     : true
public  : true
parent  : [[_wiki/ruby]]
latex   : false
resource: 523B35F8-13D5-4683-81E9-5BE92D9A5705
---

- `update_attributes` tries to **validate** the record, calls **callbacks** and **saves**
- `update_attribute` doesn’t validate the record, calls callbacks and saves
- `update_column` doesn’t validate the record, doesn’t call callbacks, **doesn’t call save** method, though it does **update** record in the database. `updated_at/updated_on` are not updated.
- `update_columns` equivalent to `update_column`
- `update`  tries to **validate** the record, calls **callbacks** and **saves**
- `update!` receive just like `update` but calls `save!`, so an exception is raised if the record is invalid

- 참고
    - [https://stackoverflow.com/questions/14415857/rails-update-column-works-but-not-update-attributes](https://stackoverflow.com/questions/14415857/rails-update-column-works-but-not-update-attributes)
    - [https://api.rubyonrails.org/v6.0/classes/ActiveRecord/Persistence/ClassMethods.html#method-i-update](https://api.rubyonrails.org/v6.0/classes/ActiveRecord/Persistence/ClassMethods.html#method-i-update) 
