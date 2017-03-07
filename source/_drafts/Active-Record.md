title: Active Record
author: wsen
tags:
  - ''
  - rails
categories:
  - ruby on rails
  - ''
date: 2017-03-05 21:22:00
---
### 数据类型
rails中在mysql，postgresql， sqlite中的类型对照表
[参考](http://www.packtpub.com/article/Working-with-Rails-ActiveRecord-Migrations-Models-Scaffolding-and-Database-Completion)
<table>
<tr>
<td width="86" height="17"><strong><span>Rails</span></strong></td>
<td width="86"><strong><span >mysql</span></strong></td>
<td width="86"><strong><span >postgresql</span></strong></td>
<td width="86"><strong><span >sqlite</span></strong></td>
</tr>
<tr>
<td height="17">:binary</td>
<td>blob</td>
<td>bytea</td>
<td>blob</td>
</tr>
<tr>
<td height="17">:boolean</td>
<td>tinyint(1)</td>
<td>boolean</td>
<td>boolean</td>
</tr>
<tr>
<td height="17">:date</td>
<td>date</td>
<td>date</td>
<td>date</td>
</tr>
<tr>
<td height="17">:datetime</td>
<td>datetime</td>
<td>timestamp</td>
<td>datetime</td>
</tr>
<tr>
<td height="17">:decimal</td>
<td>decimal</td>
<td>decimal</td>
<td>decimal</td>
</tr>
<tr>
<td height="17">:float</td>
<td>float</td>
<td>float</td>
<td>float</td>
</tr>
<tr>
<td height="17">:integer</td>
<td>int(11)</td>
<td>integer</td>
<td>integer</td>
</tr>
<tr>
<td height="17">:string</td>
<td>varchar(255)</td>
<td>*</td>
<td>varchar(255)</td>
</tr>
<tr>
<td height="17">:text</td>
<td>text</td>
<td>text</td>
<td>text</td>
</tr>
<tr>
<td height="17">:time</td>
<td>time</td>
<td>time</td>
<td>datetime</td>
</tr>
<tr>
<td height="17">:timestamp</td>
<td>datetime</td>
<td>timestamp</td>
<td>datetime</td>
</tr>
</table>

### 数据模型
#### 创建`users`表
```
$ rails g model User phone:string email:string
```
#### 修改数据表
假设数据库中的表名为`USER`，显式指定`User`模型关联`USER`数据表，负责默认表名为：`users`
```ruby
class User < ActiveRecord::Base
  self.table_name = "USER"
end
```


### 控制器
```
$ rails g controller users index new edit show 
```


### 迁移文件
执行迁移文件
```
$ bundle exec rake db:migrate
```

回滚迁移文件
```
$ bundle exec rake db:rollback
```


### [数据验证](http://guides.ruby-china.org/active_record_validations.html)

> `presence` [必须存在]
```
  validates :name, presence: true
  # 只在创建时候验证
  validates :name, presence: {on: :create}
  # 只在更新时验证
  validates :name, presence: {on: :update}
  # '自定义错误内容'
  validates :name, presence: {on: :create, message: '姓名不能为空'}
```

> `absence` [必须为空]
```
  validates :name, absence: true
```

> `length`
- maximum 最大长度 `length: {maximum: 255}`
- minimum 最小长度 `length: {minimum: 3}`
- in  范围 `length: {in: 3..20}`
- is 长度必须为多少 `length: {is: 5}`

> `confirmation` 确认性验证
```ruby
validates :password, confirmation: true  #password_confirmation[shouldn't nil]
```

> inclusion 包含性验证[province必须为北京或者上海]
```
validates :province, inclusion: {in: ['北京', '上海']}
```

> exclusion 不包含性验证[不在某个集合之内]

> format 正则匹配
```ruby
class User < ActiveRecord::Base
  EMAIL = /\A.+@.+\z/i # 常量 正则
  validates :email, presence: true, length: {maximum: 255}, format: {with: EMAIL}
end
```

> `uniqueness` 唯一
```
class User < ActiveRecord::Base
  validates :phone, uniqueness: true
end
```
#### 允许为空的唯一性验证 和 条件验证
```
class User < ActiveRecord::Base
  # 存在验证唯一，或者可以为空
  validates :phone, uniqueness: {allow_nil: true} # method1
  validates :phone, uniqueness: {allow_blank: true} # method2
  # 条件验证1[fun] 仅仅方法返回true才进行验证
  validates :phone, uniqueness: true, :if => :test?
  def test?
    false
  end 
  # 条件验证2[string] 用户名为空进行验证
  validates :phone, uniqueness: true, :if => "name.nil?"
  # 条件验证3[proc]
  validates :phone, uniqueness: true, :if => Proc.new {name.nil?}
  # 条件验证 test? 成立  才会内部代码块的验证
  with_options if: :test? do
    validates :name, presence: true
    validates :phone, presence: true
  end
  def test? do
    (1+1 == 2)
  end
end
```

> 自定义验证

- validates_with
```
class MyValidator < ActiveModel::Validator
   def validate(record)
      if record.name.nil?
	    record.errors[:name] << '用户名不能为空'
      end
      if record.phone.nil?
        record.errors[:name] << '手机号不能为空'
      end
   end
end
class User < ActiveRecord::Base
	validates_with MyValidator
end
```


- validates_each
```
class NamepresenceValidator < ActiveModel::EachValidator
	# 参数： 记录  属性 值
	def validate_each(record, attribute, value)
    	if vale.nil?
        	record.erros[attribute] << "用户名不能为空"
        end
    end
end
class User < ActiveRecord::Base
	validates :name, namepresence: true
end
```

#### 自定义验证方法

```
class User < ActiveRecord::Base
	validate :my_validator
    def my_validator
    	if name.nil?
        	errors[:name] << '姓名不能为空'
        end
    end
end
```











#### 判断数据是否合法
* valid?  判断是否合法
* invalid? 判断是否不合法

#### 跳过数据验证方法
* save(validate: false)
* update_all
* update_column
* update_columns
* update_attibute
* touch
* ...

### 回调

#### 可用的回调
创建对象回调
- before_validation
- after_validation
- before_save
- before_create
- after_create
- after_save

更新对象
- before_validation
- after_validation
- before_save
- before_update
- after_update
- after_save

销毁对象
- before_destroy
- after_dstroy

事务回调
- after_commit #事务提交成功后执行
- after_rollback #事务提交失败执行

#### 可以触发回调的方法
- create
- create!
- decrement!
- destroy
- destroy!
- destroy_all
- increment!
- save
- save!
- save(validate: false)
- toggle!
- update_attribute
- update
- update!
- valid?

#### 跳过回调
- decrement
- decrement_count
- delete
- delete_all
- increment
- increment_counter
- toggle
- touch
- update_column

#### 终止回调
before_* 
 - 返回false
 - 抛出异常

after_* 
- 抛出异常


#### 条件回调 [和验证的条件回调类似]
eg: 
```
class User < ActiveRecord::Base
	before_save :BeforeSave, if: :test?
    def test?
    	true
    end
    def BeforeSave 
    	puts 'before_save'
    end
end
```