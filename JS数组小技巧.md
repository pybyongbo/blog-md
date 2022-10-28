### JS数组相关面试题小技巧



#####  判断一个数组中所有元素是否都相同 (简单数据类型):



**方法一:**

数组的`every`方法:

```javascript
let arr1 = [1,2,3,4,5];
let arr2 = [10,10,10,10,10];
let allEqual = arr => arr.every(item=>item===arr[0]);

console.log(allEqual(arr1));
console.log(allEqual(arr2));

```



**方法二:**

使用`new Set`方法:

```javascrit
const fn = arr => new Set(arr).size === 1

console.log(fn(arr1));
console.log(fn(arr2));
```



#####  判断一个数组中是否有重复项:

```javascript
let arr = [1,10,2,20,3,4,5,2];
let judgeDuplicate = (arr) => {return new Set(arr).size!==arr.length}

console.log(judgeDuplicate(arr));// 如果返回true,则表示有重复项,如果返回false则表示没有


```

