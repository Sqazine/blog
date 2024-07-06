---
title: 帧缓冲与常用后期特效
date: 2024-07-05 13:26:20
tags: 渲染
category: 渲染
mathjax: true
---

帧缓冲(Framebuffer)顾名思义,就是内存里面当前帧信息.同样的缓冲还有深度缓冲(Depth Buffer),模板缓冲(Stencil Buffer).

<!-- more -->

# 创建帧缓冲

OpenGL中有一个默认的帧缓冲.如果想要绑定当前帧缓冲为默认帧缓冲则可以通过:
```sh
glBindFramebuffer(GL_FRAMEBUFFER, 0);
```
创建与删除帧缓冲与创建纹理,VAO,VBO,EBO等方式是一样的.
```sh
GLuint fbo;
glGenFramebuffers(1, &fbo);
glBindFramebuffer(GL_FRAMEBUFFER, fbo);
//进行一系列操作之后
//...
glDeleteFramebuffers(1, &fbo);
```

帧缓存想要好好工作必须要为它弄一个**附件**,附件可以是**纹理**或**渲染缓冲对象**(Render Buffer Object,rbo).

如果纹理作为帧缓存的的附件的话,就要创建一个纹理.创建纹理与创建VAO,VBO方式差不多,但是

● 不用考虑纹理环绕方式与MipMap

● 一般情况下将纹理宽高设置为屏幕大小

● 纹理的数据参数为nullptr,因为用帧缓存填充这个纹理

● 调用glFramebufferTexture2D附加到帧缓存上

```sh
unsigned int texture;
glGenTextures(1, &texture);
glBindTexture(GL_TEXTURE_2D, texture);
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glBindTexture(GL_TEXTURE_2D, 0);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, texture, 0);
```

这里用了颜色作为附件,GL_COLOR_ATTACHMENT0.也可以使用深度和模板GL_DEPTH_STENCIL_ATTACHMENT.

```sh
glTexImage2D( GL_TEXTURE_2D, 0, GL_DEPTH24_STENCIL8, 800, 600, 0,  GL_DEPTH_STENCIL, GL_UNSIGNED_INT_24_8, NULL);
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_TEXTURE_2D, texture, 0);
```
渲染缓存对象顾名思义跟VBO一样的都是缓存.其优势是存储的数据为OpenGL的原生渲染格式,可直接将数据存进去,但是渲染缓存对象是只写的,并不能读.但是可以从当前的帧缓存中通过glReadPixels读取.

```sh
GLuint rbo;
glGenRenderbuffers(1, &rbo);
glBindRenderbuffer(GL_RENDERBUFFER, rbo);
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, SCREEN_WIDTH, SCREEN_HEIGHT);
glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```

创建渲染缓存对象跟其他的一致,就是要制定缓存的存储空间.这里存储了24位的深度缓存和8位的模板缓存.

现在帧缓存里面有了一个用于存储颜色的纹理和一个用于存储深度值模板值的渲染缓存对象.

在主循环中,首先将帧缓存绑定到我们定义的帧缓存中,然后渲染场景.这时候场景生成的图像最终成为帧缓存中纹理附件的纹理,最后将帧缓存绑定到默认帧缓存,接着绑定纹理附件的VAO,和纹理附件的ID值到片元着色器上,关闭深度测试,最后输出渲染这个图像.

```sh
int main()
{
    //...
    while(!Exit)
    {
            glBindFramebuffer(GL_FRAMEBUFFER, fbo);
    //绘制场景
    //...
            glBindFramebuffer(GL_FRAMEBUFFER, 0);
    	sceneShader.use();
    	sceneShader.setInt("screenTexture", 0);
    	glDisable(GL_DEPTH_TEST);
    	glBindVertexArray(VAO);
    	glActiveTexture(GL_TEXTURE0);
    	glBindTexture(GL_TEXTURE_2D, texture);
    	glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);

            glfwSwapBuffers(window);
    	glfwPollEvents();
    }
}
```

对应的顶点着色器非常简单,就是传进纹理的位置与纹理映射

但是由于我们想让纹理始终处于屏幕最前方,因此位置并不需要进行MVP模型的变换.

```sh
#version 330 core

layout(location=0) in vec2 aPos;
layout(location=1) in vec2 aTexCoords;

out vec2 TexCoords;

void main()
{
	gl_Position=vec4(aPos.x,aPos.y,0.0,1.0);
	TexCoords=aTexCoords;
}
```

片元着色器就是采样纹理颜色并输出

```sh
#version 330 core
out vec4 FragColor;
in vec2 TexCoords;
uniform sampler2D screenTexture;
void main()
{
    FragColor=texture(screenTexture,TexCoords);
}
```

![最终就得到一个跟之前没什么区别的场景.](image.png)

但是注意这里显示的是screenTexture的颜色值.我们可以通过这个纹理进行一些颜色的变化,以产生一些特效.

# 反相特效

颜色区间是 $[0.0,1.0]$ ,也有 $[0.0,255.0]$ ,当然两者之间可以通过乘或除以255进行转换.这里用 $[0.0,1.0]$ .举个例子,一个红色 $(1.0,0.0,0.0)$ ,其互补颜色为 $(0.0,1.0,1.0)$ ,可以通过求得互补颜色得到反相效果.

在片元着色器修改下:
```sh
#version 330 core
out vec4 FragColor;
in vec2 TexCoords;
uniform sampler2D screenTexture;
void main()
{
  FragColor = vec4(vec3(1.0 - texture(screenTexture, TexCoords)), 1.0);
}
```
效果如下

![原本红色变成青色,白色变成黑色,黑色变成白色](image2.png)

# 灰度特效

灰度特效使场景只有黑白灰三种颜色,一种思路是采用颜色分量相加然后均分
```sh
float average = (FragColor.r + FragColor.g + FragColor.b) / 3.0;
```
另一种是采用加权平分
```sh
#version 330 core
out vec4 FragColor;
in vec2 TexCoords;
uniform sampler2D screenTexture;
void main()
{
    //灰度
	FragColor=texture(screenTexture,TexCoords);
	float average = 0.2126 * FragColor.r + 0.7152 * FragColor.g + 0.0722 * FragColor.b;
	FragColor=vec4(vec3(average),1.0);
}
```

常用于人物死亡后场景置灰

![](image3.png)

# 亮度,饱和度和对比度
亮度可以设定一个值然后乘以需要输出的颜色

饱和度参照上述的灰度然后通过参数进行插值

对比度可以定义一个各分量为0.5的vec3值与颜色值进行插值.

```sh
#version 330 core
out vec4 FragColor;
in vec2 TexCoords;

uniform sampler2D screenTexture;

uniform float brightness;
uniform float saturation;
uniform float contrast;

void main()
{
    //亮度,饱和度,对比度
	FragColor=texture(screenTexture,TexCoords);
	FragColor*=brightness;
	
	float luminance = 0.2126 * FragColor.r + 0.7152 * FragColor.g + 0.0722 * FragColor.b;
	vec3 luminanceColor=vec3(luminance);
	FragColor=(1.0-saturation)*luminanceColor+saturation*FragColor;

	FragColor=(1-contrast)*vec3(0.5)+contrast*FragColor;
	FragColor=vec4(FragColor.r,FragColor.g,FragColor.b,1.0);
}
```

在主循环中用时间控制brightness分量和contrast分量

```sh
sceneShader.setFloat("brightness",abs(sin(glfwGetTime())));
sceneShader.setFloat("saturation",1.0);
sceneShader.setFloat("contrast", abs(cos(glfwGetTime())));
```

可以看到亮度和饱和度随着函数的变化明暗不断变化

![](image4.gif)

# 锐化

锐化用到了一个叫卷积核的东西.卷积核通常定义为一个可以是2X2,3X3,4X4,5X5的矩阵

![](image5.png)

卷积核中每个格子有对应的权重,以当前像素作为卷积核的中心,并对卷积核覆盖的像素区域的像素值乘以对应权重最后叠加可以得到该卷积核中心像素最终的像素值.当然权重是自由分配的,但是保证每个分量的和为1,不然最后得出的像素值会偏大或者偏小.

锐化关键代码:
```sh
//锐化
const float offset=1.0/300;
	vec2 offsets[9] = vec2[](
        vec2(-offset,  offset), // 左上
        vec2( 0.0f,    offset), // 正上
        vec2( offset,  offset), // 右上
        vec2(-offset,  0.0f),   // 左
        vec2( 0.0f,    0.0f),   // 中
        vec2( offset,  0.0f),   // 右
        vec2(-offset, -offset), // 左下
        vec2( 0.0f,   -offset), // 正下
        vec2( offset, -offset)  // 右下
    );
	    float kernel[9] = float[](
        -1, -1, -1,
        -1,  9, -1,
        -1, -1, -1
    );

	vec3 col=vec3(0.0);
	vec3 sampleTex[9];
	for(int i=0;i<9;i++)
    {
		sampleTex[i]=vec3(texture(screenTexture,TexCoords.st+offsets[i]));

		for(int i=0;i<9;i++)
		col+=sampleTex[i]*kernel[i];

		FragColor=vec4(col,1.0);
    }
```

这里定义了一个偏移数组.取纹理中该点附近的点的采样,然后每个采样*卷积核的权值最后叠加到一起.形成如下效果.

![游戏中打了肾上腺素之类的可以用这个](image6.png)

# 均值模糊

模糊对比锐化需要修改的地方只有卷积核.就是改变其权重而已

```sh
float kernel1[9] = float[](
    1.0 / 16, 2.0 / 16, 1.0 / 16,
    2.0 / 16, 4.0 / 16, 2.0 / 16,
    1.0 / 16, 2.0 / 16, 1.0 / 16  
    );
```

效果如下:
![](image7.png)

改成这样模糊效果会没有那么明显:
```sh
  float kernel1[9] = float[](
       0, 0.125, 0,
      0.125,  0.5, 0.125,
       0, 0.125, 0
    );
```

![](image8.png)

# 高斯模糊

均值模糊实现的效果挺不错.而且原理挺简单.高斯模糊相对来说会复杂一点.其模拟了相邻像素越近对当前像素影响越大的过程.

高斯模糊也用了卷积核,叫高斯核.高斯核里面每个权重都遵循高斯方程:
$$
G(x,y)=\frac{1}{2\pi\sigma^{2}}e^{\frac{x^{2}+y^{2}}{2\sigma^{2}}}
$$

𝜎 为标准方差,一般取1就行

$\frac{1}{2\pi\sigma^{2}}$ 对实际结果影响不大

𝑥 , 𝑦 分别为当前位置到卷积核中心的整数距离

所以方程可以简化为$G(x,y)=e^{\frac{x^{2}+y^{2}}{2}}$

以下是 𝜎 为1时的5X5高斯核.这里有一个优化就是:NxN的核对图像进行滤波需要 
$𝑁 ⋅ 𝑁 ⋅ 𝑊𝑖𝑑𝑡ℎ⋅ 𝐻𝑒𝑖𝑔ℎ𝑡$次采样.可以看到将核拆分成两个一维的仅需要 
$2 ⋅𝑁 ⋅ 𝑊𝑖𝑑𝑡ℎ ⋅ 𝐻𝑒𝑖𝑔ℎ𝑡$ 次采样.

![](image9.png)

```sh
//blur的参数随着时间改变
sceneShader.setFloat("blur", abs(cos(glfwGetTime())));

//高斯模糊
float offset=1.0/100*blur;
	vec2 offsetsHorizontal[5] = vec2[](
        vec2(-offset*2,  0.0f),   // 左2
        vec2(-offset,  0.0f),   // 左1
        vec2( 0.0f,    0.0f),   // 中
        vec2( offset,  0.0f),   // 右1
        vec2( offset*2,  0.0f)   // 右2
    );
	vec2 offsetsVertical[5] = vec2[](
        vec2( 0.0f,    offset*2), // 正上2
        vec2( 0.0f,    offset), // 正上1
        vec2( 0.0f,    0.0f),   // 中
        vec2( 0.0f,   -offset), // 正下1
        vec2( 0.0f,   -offset*2) // 正下2
    );
    float kernel[5] = float[](0.0545,0.2442,0.4026,0.2442,0.0545);

	vec3 col=vec3(0.0);
	vec3 sampleTex[5];
	for(int i=0;i<5;i++)
		sampleTex[i]=vec3(texture(screenTexture,TexCoords.st+offsetsHorizontal[i]));
	for(int i=0;i<5;i++)
		col+=sampleTex[i]*kernel[i];
	for(int i=0;i<5;i++)
		sampleTex[i]=vec3(texture(screenTexture,TexCoords.st+offsetsVertical[i]));
	for(int i=0;i<5;i++)
		col+=sampleTex[i]*kernel[i];

FragColor=vec4(col,1.0);
```

可以通过修改offset的参数获得理想的效果.这里参数对应效果如图:

![感觉可以用在主角刚睁开眼时的情形](image10.gif)

# 边缘检测

边缘检测需要通过相邻像素之间的颜色梯度表示,处于边缘之间两个颜色相差较大,则梯度值比较大.因此冯乐乐的书<Unity Shader 入门精要>介绍了3种常见的边缘检测卷积核

![](image11.png)

在进行计算时需要得出两个方向上的梯度值,因此需要一个  $𝐺_𝑥$ 和一个 $𝐺_𝑦$ .然后一边的权值是正的另一边是负的.因为该卷积核的目的主要是计算梯度值,因此考虑该像素两边的颜色值的一致性,若两边相差很少则可以认为该像素不处于边缘.整体梯度可以按照 $G=\sqrt{G_{x}^{2}+G_{y}^{2}}$ 计算得到.

```sh
const float offset=1.0/1000;
	vec2 offsets[9] = vec2[](
        vec2(-offset,  offset), // 左上
        vec2( 0.0f,    offset), // 正上
        vec2( offset,  offset), // 右上
        vec2(-offset,  0.0f),   // 左
        vec2( 0.0f,    0.0f),   // 中
        vec2( offset,  0.0f),   // 右
        vec2(-offset, -offset), // 左下
        vec2( 0.0f,   -offset), // 正下
        vec2( offset, -offset)  // 右下
    );
	float Gx[9]=float[](
	-1,0,1,
	-2,0,2,
	-1,0,1
	);
	float Gy[9]=float[](
	-1,-2,-1,
	 0, 0,0,
	1, 2,1
	);

	vec3 sampleTex[9];
	float edgeX;
	float edgeY;
	for(int i=0;i<9;i++)
		sampleTex[i]=vec3(texture(screenTexture,TexCoords.st+offsets[i]));

	for(int i=0;i<9;i++)
	{
		float luminance = 0.2126 * sampleTex[i].r + 0.7152 * sampleTex[i].g + 0.0722 * sampleTex[i].b;
		edgeX+=luminance*Gx[i];
		edgeY+=luminance*Gy[i];
	}
	float edge=1-abs(edgeX)-abs(edgeY);

	FragColor=(1-edge)*edgeColor+edge*backgroundColor;
	FragColor=vec4(FragColor.r,FragColor.g,FragColor.b,1.0);
```

原来的场景是这样的:

![](image12.png)

edgeColor设置为黑色,backgroundColor设置为白色.然后关闭场景里所有的光源.这时候2B只能看到轮廓了.设置边缘检测之后如下图.

![](image13.png)

加上灯光的效果是这样:

![](image14.png)

# 参考文档或书籍
---

learnopengl-cn
https://learnopengl-cn.github.io/

---

---

Unity Shader入门精要
https://product.dangdang.com/23972910.html

---