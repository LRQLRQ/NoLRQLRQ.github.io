---
title: LeetCode第4题
tags: algorithm
---

2.27

#### 第4题题目要求如下：
给出两个已经排好序的数组，nums1和nums2，要求输出两个数组合并后的中位数。要求时间复杂度不能超过 log(m+n) (假设两个数组长度分别为m和n)
我的思路是：分别求出两个数组的中位数，合并后的中位数也一定在这两个中位数之间。就一步一步的逼近，当走过总长度的四分之一后，就达到了中位数。但是我被其中的奇数偶数困扰太多，越写情况越麻烦，最后筋疲力尽。

然后看讨论区，有个人写的比较简单的解：使用shift方法，依次将两个数组头部中比较小的元素弹出，只要弹出总长度一半的次数，剩下的就是中位数了。为了应对奇数偶数的问题，设置一个缓冲变量记录上一次的值，如果总长度是奇数则剩下的就是中位数；如果总长度是偶数则对当前变量与缓冲变量求和即可。
感觉其实我的思路和这个差不多，但是一开始想的太局限了，陷入细节，越来越麻烦。如果跳出去的话就能更好些一点。
今日份感慨：**算法===抽象，业务逻辑===具体**，算法就像诗，业务逻辑写的越多就像屎。

下面贴出代码：

```
var findMedianSortedArrays = function(nums1, nums2) {
    const totalLength = nums1.length + nums2.length;	//计算出总共的长度
    let mid = Math.floor(totalLength/2) + 1;			//算出中位数所在的位置，进行这么多次数
    let last = null, preLast = null;					//用两个变量作为缓冲，用于应对奇数偶数长度
    while (mid--) {		
        preLast = last;			//保存上次的值
        last = ((nums2.length === 0 || nums1[0] < nums2[0]) ? nums1 : nums2).shift();	//从两个数组头开始计算，把小的数字弹出去
    }
    console.log(totalLength % 2 ? last : (last + preLast)/2 );
    return totalLength % 2 ? last : (last + preLast)/2 		//如果有奇数个数字，则返回当前的值；如果有偶数个数字，则返回当前与上次值的均值
};

```


鞭策一下自己吧
然后是我的屎：
```
var findMedianSortedArrays = function(nums1, nums2) {
    var start1 = parseInt((nums1.length-1)/2);
    var start2 = parseInt((nums2.length-1)/2);
    var step1 = 0;
    var step2 = 0;
    var length = nums1.length + nums2.length;

    while(nums1[start1] < nums2[start2] && (step1+step2)<(parseInt(length/4+1) )){
        if(start1<nums1.length-1){
            start1++;
            step1++;
        }
        if(start2>0){
            start2--;
            step2++;
        }
    }
    while(nums1[start1] > nums2[start2] && (step1+step2)<(parseInt(length/4+1))){
        if(start1>0 ){
            start1--;
            step1++;
        }
        if(start2<nums2.length-1){
            start2++;
            step2++;
        }
    }
    if(nums1[start1] < nums2[start2]){
       if(length%2===1){   //如果总共是有奇数个，且数组1中位数小于数组2
            console.log(step1>step2?nums1[start1]:nums2[start2]);
            return step1>step2?nums1[start1]:nums2[start2];
        } else{             //如果总数是有偶数个
            if(step1>step2){
                console.log((nums1[start1]+nums1[start1-1])/2);
                return (nums1[start1]+nums1[start1-1])/2;
            } else if(step1===step2){
                console.log((nums1[step1]+nums2[step2])/2);
                return (nums1[step1]+nums2[step2])/2;
            } else{
                console.log((nums2[start2]+nums2[start2-1])/2);
                return (nums2[start2]+nums2[start2-1])/2;
            }
        }
    }

    if(nums1[start1] > nums2[start2]){
        if(length%2===1 ){   //如果总共是有奇数个，且数组1中位数大于数组2
            console.log(step1>step2?nums1[start1]:nums2[start2]);
            return step1>step2?nums1[start1]:nums2[start2];
        } else{             //如果总数是有偶数个
            if(step1>step2){
                console.log((nums1[start1]+nums1[start1-1])/2);
                return (nums1[start1]+nums1[start1+1])/2;
            } else if(step1===step2){
                console.log((nums1[step1]+nums2[step2])/2);
                return (nums1[step1]+nums2[step2])/2;
            } else{
                console.log((nums2[start2]+nums2[start2-1])/2);
                return (nums2[start2]+nums2[start2+1])/2;
            }
        }
    }
};

```



