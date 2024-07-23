---
title: Knuth算法
date: 2024-07-06 16:26:26
tags: 算法
category: 算法
mathjax: true
---

Knuth算法,又称洗牌算法或者费歇尔算法,可用于棋牌类游戏中



```sh
vector<int> ShuffleArray(vector<int> numbers)
{
	srand(time(0));
	for(int i=0;i<numbers.size();i++)
	{
		int tmp=numbers[i];
		int r=rand()%(numbers.size()-i+1)+i;
		numbers[i]=numbers[r];
		numbers[r]=tmp;
	}
	return numbers;
}
```

使用当前时间作为随机种子.具体步骤:

1. 取当前循环次数对应numbers中的值,放进tmp中
2. 对numbers之后的下标随机选取一个
3. 将当前循环次数对应的值与步骤2中的值交换
4. 循环步骤1,2,3直至到达numbers尾

如numbers={0,0,1,1,2,2,3,3}

i=1

tmp=0,r随机为6,则numbers={3,0,1,1,2,2,0,3}

i=2

tmp=0,r随机数为4,怎numbers={3,2,1,1,0,2,0,3}

以此类推最终得出一个随机序列