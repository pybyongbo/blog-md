
### javascript中Function.prototype是对象还是函数?

即是对象又是函数.

#### `prototype`和`__proto__`的关系是什么

先说结论:

1. `prototype`用于访问函数的原型对象.

2. `__proto__` 用于访问对象实例的原型对象（或者使用 `Object.getPrototypeOf()`）。

```

function Test(){}

const test = new Test();
test.__proto__ == Test.prototype;  // true
```

也就是说,函数拥有`prototype` 属性,对象实例拥有`__proto__`属性,它们都是用来访问原型对象的.

函数有点特别,它不仅是个函数,还是个对象.所以它也有`__proto__`属性.

为什么会这样呢?因为函数是内置构造函数`Function`的实例:

```
    
    const test= new Function('function test(){}');
    
    test.__proto__ == Function.prototype // true
```

所以函数能通过`__proto__` 访问它的原型对象.

由于`prototype`是一个对象,所以它也可以通过`__proto__`访问它的原型对象.对象的原型对象,那自然就是`Object.prototype`了.

```
    
    function Test(){};
    
    Test.prototype.__proto__ == Object.prototype  // true
```

这样看起来好像有点复杂,我们可以换个角度来看.`Object`其实也是内置构造函数:

```
    
    const obj = new Object() // 我们可以把这个obj想象成原型对象 prototype
    obj.__proto__ == Object.prototype // true 换个角度来看，相当于 prototype.__proto__ == Object.prototype
```

从这一点来看,是不是更好理解一点.

为了防止无休止的循环下去,所以`Object.prototype.__proto__`是指向`null`的,`null`是万物的终点.

```

Object.prototype.__proto__ == null // true
```

既然`null`是万物的终点,那么使用`Object.create(null)`创建的对象是没有`__proto__`属性的,也没有`prototype`属性.