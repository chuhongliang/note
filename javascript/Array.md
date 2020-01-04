### 数组相关的常用方法

下面这些方法会改变数组自身的值：
	
1. ``Array.prototype.pop()`` 删除数组的最后一个元素，并返回这个元素
2. ``Array.prototype.push()`` 在数组的末尾增加一个或多个元素，并返回数组长度
3. ``Array.prototype.shift()`` 删除数组的第一个约束，并返回这个元素
4. ``Array.prototype.unshift()`` 在数组的开头增加一个或多个元素，并返回数组长度
5. ``Array.prototype.sort()`` 对数组进行排序，并返回当前数组。
6. ``Array.prototype.reverse()`` 颠倒数组中元素的排列顺序。
7. ``Array.prototype.splice()`` 在任意位置给数组添加或删除任意个元素。

下面这些方法不会改变数组自身的值，只会返回一个新的数组或者其他值：

1. ``Array.prototype.concat()`` 返回当前数组和其他若干数组或者若干个费数组值组合而成的新数组
2. ``Array.prototype.includes()`` 判断当前数组是否包含某指定的值。
3. ``Array.prototype.join()`` 连接所有数组元素组成一个字符串
4. ``Array.prototype.slice()`` 抽取当前数组中的一段元素组合成一个新数组
5. ``Array.prototype.toString()`` 返回由数组元素组成的字符串
6. ``Array.prototype.indexOf()`` 返回数组中第一个与指定值相等的元素的索引，如果找不到返回-1
7. ``Array.prototype.lastIndexOf()`` 返回数组中最后一个与指定值相等的元素的索引，如果找不到返回-1
   
遍历方法:

1. ``Array.prototype.forEach()`` 为数组中每一个元素执行一次回调。
2. ``Array.prototype.every()`` 如果数组中每个元素都满足测试函数，则返回true，否则返回false；
3. ``Array.prototype.some()`` 如果数组中至少有一个元素满足测试函数，则返回true，否则返回false
4. ``Array.prototype.filter()`` 将所有在过滤函数中返回true的数组元素放假一个新数组中并返回。
5. ``Array.prototype.find()`` 找到第一个满足测试函数的元素并返回那个元素的值，如果找不到，返回undefined
6. ``Array.prototype.map()`` 返回一个由回调函数的返回值组成的新数组
7. ``Array.prototype.reduce()`` 从左到右为每个元素执行一次回调函数，并把上次回调函数的返回值放在一个暂存器中传给下次回调函数，并返回最后一次回调函数的返回值。

---

### 数组去重

```
const arr = [1,2,3,4,4,3,2,1];
// 方法一：new Set ES6
return [...new Set(arr)]; // 注意...的用法

// 方法二：双层for循环 (这是一种方法，但是性能不好)
function unique(arr){
  var res=[];
  for (var i = 0; i < arr.length; i++) {
    for (var j = i+1; j < arr.length; j++) {
      if (arr[i] === arr[j]) {
        ++ i;
        j = i;
      }
    }
    res.push(arr[i]);
  }
  return res;
}

// 方法三：单层for循环 + indexOf
function unique(array){
    var res = [];
    for(var i = 0; i < array.length; i++) {
        //如果当前数组的第i项在当前数组中第一次出现的位置是i，才存入数组；否则代表是重复的
        if (array.indexOf(array[i]) === i) {
            res.push(array[i])
        }
    }
    return res;
}
//或者这样
function unique(array){
    let res = [];
    for(var i = 0; i < array.length; i++) {
        if (res.indexOf(array[i]) === -1) {
            res.push(array[i]);
        }
    }
    return res;
}

// 方法四：如果可以容忍改变原有数组的情况下，这样性能更好
function unique(array){
    // 注意这里一定要倒叙for循环，否则会出现问题
    for(var i = array.length - 1; i > 0; i--) { 
        if (array.indexOf(array[i]) !== i) {
            array.splice(i, 1);
        }
    }
    // 因为少声明一个变量，节省了内存空间
    return array;
}

```
---

### 扁平化数组

完成一个函数,接受数组作为参数,数组元素为整数或者数组,数组元素包含整数或数组,函数返回扁平化后的数组
	
如：[1, [2, [ [3, 4], 5], 6]] => [1, 2, 3, 4, 5, 6]

```
function flat(array, result = []) {
	for (let i = 0; i < array.length; i++) {
		const value = array[i];
		if (Array.isArray(value)) {
			flat(value, result);
		} else {
			result.push(value);
		}
	}
	return result;
}
```

```
let arr = [1, 2, [3, 4, 5, [6, 7], 8], 9, 10, [11, [12, 13]]]

const flatten = function (arr) {
    while (arr.some(item => Array.isArray(item))) {
        arr = [].concat(...arr)
    }
    return arr
}

console.log(flatten(arr))
```

```
const flattenDeep = (arr) => Array.isArray(arr)
? arr.reduce( (a, b) => [...a, ...flattenDeep(b)] , [])
: [arr]

flattenDeep([1, [[2], [3, [4]], 5]])
```

一下方法扁平化数组的同时去重并排序：
```
Array.from(new Set(arr.toString().split(',').sort((a,b)=>{return a-b;}).map(Number)));
```

```
Array.from(new Set(arr.flat(Infinity))).sort((a,b)=>{return a-b});
```
