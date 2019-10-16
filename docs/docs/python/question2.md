# @staticmethod和@classmethod的区别

##### @staticmethod不需要表示自身对象的self和自身类的cls参数，就跟使用函数一样。
##### @classmethod也不需要self参数，但第一个参数需要是表示自身类的cls参数。

代码示例
```
class A(object):
    bar = 1
    def foo(self):
        print 'foo'
 
    @staticmethod
    def static_foo():
        print 'static_foo'
        print A.bar
 
    @classmethod
    def class_foo(cls):
        print 'class_foo'
        print cls.bar
        cls().foo()
 
A.static_foo()
A.class_foo()
```
