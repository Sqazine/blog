---
title: GamePhysicsInOneWeekend V1.07 个人翻译
date: 2024-07-22 17:10:47
tags: 仿真
category: 仿真 
mathjax: true
---

# 1. 总览
本系列书籍受Peter Shirley相似名字的书籍"Ray Tracing In One Weekend"启发.他的书可以让没有任何图形编程知识的人都可以自制一个光线追踪器.

　　读完他的书之后,我想对于学科应该尽可能有更多相似系列的书籍.总之,在开始学习某个学科时,最困难的部分经常是弄清楚如何开始.

　　甚至,我有一个做一点微小贡献的想法.所以这本书就是我的结果.现在,这本书并不是让你成为一个游戏刚体物理方面的专家,它旨在成为该学科的一个大出发点.并且如果你完成了本系列,则在最后你会明白连续碰撞检测,约束,汽车,布娃娃系统,且你可以模拟一个稳定的方块栈.

　　因为这是一本关于游戏物理的书,所以你需要一个渲染器.如果你自己有一个,那就太好了.如果没有的话,我准备了一个简单的Vulkan渲染器.与本书一起的网站, [https://gamephysicsweekend.github.io/](https://gamephysicsweekend.github.io/),有介绍怎么样使用.特别说明,这个"渲染器"只是本书的一个骨架项目.这个项目里面有一些注释"// TODO: add code",这就是需要你从书中手动写代码到项目里的地方.

　　本书的代码是用C++写的.由于本书计划用于游戏应用,我假设你是游戏行业从业人员或者打算投身于游戏工业的学生.且C++在游戏开发中普遍使用,所以它是本书自然而然的选择.

　　与此同时也假设你知道一些向量微积分,线性代数以及四元数的一些知识.如果不知道也没关系.我推荐Schaum的大纲作为导引.这些书相对不太贵且有着吸引人的学科概述.我也推荐3blue1brown的视频讲义来构建数学直觉.

　　讲到数学,本书假设+z轴为上向量;其他人喜欢使用+y轴作为上向量,但这里不是.以及作为基本簿记,向量是写成变量上有一个小箭头,$\vec v$ ,矩阵则总是大写粗体,**M**,四元数则是小写粗体,**q**,标量可以大写也可以小写,$s S$  

$$
{\vec v} - 向量 \\
{\textbf M} - 矩阵 \\
{\textbf q} - 四元数 \\
s - 标量
$$

　　最后,我从没找到一本书是没有任何错误的.所以如果你找到任何错误,可以在推特@ghodges_dev上给出建设性的反馈或问题.如果没有社交媒体,则可以给我发电子邮件greg@gamephysicsweekend.com.一些人喜欢在github页面提交issue,那也是极好的联系.但是我可能不能及时响应,但我保证一定很快会回复.

　　无论你是学生或者是专业的程序员,我都希望你可以从本书中有所收获.让我们开始吧.



## 1.1 致谢
Peter Shirley, Zachary Williams, Adam Petrone, Nick Korn, Mincho Paskalev, Yuki Nishidate

# 2 向量类
我们需要一种可以在2D,3D和4D空间表示位置和方向的方式.基于此,我们使用Vec类表示2D向量,Vec3类表示3D向量以及Vec类表示4D向量.

　　虽然我们通常只关心3D向量,因为我们在创建一个3D物理引擎.但是Vec2和Vec4在这里主要就是支持下一章会讲到的矩阵类.