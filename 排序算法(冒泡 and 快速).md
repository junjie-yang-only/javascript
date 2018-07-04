#### 冒泡排序

1. 思想：每一次对比相邻两个数据的大小，小的排在前面，如果前面的数据比后面的大就交换这两个数的位置。

要实现上述规则需要用到两层 for 循环，外层从第一个数到倒数第二个数，内层从外层的后面一个数到最后一个数。

2. 特点：排序算法的基础，简单实用易于理解，缺点是比较次数多，效率低。

3. 实现

```javascript
var times = 0;
var bubbleSort = function(arr){
    for(var i = 0,len = arr.length - 1; i < len; i++){
        for(var j = i + 1; j < arr.length; j++){
            if(arr[i] > arr[j]){
                var temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp
            }
            console.log(++times)
        }
    }
    return arr
}
```

#### 快速排序

1. 思想：先找到一个基准点（一般指数组的中部），然后数组被该基准点分为两部分，依次与该基准点比较，如果比它小，放左边，反之，放右边。左右分别用一个空数组去存储比较后的数据。最后执行递归操作，直到数组长度 <= 1；

2. 特点：快速，常用，缺点是需要另外声明两个数组，浪费了内存空间资源。

3. 实现

```javascript
var times = 0;
var quickSort = function(arr){
    // 如果数组长度小于等于 1 无需判断直接返回即可
    if(arr.length < 1){
        return arr
    }
    var maxIndex = Math.floor(arr.length / 2);  // 获取基准点
    var midIndexVal = arr.splice(maxIndex, 1);  // 获取基准点的值
    var left = [];
    var right = [];
    for(var i = 0, len = arr.length; i < len; i++){
        if(arr[i] < midIndexVal){
            left.push(arr[i])
        }else{
            right.push(arr[i])
        }
    }
    return quickSort(left).contact(midIndexVal, quickSort(right))
}
```


```javascript
Math.floor(5.55); //向下取整 结果为5 
Math.floor(5.99); //向下取整 结果为5 
Math.ceil(5.21);//向上取整，结果为6 
Math.ceil(5.88);//向上取整，结果为6 
Math.round(5.78);//四舍五入 结果为6 
Math.round(5.33);//结果为5 
```