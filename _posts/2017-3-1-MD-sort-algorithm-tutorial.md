---
layout: post
title: 排序算法总结
categories: [数据结构与算法]
---

## 插入排序

### 插入排序

 ![](../image/sort/Insertion-sort-example-300px.gif)

一般来说，插入排序都采用in-place在数组上实现。具体**算法描述如下**：


1. **从第一个元素开始，该元素可以认为已经被排序**
2. **取出下一个元素，在已经排序的元素序列中从后向前扫描**
3. 如果该元素（已排序）大于新元素，将该元素移到下一位置
4. 重复步骤3，直到找到已排序的元素小于或者等于新元素的位置
5. 将新元素插入到该位置后
6. 重复步骤2~5
		
如果比较操作的代价比交换操作大的话，可以采用二分查找法来减少比较操作的数目。该算法可以认为是插入排序的一个变种，称为二分查找插入排序。
		
时间复杂度：平均情况$O(n^2)$最好形况$O(n)$最坏情况$O(n^2)$

空间复杂度：O(1)

{%highlight ruby%}

		代码：
		template<typename T>//插入排序
		void insert_sort(vector<T>& arr)
		{
		    for(int i = 1;i < arr.size()-1;i++)
		    {
		        int temp = arr[i];
		        for(int j = i - 1;j >= 0;j--)
		        {
		            if(arr[j] > temp)
		            {
		                arr[j + 1] = arr[j];
		                arr[j] = temp;
		            }
		            else
		                break;
		        }
		    }
		}

{%endhighlight%}

### 希尔排序

![以23, 10, 4, 1的步长序列进行希尔排序](../image/sort/Sorting_shellsort_anim.gif)
<center>以23, 10, 4, 1的步长序列进行希尔排序</center>


**算法思想**：希尔排序通过将比较的全部元素分为几个区域来提升插入排序的性能。这样可以让一个元素可以一次性的朝最终位置前进一大步。然后算法再取越来越小的步长进行排序，算法的最后一步就是普通的插入排序，但是到了这一步，需要排序的数据几乎是已经排好的了。

**步长序列**：已知的最好步长序列是由Sedgewick提出的(1, 5, 19, 41, 109,...)，该序列的项来自 $9*4^i-9*2^i+1$和$2^{(i+2)}*(2^{(i+2)}-3)+1$这两个算式。

时间复杂度：平均情况：$O(n^{1.3})$最好情况：$O(n)$最坏情况：$O(n^2)$
		
空间复杂度：O(1)

{%highlight ruby%}

		代码：
		template<typename T>
		void shell_sort(T array[], int length) {
		    int h = 1;
		    while (h < length / 3) {
		        h = 3 * h + 1;
		    }
		    while (h >= 1) {
		        for (int i = h; i < length; i++) {
		            for (int j = i; j >= h && array[j] < array[j - h]; j -= h) {
		                std::swap(array[j], array[j - h]);
		            }
		        }
		        h = h / 3;	
		    }
		}

{%endhighlight%}

## 选择排序

### 选择排序（类似于插入排序）

 ![](../image/sort/Selection-Sort-Animation.gif)

<center>选择排序的示例动画。红色表示当前最小值，黄色表示已排序序列，蓝色表示当前位置。</center>

**选择排序（Selection sort）**:是一种简单直观的排序算法。它的**工作原理如下**。

1. **首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置**
2. 然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
3. 以此类推，直到所有元素均排序完毕。
	
时间复杂度：平均情况$O(n^2)$最好形况$O(n^2)$最坏情况$O(n^2)$

空间复杂度：O(1)

{%highlight ruby%}

		代码：
		template<typename T> //整數或浮點數皆可使用，若要使用物件（class）時必須設定大於（>）的運算子功能
		void selection_sort(std::vector<T>& arr) {
			for (int i = 0; i < arr.size() - 1; i++) {
				int min = i;
				for (int j = i + 1; j < arr.size(); j++)
					if (arr[j] < arr[min])
						min = j;
				std::swap(arr[i], arr[min]);
			}
		}

{%endhighlight%}


### 堆排序（注意查看代码，不会）
		
https://blog.csdn.net/u010452388/article/details/81283998
https://www.cnblogs.com/onepixel/p/7674659.html
https://zh.wikipedia.org/wiki/%E5%A0%86%E6%8E%92%E5%BA%8F

 ![](../image/sort/heap_sort_anim.gif)

<center>[图的引用地址](https://www.cnblogs.com/onepixel/p/7674659.html)</center>
	
	相关操作：
		堆的操作
		在堆的数据结构中，堆中的最大值总是位于根节点(在优先队列中使用堆的话堆中的最小值位于根节点)。堆中定义以下几种操作：
		
		最大堆调整（Max_Heapify）：将堆的末端子节点作调整，使得子节点永远小于父节点
		创建最大堆（Build_Max_Heap）：将堆所有数据重新排序
		堆排序（HeapSort）：移除位在第一个数据的根节点，并做最大堆调整的递归运算

**算法描述:**

		时间复杂度：
		空间复杂度：
		代码：
		#include <iostream>
		#include <algorithm>
		using namespace std;
		
		void max_heapify(int arr[], int start, int end) {
			//建立父節點指標和子節點指標
			int dad = start;
			int son = dad * 2 + 1;
			while (son <= end) { //若子節點指標在範圍內才做比較
				if (son + 1 <= end && arr[son] < arr[son + 1]) //先比較兩個子節點大小，選擇最大的
					son++;
				if (arr[dad] > arr[son]) //如果父節點大於子節點代表調整完畢，直接跳出函數
					return;
				else { //否則交換父子內容再繼續子節點和孫節點比較
					swap(arr[dad], arr[son]);
					dad = son;
					son = dad * 2 + 1;
				}
			}
		}
		
		void heap_sort(int arr[], int len) {
			//初始化，i從最後一個父節點開始調整
			for (int i = len / 2 - 1; i >= 0; i--)
				max_heapify(arr, i, len - 1);
			//先將第一個元素和已经排好的元素前一位做交換，再從新調整(刚调整的元素之前的元素)，直到排序完畢
			for (int i = len - 1; i > 0; i--) {
				swap(arr[0], arr[i]);
				max_heapify(arr, 0, i - 1);
			}
		}
		
		int main() {
			int arr[] = { 3, 5, 3, 0, 8, 6, 1, 5, 8, 6, 2, 4, 9, 4, 7, 0, 1, 8, 9, 7, 3, 1, 2, 5, 9, 7, 4, 0, 2, 6 };
			int len = (int) sizeof(arr) / sizeof(*arr);
			heap_sort(arr, len);
			for (int i = 0; i < len; i++)
				cout << arr[i] << ' ';
			cout << endl;
			return 0;
		}


## 比较排序

### 冒泡排序
**冒泡排序（英语：Bubble Sort）**：是一种简单的排序算法。它重复地走访过要排序的数列，一次比较两个元素，如果他们的顺序错误就把他们交换过来。走访数列的工作是重复地进行直到没有再需要交换，也就是说该数列已经排序完成。这个算法的名字由来是因为越小的元素会经由交换慢慢“浮”到数列的顶端。
		
**冒泡排序算法的运作如下**：

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

时间复杂度：平均复杂度$O(n^2)$最好情况$O(n)$最坏情况$O(n^2)$
		
空间复杂度：O(1)

{%highlight ruby%}

		代码：
		void bubble_sort(int arr[], int len) {
		int i, j, temp;
		for (i = 0; i < len - 1; i++)
			for (j = 0; j < len - 1 - i; j++)
				if (arr[j] > arr[j + 1]) {
					temp = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1] = temp;
				}
		}

{%endhighlight%}


### **快速排序**（最常用）

#### 步骤为：
		从数列中挑出一个元素，称为"基准"（pivot），
		重新排序数列，所有比基准值小的元素摆放在基准前面，所有比基准值大的元素摆在基准后面（相同的数可以到任何一边）。在这个分区结束之后，该基准就处于数列的中间位置。这个称为分区（partition）操作。
		递归地（recursively）把小于基准值元素的子数列和大于基准值元素的子数列排序。
		递归到最底部时，数列的大小是零或一，也就是已经排序好了。这个算法一定会结束，因为在每次的迭代（iteration）中，它至少会把一个元素摆到它最后的位置去。
		
#### 优化的排序算法:
		快速排序是二叉查找树（二叉查找树）的一个空间最优化版本。不是循序地把数据项插入到一个明确的树中，而是由快速排序组织这些数据项到一个由递归调用所隐含的树中。这两个算法完全地产生相同的比较次数，但是顺序不同。对于排序算法的稳定性指标，原地分区版本的快速排序算法是不稳定的。其他变种是可以通过牺牲性能和空间来维护稳定性的。
		
		快速排序的最直接竞争者是堆排序（Heapsort）。堆排序通常比快速排序稍微慢，但是最坏情况的运行时间总是 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}。快速排序是经常比较快，除了introsort变化版本外，仍然有最坏情况性能的机会。如果事先知道堆排序将会是需要使用的，那么直接地使用堆排序比等待introsort再切换到它还要快。堆排序也拥有重要的特点，仅使用固定额外的空间（堆排序是原地排序），而即使是最佳的快速排序变化版本也需要 {\displaystyle \Theta (\log n)} {\displaystyle \Theta (\log n)}的空间。然而，堆排序需要有效率的随机存取才能变成可行。
		
		快速排序也与归并排序（Mergesort）竞争，这是另外一种递归排序算法，但有坏情况 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}运行时间的优势。不像快速排序或堆排序，归并排序是一个稳定排序，且可以轻易地被采用在链表（linked list）和存储在慢速访问媒体上像是磁盘存储或网络连接存储的非常巨大数列。尽管快速排序可以被重新改写使用在链串列上，但是它通常会因为无法随机存取而导致差的基准选择。归并排序的主要缺点，是在最佳情况下需要 {\displaystyle \Omega (n)} {\displaystyle \Omega (n)}额外的空间
		正规分析:
		从一开始快速排序平均需要花费 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}时间的描述并不明显。但是不难观察到的是分区运算，数组的元素都会在每次循环中走访过一次，使用 {\displaystyle O(n)} {\displaystyle O(n)}的时间。在使用结合（concatenation）的版本中，这项运算也是 {\displaystyle O(n)} {\displaystyle O(n)}。
		
		在最好的情况，每次我们运行一次分区，我们会把一个数列分为两个几近相等的片段。这个意思就是每次递归调用处理一半大小的数列。因此，在到达大小为一的数列前，我们只要作 {\displaystyle \log n} \log n次嵌套的调用。这个意思就是调用树的深度是 {\displaystyle O(\log n)} {\displaystyle O(\log n)}。但是在同一层次结构的两个程序调用中，不会处理到原来数列的相同部分；因此，程序调用的每一层次结构总共全部仅需要 {\displaystyle O(n)} {\displaystyle O(n)}的时间（每个调用有某些共同的额外耗费，但是因为在每一层次结构仅仅只有 {\displaystyle O(n)} {\displaystyle O(n)}个调用，这些被归纳在 {\displaystyle O(n)} {\displaystyle O(n)}系数中）。结果是这个算法仅需使用 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}时间。
		
		另外一个方法是为 {\displaystyle T(n)} {\displaystyle T(n)}设立一个递归关系式，也就是需要排序大小为 {\displaystyle n} n的数列所需要的时间。在最好的情况下，因为一个单独的快速排序调用牵涉了 {\displaystyle O(n)} {\displaystyle O(n)}的工作，加上对 {\displaystyle n/2} {\displaystyle n/2}大小之数列的两个递归调用，这个关系式可以是：
		
		{\displaystyle T(n)=O(n)+2T(n/2)} {\displaystyle T(n)=O(n)+2T(n/2)}
		
		解决这种关系式类型的标准数学归纳法技巧告诉我们 {\displaystyle T(n)=O(n\log n)} {\displaystyle T(n)=O(n\log n)}。
		
		事实上，并不需要把数列如此精确地分区；即使如果每个基准值将元素分开为99%在一边和1%在另一边，调用的深度仍然限制在 {\displaystyle 100\log n} {\displaystyle 100\log n}，所以全部运行时间依然是 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}。
		
		然而，在最坏的情况是，两子数列拥有大各为 {\displaystyle 1} 1 和 {\displaystyle n-1} {\displaystyle n-1}，且调用树（call tree）变成为一个 {\displaystyle n} n个嵌套（nested）调用的线性连串（chain）。第 {\displaystyle i} i 次调用作了 {\displaystyle O(n-i)} {\displaystyle O(n-i)}的工作量，且 {\displaystyle \sum _{i=0}^{n}(n-i)=O(n^{2})} \sum _{i=0}^{n}(n-i)=O(n^{2})递归关系式为：
		
		{\displaystyle T(n)=O(n)+T(1)+T(n-1)=O(n)+T(n-1)} {\displaystyle T(n)=O(n)+T(1)+T(n-1)=O(n)+T(n-1)}
		
		这与插入排序和选择排序有相同的关系式，以及它被解为 {\displaystyle T(n)=O(n^{2})} {\displaystyle T(n)=O(n^{2})}。
#### 随机数快速排序的期望复杂度:
		随机数快速排序有一个值得注意的特性，在任意输入数据的状况下，它只需要 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}的期望时间。是什么让随机的基准变成一个好的选择？
		
		假设我们排序一个数列，然后把它分为四个部分。在中央的两个部分将会包含最好的基准值；他们的每一个至少都会比25%的元素大，且至少比25%的元素小。如果我们可以一致地从这两个中央的部分选出一个元素，在到达大小为1的数列前，我们可能最多仅需要把数列分区 {\displaystyle 2\log _{2}n} {\displaystyle 2\log _{2}n}次，产生一个 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}算法。
		
		不幸地，随机数选择只有一半的时间会从中间的部分选择。出人意外的事实是这样就已经足够好了。想像你正在翻转一枚硬币，一直翻转一直到有 {\displaystyle k}  k 次人头那面出现。尽管这需要很长的时间，平均来说只需要 {\displaystyle 2k} {\displaystyle 2k}次翻动。且在 {\displaystyle k}  k 次翻动中得不到 {\displaystyle k}  k 次人头那面的机会，是像天文数字一样的非常小。借由同样的论证，快速排序的递归平均只要 {\displaystyle 2(2\log _{2}n)} {\displaystyle 2(2\log _{2}n)}的调用深度就会终止。但是如果它的平均调用深度是 {\displaystyle O(\log n)} {\displaystyle O(\log n)}且每一阶的调用树状过程最多有 {\displaystyle n} n个元素，则全部完成的工作量平均上是乘积，也就是 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}。
		
#### 平均复杂度:
		即使如果我们无法随机地选择基准数值，对于它的输入之所有可能排列，快速排序仍然只需要 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}时间。因为这个平均是简单地将输入之所有可能排列的时间加总起来，除以 {\displaystyle n} n这个因数，相当于从输入之中选择一个随机的排列。当我们这样作，基准值本质上就是随机的，导致这个算法与随机数快速排序有一样的运行时间。
		
		更精确地说，对于输入顺序之所有排列情形的平均比较次数，可以借由解出这个递归关系式可以精确地算出来。
		
		{\displaystyle C(n)=n-1+{\frac {1}{n}}\sum _{i=0}^{n-1}(C(i)+C(n-i-1))=2n\ln n=1.39n\log _{2}n} C(n)=n-1+{\frac {1}{n}}\sum _{i=0}^{n-1}(C(i)+C(n-i-1))=2n\ln n=1.39n\log _{2}n。
		
		在这里， {\displaystyle n-1} {\displaystyle n-1}是分区所使用的比较次数。因为基准值是相当均匀地落在排列好的数列次序之任何地方，总和就是所有可能分区的平均。
		
		这个意思是，平均上快速排序比理想的比较次数，也就是最好情况下，只大约比较糟39%。这意味着，它比最坏情况较接近最好情况。这个快速的平均运行时间，是快速排序比其他排序算法有实际的优势之另一个原因。
		
#### 空间复杂度:
		被快速排序所使用的空间，依照使用的版本而定。使用原地（in-place）分区的快速排序版本，在任何递归调用前，仅会使用固定的額外空間。然而，如果需要产生 {\displaystyle O(\log n)} {\displaystyle O(\log n)}嵌套递归调用，它需要在他们每一个存储一个固定数量的信息。因为最好的情况最多需要 {\displaystyle O(\log n)} {\displaystyle O(\log n)}次的嵌套递归调用，所以它需要 {\displaystyle O(\log n)} {\displaystyle O(\log n)}的空间。最坏情况下需要 {\displaystyle O(n)} {\displaystyle O(n)}次嵌套递归调用，因此需要 {\displaystyle O(n)} {\displaystyle O(n)}的空间。
		
		然而我们在这里省略一些小的细节。如果我们考虑排序任意很长的数列，我们必须要记住我们的变量像是left和right，不再被认为是占据固定的空间；也需要 {\displaystyle O(\log n)} {\displaystyle O(\log n)}对原来一个 {\displaystyle n} n项的数列作索引。因为我们在每一个堆栈框架中都有像这些的变量，实际上快速排序在最好跟平均的情况下，需要 {\displaystyle O(\log _{2}n)} {\displaystyle O(\log _{2}n)}空间的比特数，以及最坏情况下 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}的空间。然而，这并不会太可怕，因为如果一个数列大部分都是不同的元素，那么数列本身也会占据 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}的空间字节。
		
		非原地版本的快速排序，在它的任何递归调用前需要使用 {\displaystyle O(n)} {\displaystyle O(n)}空间。在最好的情况下，它的空间仍然限制在 {\displaystyle O(n)} {\displaystyle O(n)}，因为递归的每一阶中，使用与上一次所使用最多空间的一半，且
		
		{\displaystyle \sum _{i=0}^{\infty }{\frac {n}{2^{i}}}=2n} \sum _{i=0}^{\infty }{\frac {n}{2^{i}}}=2n。
		它的最坏情况是很恐怖的，需要
		
		{\displaystyle \sum _{i=0}^{n}(n-i+1)=\Theta (n^{2})} \sum _{i=0}^{n}(n-i+1)=\Theta (n^{2})
		空间，远比数列本身还多。如果这些数列元素本身自己不是固定的大小，这个问题会变得更大；举例来说，如果数列元素的大部分都是不同的，每一个将会需要大约 {\displaystyle O(\log n)} {\displaystyle O(\log n)}为原来存储，导致最好情况是 {\displaystyle O(n\log n)} {\displaystyle O(n\log n)}和最坏情况是 {\displaystyle O(n^{2}\log n)} {\displaystyle O(n^{2}\log n)}的空间需求。
#### 时间复杂度：平均复杂度 1.39nlog2n
		#空间复杂度：
		#代码：
		迭代法
		struct Range {
		    int start, end;
		    Range(int s = 0, int e = 0) {
		        start = s, end = e;
		    }
		};
		template <typename T> // 整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)、"大於"(>)、"不小於"(>=)的運算子功能
		void quick_sort(T arr[], const int len) {
		    if (len <= 0)
		        return; // 避免len等於負值時宣告堆疊陣列當機
		    // r[]模擬堆疊,p為數量,r[p++]為push,r[--p]為pop且取得元素
		    Range r[len];
		    int p = 0;
		    r[p++] = Range(0, len - 1);
		    while (p) {
		        Range range = r[--p];
		        if (range.start >= range.end)
		            continue;
		        T mid = arr[range.end];
		        int left = range.start, right = range.end - 1;
		        while (left < right) {
		            while (arr[left] < mid && left < right) left++;
		            while (arr[right] >= mid && left < right) right--;
		            std::swap(arr[left], arr[right]);
		        }
		        if (arr[left] >= arr[range.end])
		            std::swap(arr[left], arr[range.end]);
		        else
		            left++;
		        r[p++] = Range(range.start, left - 1);
		        r[p++] = Range(left + 1, range.end);
		    }
		}	
		递归法
		template <typename T>
		void quick_sort_recursive(T arr[], int start, int end) {
		    if (start >= end)
		        return;
		    T mid = arr[end];
		    int left = start, right = end - 1;
		    while (left < right) {
		        while (arr[left] < mid && left < right)
		            left++;
		        while (arr[right] >= mid && left < right)
		            right--;
		        std::swap(arr[left], arr[right]);
		    }
		    if (arr[left] >= arr[end])
		        std::swap(arr[left], arr[end]);
		    else
		        left++;
		    quick_sort_recursive(arr, start, left - 1);
		    quick_sort_recursive(arr, left + 1, end);
		}
		template <typename T> //整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)、"大於"(>)、"不小於"(>=)的運算子功能
		void quick_sort(T arr[], int len) {
		    quick_sort_recursive(arr, 0, len - 1);
		}
		#注意浙大老师的版本
		#迭代和递归的区别;
		迭代：for (int i=0; i < 100; i++) n = f(n);
		递归：function（n）{n = function(n-1)}
### 归并排序

<center>![](https://upload.wikimedia.org/wikipedia/commons/c/cc/Merge-sort-example-300px.gif)</center>

		递归法（Top-down）
		1申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列
		2设定两个指针，最初位置分别为两个已经排序序列的起始位置
		3比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置
		4重复步骤3直到某一指针到达序列尾
		5将另一序列剩下的所有元素直接复制到合并序列尾
		迭代法（Bottom-up）
		原理如下（假设序列共有 {\displaystyle n} n个元素）：
		1将序列每相邻两个数字进行归并操作，形成 {\displaystyle ceil(n/2)} {\displaystyle ceil(n/2)}个序列，排序后每个序列包含两/一个元素
		2若此时序列数不是1个则将上述序列再次归并，形成 {\displaystyle ceil(n/4)} {\displaystyle ceil(n/4)}个序列，每个序列包含四/三个元素
		3重复步骤2，直到所有元素排序完毕，即序列数为1
		时间复杂度：
		空间复杂度：O（n）
		#代码：
		迭代版：

		template<typename T> //整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)的運算子功能
		void merge_sort(T arr[], int len) {
			T* a = arr;
			T* b = new T[len];
			for (int seg = 1; seg < len; seg += seg) {
				for (int start = 0; start < len; start += seg + seg) {
					int low = start, mid = min(start + seg, len), high = min(start + seg + seg, len);
					int k = low;
					int start1 = low, end1 = mid;
					int start2 = mid, end2 = high;
					while (start1 < end1 && start2 < end2)
						b[k++] = a[start1] < a[start2] ? a[start1++] : a[start2++];
					while (start1 < end1)
						b[k++] = a[start1++];
					while (start2 < end2)
						b[k++] = a[start2++];
				}
				T* temp = a;
				a = b;
				b = temp;
			}
			if (a != arr) {
				for (int i = 0; i < len; i++)
					b[i] = a[i];
				b = a;
			}
			delete[] b;
		}
		递归版：
		
		template<typename T>
		void merge_sort_recursive(T arr[], T reg[], int start, int end) {
			if (start >= end)
				return;
			int len = end - start, mid = (len >> 1) + start;
			int start1 = start, end1 = mid;
			int start2 = mid + 1, end2 = end;
			merge_sort_recursive(arr, reg, start1, end1);
			merge_sort_recursive(arr, reg, start2, end2);
			int k = start;
			while (start1 <= end1 && start2 <= end2)
				reg[k++] = arr[start1] < arr[start2] ? arr[start1++] : arr[start2++];
			while (start1 <= end1)
				reg[k++] = arr[start1++];
			while (start2 <= end2)
				reg[k++] = arr[start2++];
			for (k = start; k <= end; k++)
				arr[k] = reg[k];
		}
		template<typename T> //整數或浮點數皆可使用,若要使用物件(class)時必須設定"小於"(<)的運算子功能
		void merge_sort(T arr[], const int len) {
			T reg = new T[len];
			merge_sort_recursive(arr, reg, 0, len - 1);
			delete[] reg;
		}



## 非比较排序（计数排序）
### 哈希表排序
### 桶排序(使用空间多，不是基于比较的方法)
### 基数排序
![](https://img-blog.csdn.net/20161009171515225)
##另外比较特殊的还有
### 表排序（这个是间接排序是为了防止多次移动待排数列造成时间花费过大）
