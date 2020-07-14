---
layout: post
title: 查找
categories: [数据结构与算法]
---

## 二分查找

* 算法描述：一般情况下，二分查找是最长用的能将O(N)时间复杂度减低到O(logn),而且最长用的也是while式的查找。

* 二分法最常用的函数样式：

{% highlight c++ %}
int binary_search(vector<int>&vec, int target){
		int i = 0, j = vec.size() - 1;
		int mid = -1;
		while(i<=j){
			mid = (i+j)/2;
			if(vec[mid] < target){
				i = mid + 1;
			}else if(vec[mid] == target){
				return mid;
			}else{
				j = mid - 1;
			}
		}
		return -1;
	}
{% endhighlight %}

* 可能存在的bug:

mid = (i+j)/2这一句在i和j过大时会发生溢出，所以最好写成mid = i+(j-i)/2;这样不会造成溢出。

* 另外还需要注意的细节：

    * 二分搜索对一个有重复元素的数组进行搜索
    * 二分搜索在搜索递增序列的时候，迭代条件应该是:left = left+(right-left)/2;
    * 二分搜索在搜索递减序列的时候，迭代条件应该是：left = left+(right-left+1)/2想想为什么？
    * 终止条件和更新迭代区间代码的配合:
        while(left<=right) 应该与  left = mid + 1; right = mid-1配合使用



### [力扣-33-搜索旋转排序数组](https://leetcode-cn.com/problems/search-in-rotated-sorted-array/)

思路：在进行划分时，一定有一部分是有序的，而另一部分是无序的所以分两种情况处理左右两边

### [力扣-34-在排序数组中查找元素的第一个和最后一个位置](https://leetcode-cn.com/problems/find-first-and-last-position-of-element-in-sorted-array/)

### [力扣-1095-山脉数组中查找目标值](https://leetcode-cn.com/problems/find-in-mountain-array/)

这是一个交互式的查找问题，访问提供的函数的次数受限，因此必须使用二分查找法来降低复杂度。