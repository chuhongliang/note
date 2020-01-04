
### Object相关的常用方法

1. ``Object.assign()`` 通过复制一个或多个对象来创建一个新对象
2. ``Object.create()`` 使用指定的原型对象和属性创建一个新对象
3. ``Object.defineProperty()`` 给对象添加一个属性并指定该属性的配置
4. ``Object.keys()`` 返回一个包含所有给定对象自身可枚举属性名称的数组
5. ``Object.values()`` 返回给定对象自身可枚举值的数组
   
---

### defineProperty, hasOwnProperty, propertyIsEnumerable都是做什么用的？

``Object.defineProperty(obj, prop, descriptor)``用来给对象定义属性,有``value``,``writable``,``configurable``,``enumerable``,``set/get``等.``hasOwnProerty``用于检查某一属性是不是存在于对象本身，继承来的父亲的属性不算．``propertyIsEnumerable``用来检测某一属性是否可遍历，也就是能不能用``for..in``循环来取到.