---
title: Phong着色模型
date: 2024-07-02
tags: 渲染
category: 渲染
mathjax: true
---

Phong着色模型包含ambient,diffuse,specular部分,对应中文就是环境光,漫反射光照和高光光照.各自呈现的效果如图所示:

![](image.png)

<!-- more -->

# 环境光照

环境光照可以理解为外界给予的颜色,此外一般还包含物体自发光颜色,但在一些文献中这两个是分开讨论的.

漫反射光照可以理解为光现在粗糙物体表面投射时呈现光像各个方向反射的效果.

高光反射则是模拟光投射在光泽物体上呈现的效果.

环境光照的计算很简单,给定一个ambientStrength然后乘以物体颜色即可.

```sh
float ambientStrength=0.1;//环境光强度,模拟环境光照
vec3 ambient=ambientStrength*lightColor;//环境光
```

![当ambientStrength为0.1时的物体外观](image-1.png)


objectColor可以是红色绿色蓝色等,也可以是白色,由纹理贴图决定物体外观.

# 漫反射

漫反射有两种,一种为Lambert模型,一种为Half-Lambert模型.

![](image-2.png)

Lambert: $DiffColor=(LightColor*DiffStrength)*Max(0,Dot(n,l))$

Half-Lambert: $DiffColor=(LightColor*DiffStrength)*(\alpha*Dot(n,l)+\beta)$

Diffcolor就是最终呈现出的漫反射光照颜色啦,LightColor就是光源的颜色,Diffstrength就是漫反射的强度因子,n就是顶点/片元的法向量,l就是从光源指向顶点/片元位置的向量.

先来Lambert模型.

```sh
vec3 normal=normalize(Normal);
vec3 lightDir=normalize(lightPos-fragPos);
float diff=max(dot(normal,lightDir),0.0);//lambert光照模型
vec3 diffuse=lightColor*diffuseStrength*diff;//漫反射光照
```

![Lambert模型,环境光强为0的情况](image-3.png)

因为对于lightDir和normal我们仅需要知道他们的方向,而不想让数值影响后续的计算,所以先对他们进行标准化.

法线在这里是通过手工定义然后通过顶点属性传进GLSL的.我是将面的法线定义在模型空间中,因此如果想光照模型对进行仿射变换后的物体模型仍然有良好的效果的话则需要定义一个**法线矩阵**.例如在物体进行**非等比**缩放时其法线将不一定垂直于表面(这时候切线也变了).

![](image-4.png)

法线矩阵可以理解为模型矩阵左上方3x3矩阵的逆矩阵的转置矩阵.

```sh
Normal=mat3(transpose(inverse(model)))*aNormal;
```
通过法线矩阵可以消除仿射变换带来的影响.


fragPos则是通过顶点*模型坐标得到该顶点在世界坐标的位置
```sh
fragPos=vec3(model*vec4(aPos,1.0));
```
接下来的步骤根据公式计算即可.

用传统的Lambert模型会发现在物体的背光处几乎没有光存在,并且在箱子的棱那里有很明显的颜色分割.

![](image-5.png)

绕到模型后方发现丢失了所有的模型细节

![](image-6.png)

有一种解决方法就是添加环境光:

![环境光强为0.1的情况](image-7.png)

相对来说会好一丢丢.

有没有更好的方法呢?Valve在做半条命的时候提出了Half-Lambert模型.对原Lambert做了一些简单的修改.

在原Lambert中, $Max(0,Dot(n,l))$这个是防止法线和光源向量夹角大于90度而出现负数的情况.将负数部分生生变为0,则会出现棱附近颜色断层的现象.同时由于背光部分漫反射为0,因此全黑.

Half-Lambert中,$(\alpha*Dot(n,l)+\beta)$ 则是对点积进行一个 $𝛼$倍的缩放然后加上$𝛽$值的偏移.一般情况下$𝛼$和$𝛽$都取0.5即可,这样就避免了负数的出现.从而使背面也有明暗变化.

```sh
vec3 normal=normalize(Normal);
vec3 lightDir=normalize(lightPos-fragPos);
float diff=0.5*(dot(normal,lightDir))+0.5;//half-lambert模型，用于改善模型背光区域全黑的问题
vec3 diffuse=lightColor*diffuseStrength*diff;//漫反射光照
```

![使用Half-Lambert之后相对柔和很多](image-8.png)

# 高光反射

高光反射模型同样也有两种:

Phong高光: $$SpecColor=(LightColor*SpecStrength)*Max(0,Dot(v,r))^{GlossStrength}$$
Blinn-Phong高光: $$SpecColor=(LightColor*SpecStrength)*Max(0,Dot(n,h))^{GlossStrength}$$

![](image-9.png)

高光反射要求给出光源的反射方向 $𝑟$,即Reflect.同时比漫反射多了一个 $𝑣$,即观察向量.

```sh
vec3 normal=normalize(Normal);
vec3 lightDir=normalize(lightPos-fragPos);
vec3 viewDir=normalize(viewPos-fragPos);
vec3 reflectDir=reflect(-lightDir,normal);//reflect函数第一个参数要求从光源指向片元，因此需要取反，此处位计算反射向量
float spec=pow(max(dot(viewDir,reflectDir),0),32);
vec3 specular=lightColor*specularStrength*spec;
```

因为高光反射是与观察的位置相关,因此通过计算反射向量与观察向量之间的关系来求得高光区域.GLSL中有一个内置的函数reflect可以求得反射向量,第一个参数要求给的是从光源指向片元位置得向量.前文中lightDir是从片元位置指向光源位置的.因此需要取反.

当然也可以不用内置reflect函数而自己写一个:$ReflectDir=l-2*(Dot(n,l))*n$

```sh
vec3 reflectDir=-lightDir-2*(dot(normal,(-lightDir)))*normal;
```

GlossStrength这里为32.GlossStrength取值越大则光斑越明显,金属质感越强.

![](image-10.png)

最终将环境光,漫反射光照,高光反射光照得到的颜色分量加在一起就形成Phong着色模型了:

![](image-11.png)

Blinn-Phong的高光反射模型对比传统的Phong氏着色模型:没有使用反射向量,通过引用新向量$ℎ$和法向量计算得到.

![](image-12.png)

Blinn-Phong高光:
$$
SpecColor=(LightColor*SpecStrength)*Max(0,Dot(n,h))^{GlossStrength}
$$

$ℎ$向量通过对观察向量$𝑣$和光照方向$𝑙$相加后归一化获得的:
$$
h=\frac{v+l}{\left| v+l \right|}
$$

注意,这里光照方向向量指的是从片元位置指向光源位置的向量.前文中reflect函数需要对lightDir进行取反,而Blinn-Phong中的$𝑙$不需要进行取反.

```sh
float spec=pow(max(dot(normal,normalize(lightDir+viewDir)),0),100);
```

因此Phong氏着色模型以及其改进有挺多的,漫反射有Lambert和Half-Lambert,高光反射有Phong和Blinn-Phong.

以上的实现都是在片元着色器中进行的.如果是要优化性能节约资源的话可以将上述的实现方式丢到顶点着色器中实现.但是出来的效果并不如在片元着色器中的顺滑.因为顶点着色器是per-pixel计算的,因此计算的边缘会有一定的锯齿.

![](image-13.png)

在顶点着色器中实现Phong氏模型叫Gouraud着色

# 参考文档或书籍:

[learnopengl-cn](https://learnopengl-cn.github.io/)

[Unity Shader入门精要](https://product.dangdang.com/23972910.html)