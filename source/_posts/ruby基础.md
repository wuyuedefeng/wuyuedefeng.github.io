title: ruby基础
author: wsen
tags:
  - ruby
categories:
  - ruby
date: 2017-03-23 19:42:00
---
### 闭包

#### use `block`
```
def foo
  a = 3
  yield a
end

foo {|b| puts b}
```

#### use `&` and `call`
```
def foo(&block)
  a = 2
  block.call(a)
end

foo {|a| puts a}
```

### proc
`proc`可以看做是`block`的对象表示，类名为`Proc`
```
proc = Proc.new {|x| x*2}
```

### lambda
lambda的class也是`Proc`
```
lambda = lambda {|x| x*2}
```
lambda和proc的区别是： 实例化方式的不同而已，`proc`更像对象，`lambda`更像方法

```
## diff1
p = Proc.new {|x, y| p x,y}
p.call(1)    # 1, nil
p.call(1,2)  # 1,2
p.cal(1,2,3) # 1,2

l = lambda {|x,y| p x,y}
l.call(1)     # err
l.call(1,2)   # 1,2
l.call(1,2,3) # err

## diff2
p = Proc.new {|x| return x}
p.call(1) # err

l = lambda {|x| return x}
p.call(1) # 1
```

### Class

#### 基础类示例
```
class Point
  attr_accessor :x  # getter setter
  attr_reader :y    # getter
  @@origin = 0
  ORIGIN = 2 # 常量    # 取值 Point::ORIGIN
  def initialize(x = 0, y = 0)
    # @x 实例变量
    # @@x 类变量
    # $x 全局变量
    # x 局部变量
    @x = x
    @y = y
  end

  # 实例方法
  def +(p2)
    Point.new(x+p2.x, y+p2.y)
  end

  # 类方法
  def self.foo
  end

  # 内部定义的都是类方法
  class << self
    def bar
    end
  end
end
```

#### 继承
* public 
>* visibility: `in/out`
>* inheritance: `Yes`
>* call an obj: `Yes`

* protected
>* visibility: `within`
>* inheritance: `Yes`
>* call an obj: `Yes`

* private
>* visibility: `within`
>* inheritance: `Yes`
>* call an obj: `No`

```
class Point3D < Point
  def initialize(x=0, y=0, z=0)
    # @x, @y, @z = x, y , z
    super(x, y)
    @z = z
  end
end
```

### module
* include 引入成实例方法
* extend  引入成类方法
```
module Helper
  #  instance method
  def test
  end
  # 类方法
  module ClassMethods
    def test2
    end
  end
  # hook klass为引入的类这里是 Point
  def self.included(klass)
    klass.extend ClassMethods
  end
end
# Point class
class Point
  include Helper
end
```
方法优先级：
`singleton中的方法` - `实例方法` - `module方法` - `父类实例方法`

### 异常处理
产生： raise
处理： rescue
```
def foo
  begin
    # raise "boom in foo"
    raise TypeError, "boom in foo", caller
  rescue => e
    puts e
  end
end

```

### 枚举和比较模块
Comparable
```
class People
  attr_reader :name
  include Comparable
  def initialize name
    @name = name
  end
  def <=> other
    self.name <=> other.name
  end
end
```
Enumerable
```
class People
  attr_reader :people
  include Enumerable
  def initialize people
    @people = people
  end
  def each
    raise "please provide a block" unless block_given?
    people.each do |person|
      yield person
    end
  end
end
```

### 正则表达式

生成正则的方法
* /ruby/
* %r{ruby}
* Regexp.new

使用
```
"ruby" =~ /ruby/  # 0
"ruby".match(/ru/) # ru
"ruby".gsub!(/r/, i) # iuby
```
[正则编写测试网站](http://rubular.com/)
```
[abc]	A single character of: a, b, or c
[^abc]	Any single character except: a, b, or c
[a-z]	Any single character in the range a-z
[a-zA-Z]	Any single character in the range a-z or A-Z
^	Start of line
$	End of line
\A	Start of string
\z	End of string
.	Any single character
\s	Any whitespace character
\S	Any non-whitespace character
\d	Any digit
\D	Any non-digit
\w	Any word character (letter, number, underscore)
\W	Any non-word character
\b	Any word boundary
(...)	Capture everything enclosed
(a|b)	a or b
a?	Zero or one of a
a*	Zero or more of a
a+	One or more of a
a{3}	Exactly 3 of a
a{3,}	3 or more of a
a{3,6}	Between 3 and 6 of a

options: 
i case insensitive 
m make dot match newlines 
x ignore whitespace in regex 
o perform #{...} substitutions only once
```
### 时间、日期
* DateTime < Date
* Time  # core library
```
# 设置环境变量time zone
ENV['TZ'] = 'Asia/Shanghai'
# 或者
Time.now.new_offset(Rational(8,24))
```
### 文件操作
[file-doc 2.2.0](http://ruby-doc.org/core-2.2.0/File.html)


### 多线程

#### 示例
```
def foo
  Thread.current['a'] = 1
  10.times do
    puts 'foo'
  end
end

def bar
  10.times do
    puts 'bar'
  end
end

t1 = Thread.new {foo()}
t2 = Thread.new {bar()}
t1.join
t2.join # 这里主线程等待t1,t2执行完成，才继续主线程执行，否则主线程停止，子线程将被释放

puts t1['a'] # 1
```
 Thread.new 创建线程
 
 t1.priority = -1
  
 t1.priority = 1 # 设置优先
 
 t1.join 开始执行t1线程,主线程等待t1完成
 
 Thread.current # 当前线程对象
 
  
 Thread.abort_on_exception = true # 一个线程异常，所有线程都退出
 
 #### mutex
 ```
 mutex = Mutex.new
 count1 = count2 = 0
 Thread.new do
	loop do
    	mutex.synchronize do # 类似ios的线程锁！
        	count1 += 1
            count2 += 1
        end
    end
 end
 sleep(1)
 mutex.lock
 ```
 
 ### 元编程（metaprogramming）
 #### eval
 ```
 eval "1 + 1"
 eval "def foo; puts 'foo'; end"
 ```
 #### instance_eval
 ```
 String.instance_eval "def foo; puts 'instance_eval foo'; end"
 String.foo #输出 instance_eval foo
 ```
 #### class_eval
 ```
 String.class_eval "def foo; puts 'class_eval foo'; end"
 str = 'abc'
 str.foo #输出： class_eval foo
 ```
 #### define_method
 ```
 define_method(:foo) { puts 'abc' }
 # 带一个参数的方法定义
 define_method(:foo) {|arg| puts arg }
 ```
 ```
 def method_missing method_name
	self.class.send(:define_method, method_name){
		puts method_name
    }
 end
 ```
 
 ### 编写gem
 
 1. 封装class module
 2. 讲文件放到lib test bin等目录中
 3. 编写gemspec文件
 4. gem build gemspec_file
 
 ### 发布gem
 1. rubygems.org注册账号
 2. 设置账号与电脑绑定
 3. gem push gem_name
 










