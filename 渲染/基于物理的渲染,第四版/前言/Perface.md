# 《基于物理的渲染第四版》前言(Perface)

## [序言](https://pbr-book.org/4ed/Preface#)

*就像其他信息应该提供给那些想要学习和理解的人一样,程序源代码是程序员从他们的前辈那里学习编程艺术的唯一手段.剧作家不允许其他剧作家读他们的剧本(或允许他们)在剧院演出,他们甚至被禁止做笔记,这是不可想象的.同样,任何优秀的作家都是博览百书的,因为每个学会写字的孩子读的东西都是他写的东西的数百倍.然而,程序员被要求发明字母表,并学会自己写长篇小说.除非下一代程序员能够接触到前人所收集的知识和信息,否则编程就无法成长和学习.*  —Erik Naggum


渲染是计算机图形学的一个基本组成部分.在最高的抽象层上,渲染是将三维场景的描述转换成图像的过程.动画,几何建模,纹理和计算机图形学的其他领域的算法都必须通过某种渲染过程来传递它们的结果,以便它们可以在图像中可见.渲染已经无处不在;从电影到游戏等等,它为创意表达,娱乐和可视化开辟了新的领域.

在该领域的早期,渲染研究主要集中在解决基本问题,例如确定从给定的视点可以看到哪些物体.随着这些问题的有效解决方案的逐渐发现,以及由于图形学其他领域的持续进步,更丰富,更逼真的场景描述渐渐变得可能,现代渲染已经发展到包括来自广泛学科的思想,包括物理学和天体物理学,天文学,生物学,心理学和感知研究,以及纯数学和应用数学.渲染的跨学科性质是它是一个如此迷人的研究领域的原因之一.

本书通过记录在案的源代码展示了一系列现代渲染算法,以构建一个完整的渲染系统.这本书中几乎所有的图片,包括封面上的图片,都是由该软件渲染的.这些页面中介绍了汇集在一起生成这些图像的所有算法.该系统pbrt是使用一种称为 *文学编程* 的编程方法编写的,该方法将描述系统的散文与实现它的源代码混合在一起.我们认为,文学编程方法是引入计算机图形学和一般计算机科学思想的一种有价值的方式.通常,在实现算法之前,算法的一些微妙之处可能是不清楚或隐藏的,因此查看实际实现是深入了解该算法细节的好方法.事实上,我们相信,以这种方式深入了解一些精心挑选的算法,为进一步的研究提供了更好的基础.

除了阐明算法在实际中是如何实现的之外,在一个完整且复杂的软件系统中呈现这些算法还使我们能够解决中型渲染系统的设计和实现中的问题.渲染系统的基本抽象和接口的设计对实现的优雅性和以后扩展它的能力都有重大影响,但在这些设计中,很少人会讨论取舍权衡.

pbrt以及本书的的内容完全侧重于 *照片级渲染(photorealistic rendering)* ,照片级渲染有不同定义,既可定义成生成与相机拍摄的照片无异的图像,也可定义为生成能唤起人类观察者与实际场景相同反应的图像.专注照片级真实感的原因有很多.逼真的图像对于电影中的特效至关重要,因为计算机生成的图像通常必须与现实世界的镜头无缝混合.在像电脑游戏这样的应用中,所有的图像都是合成的,照片级真实感是一种有效的工具,可以使观察者忘记他或她正在观看一个实际上并不存在的环境.最后,照片级真实感为评估渲染系统输出的质量提供了一个合理定义的指标.

## 受众

本书主要面向三种受众.第一类是研究生或学习计算机图形学课程的高年级本科生.本书假定已有的计算机图形学知识达到了大学入门课程的水平,尽管某些关键概念(如基本的矢量几何和变换)将在本书中复习.对于没有接触过数万行源代码的程序的学生来说,本书的编程风格为他们提供了解这种复杂性的简单入门.我们特别注意解释系统中一些关键接口和抽象概念背后的原因,以便让这些读者了解为何如此架构这一系统.

第二类受众是计算机图形学的高级研究生和研究人员.对于从事渲染研究的人来说.这本书提供了对该领域的广泛介绍.而pbrt源代码提供了一个有用的基础(或至少可以使用其中的一些源代码).对于那些从事计算机图形学其他领域工作的人来说.我们相信.对渲染的透彻理解会对他们的工作有所帮助.

我们的最终读者是工业领域的软件开发人员.尽管这部分读者对本书中的许多基本思想并不陌生.但看到以文艺风格呈现的算法解释.他们可能会有新的视角.pbrt还包括许多算法的精心设计和调试实现.这些算法的正确实现可能具有挑战性;有经验的渲染从业人员应该会对这些算法特别感兴趣.我们希望.深入研究一个完整而复杂的渲染系统的特定组织结构.也能引发读者的思考.

## 概述与目标

pbrt基于光线追踪算法.光线追踪是一种优雅的技术,起源于透镜制造;19 世纪,Carl Friedrich Gauß通过自制透镜追踪光线.计算机上的光线追踪算法是跟踪无限小的光线穿过场景的路径,直到它们与某个表面相交.这种提供了一种简单的方法,可以找到从任何特定位置和方向看到的第一个可见物体,也是许多渲染算法的基础.

pbrt的设计与实现有三个主要目标: *完整(complete)*,*图文并茂(illustrative)*,*基于物理(physically based)* .

完整意味着系统不应缺少高质量商业渲染系统中的关键功能.特别是,这表示重要的实际问题,如抗锯齿,健壮性,数值精度,以及高效渲染复杂场景的能力都应该得到彻底的解决.从系统设计之初就考虑这些问题是非常重要的,因为这些功能可能会对系统的所有组件产生微妙的影响,而且在实现的后期阶段可能很难改到系统中.

我们的第二个目标表示我们在选择算法,数据结构和渲染技术时,要谨慎小心,注重可读性和清晰度.由于与其他渲染系统相比,更多的读者会对它们的实现进行研究,因此我们尽量选择我们所知道的最优雅的算法,并尽可能好地实现它们.这个目标还要求系统小到一个人就能完全理解.我们采用可扩展的架构来实现pbrt,系统的核心部分由一系列精心设计的接口类来实现,而具体功能则尽可能由这些接口的实现来实现.这样做的结果是,人们不需要了解所有的具体实现,就能理解系统的基本结构.这样,我们就可以更容易地深入研究感兴趣的部分,而跳过其他部分,同时又不会忽略整个系统是如何协调在一起的.

既要完整,又要图文并茂,这两个目标之间存在着矛盾.如果要实现和描述每一种可能的有用技术,不仅会使本书篇幅过长,而且会使系统的复杂程度令大多数读者望而却步.如果pbrt缺乏某个特别有用的功能,我们会尝试设计架构,以便在不改变整个系统设计的情况下添加该功能.

物理渲染的基础是物理定律及其数学表达式.pbrt在设计上使用了正确的物理单位和概念来计算数量和实现算法.pbrt致力于 *计算物理正确(physically correct)* 的图像;其正确地反映了真实世界中光线的反射情况.有关pbrt中的选择见1.2的延伸讨论.决定使用物理基础的一个好处是,它为程序的正确性提供了一个具体的标准:对于简单的场景,预期的结果可以用封闭的形式计算出来,如果pbrt不能计算出相同的结果,我们就知道一定是实现过程中出现了错误.同样,如果pbrt中不同的物理照明算法对同一场景给出了不同的结果,或者pbrt与另一个物理渲染器给出的结果不一样,那么其中肯定存在错误.最后,我们认为这种基于物理的渲染方法之所以有价值,是因为它非常严谨.当不清楚应该如何进行特定计算时,物理学给出的答案可以保证结果的一致性.

与这三个目标相比,效率的优先级较低.由于渲染系统在生成图像的过程中通常要运行数分钟或数小时,因此效率显然非常重要.不过,我们主要关注的是**算法效率**,而不是底层代码优化.在某些情况下,明显的微观优化要比清晰,有条理的代码更重要,尽管我们在优化系统中计算量最大的部分时做了一些努力.

在介绍pbrt和讨论其实现的过程中,我们希望传达一些从多年的渲染研究和开发中汲取的宝贵经验.要编写一个好的渲染器,不仅仅是将一组快速算法串联起来;让系统既灵活又健壮是一项艰巨的任务.系统的性能必须随着几何体或光源的增加或任何其他复杂度的增高而缓缓降低.

开发一个能解决所有这些问题的系统的回报是巨大的—编写一个新的渲染器或为现有的渲染器添加一个新功能,并用它来创建一个以前无法生成的图像是一件非常愉快的事情.我们编写本书的最根本目的就是为更多读者带来这样的机会.我们鼓励读者在阅读本书的过程中,使用系统渲染pbrt软件发行版中的示例场景.每章末尾的练习建议对系统进行修改,这将有助于阐明其内部工作原理,并建议开展更复杂的项目,通过添加新功能来扩展系统.

本书的网站位于[pbrt.org](https://pbrt.org/).该网站包括pbrt源代码,可下载使用pbrt渲染的场景,错误跟踪器以及勘误表的链接.如果您发现本文中有任何未在勘误表中列出的错误,请发送电子邮件至 *authors@pbrt.org* .我们非常重视您的反馈意见!

## 第二版对比第一版的变化

从2004年出版本书第一版到2010年出版第二版,六年过去了.在此期间,该书售出了数千册,pbrt软件也从该书的网站上被下载了数千次.pbrt用户群给了我们大量的反馈和鼓励,我们对该系统的使用经验指导我们做出了许多决定,对第一版中的pbrt版本和第二版中的pbrt版本进行了修改.除了修复大量错误外,我们还对设计进行了重大修改和增强:
● *移除插件架构(Removal of the plugin architecture)* :第一版pbrt采用运行时插件架构,动态加载当前渲染场景中使用的形状,灯光,集成器,摄像头等对象的实现代码.这种方法允许用户使用新的对象类型(如新的形状图元)扩展pbrt,而无需重新编译整个渲染系统.这种方法最初看起来很优雅,但却使在多个平台上支持pbrt的任务变得复杂,也增加了调试的难度.它真正实现的唯一新的使用场景(仅二进制发布的pbrt或二进制插件)实际上与我们的教学和开源目标背道而驰.因此,本版放弃了插件架构.  
● *移除图像处理管线(Removal of the image-processing pipeline)* :pbrt的第一个版本提供了一个色调映射接口,可将高动态范围(HDR)浮点输出图像直接转换为低动态范围TIFF图像进行显示.这一功能在2004年很有意义,因为当时对HDR图像的支持还很少.然而到了2010年,数码摄影技术的进步已经让 HDR 图像变得司空见惯.虽然色调映射的理论和实践都很优雅,值得学习,但我们决定将新书的重点完全放在图像形成的过程上,而跳过图像显示的主题.有兴趣的读者可以参阅Reinhard等人([2010](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Reinhard10))撰写的HDR图像显示处理的书,该书对HDR图像显示过程进行了全面而现代的阐述.  
● *任务并行(Task parallelism)* :多核架构变得无处不在,我们认为,如果pbrt不能根据本地可用内核的数量进行扩展,就无法继续发挥作用.我们还希望本书中记录的并行编程实现细节能帮助图形程序员理解编写可扩展并行代码的一些微妙之处和复杂性.  
● *适合"产品级"渲染(Appropriateness for "production" rendering)* :第一版pbrt完全是作为教学工具和渲染研究的垫脚石.事实上,我们在制作第一版时做出了许多与在生产环境中使用相悖的决定,如对基于图像的照明支持有限,不支持运动模糊,光子映射实现在复杂照明下不稳定等.随着对这些功能的支持以及对次表面散射和Metropolis 光传输的支持大大改进,我们认为第二版pbrt更适合渲染复杂环境中的高质量图像.

## 第三版对比第二版的变化

六年过去了,是时候对本书和pbrt系统进行更新和扩展了. 我们继续从读者和用户的经验中学习,以便更好地了解哪些主题是最有用的.此外,渲染研究也在继续进行;书中的许多内容也到了更新的时候,以反映当前的最佳实践.我们在多个方面做出了重大改进:

● *双向光传输(Bidirectional light transport)* :第三版pbrt增加了双向路径跟踪器,包括对体积光传输和多重重要性采样加权路径的全面支持.全新的 Metropolis 光传输积分器使用了双向路径追踪器组件,从而可以特别简洁地实现该算法.  
● *次表面散射(Subsurface scattering)* :许多物体的外观—尤其是皮肤和半透明物体—都是次表层光传输的结果.我们在第二版中对次表层散射的实现反映了 21 世纪初的技术水平;我们彻底更新了BSSRDF模型和次表层光传输算法,以反映随后十年的研究进展. 
● *数值健壮的求交(Numerically robust intersections)* :几何光线相交计算中的浮点舍入误差一直是光线追踪中的一个难题:它会导致整个图像出现微小误差.我们专注于这一问题,并推导出了这一误差的保守(但严格)界限,这使得我们的实现在这一问题上比之前的渲染系统更加稳健.  
● *参与介质表示(Participating media representation)* :我们大大改进了散射介质在系统中的描述和表示方法;这使得嵌套散射介质的结果更加准确.一种新的采样技术能够无偏地渲染非均匀介质,并与系统的所有其他部分完美融合.  
● *测量材质(Measured materials)* :本版增加了一项新技术,使用稀疏频率空间基来表示和评价测量材质.这种方法非常方便,因为它可以进行精确的重要性采样,而上一版中使用的表示方法则无法做到这一点.  
● *光子映射(Photon mapping)* :光子映射算法的一大进步是开发出了无需在内存中存储所有光子的变体.我们用一种基于随机渐进光子映射的实现方法取代了pbrt的光子映射算法,它能有效地渲染许多困难的光传输效果.  
● *样本生成算法(Sample generation algorithms)* :渲染算法中用于数值积分的样本值分布对最终结果的质量有着惊人的影响.我们全面更新了对这一主题的论述,比以前更深入地涵盖了新方法和高效的实现技术.

## 第四版对比第三版的变化

渲染算法的创新丝毫没有放缓的迹象,因此我们在2019年开始重点编写第四版教材. 我们不仅对几乎每一章的内容都进行了大量补充,还更新了章节顺序和引入的观点,将蒙特卡罗积分和路径追踪的基本观点放在了最前面,而不是留到最后.

该系统的功能改进尤为显著,其中包括:

● *体积散射(Volumetric scattering)* :我们更新了对参与介质散射建模的算法,使其达到最新技术水平,增加了对自发光体积的支持,对不同密度体积的高效采样,以及对色度介质的强大支持,在色度介质中,散射特性随波长而变化.    
● *光谱渲染(Spectral rendering)* :我们取消了所有使用RGB颜色进行光照计算的方法;现在,pbrt完全根据与波长相关的光谱分布样本来计算光照.这种方法不仅在物理上比使用RGB更准确,而且还能让pbrt精确地模拟色散等效果.  
● *反射模型(Reflection models)* :我们对BSDF和反射模型的基础进行了大量修订,并扩大了BSDF的范围,包括一种可准确模拟头发反射的BSDF和另一种可模拟分层材质散射的 BSDF.测量的 BRDF 采用了一种新的方法,可以代表多种材料的反射光谱.   
● *光源采样(Light sampling)* :我们不仅改进了对单个光源进行点采样的算法,以更好地反映当前的技术水平,而且本版本还支持*多光源采样*,这样就可以通过对少数光源进行仔细采样,高效地渲染包含数千或数百万个光源的场景.  
● *GPU渲染(GPU rendering)* :这一版本的pbrt增加了对 GPU 渲染的支持,GPU 的光线追踪性能会比 CPU 快 10-100 倍.我们实现这一功能的方式是,书中介绍的几乎所有代码都可以在 CPU和GPU 上运行,这样就可以在第 15 章讨论 GPU 相关问题.  

该系统还进行了许多其他改进和新增功能,包括新的双线性面片形状(Bilinear Patch Shape),基于蒙特卡罗积分核心的样本生成算法的许多更新,支持在每个像素输出有关可见表面几何形状和反射特性的辅助信息,以及对系统进行的更多微小改进.

## 致谢

Pat Hanrahan 对本书的贡献之大,我们无以言表;我们欠他太多了.他不知疲倦地为简洁的界面和在整个系统中使用正确的抽象而争论,他对渲染的理解和方法深深地影响了本书的设计.他愿意在斯坦福大学的渲染课程中使用pbrt和本手稿,这对我们帮助很大,尤其是在手稿诞生的最初几年,当时手稿还很粗糙;在整个过程中,他的反馈意见对手稿达到目前的状态至关重要.最后,在Pat的帮助下,斯坦福大学图形实验室聚集了一批人,他所营造的开放式环境,为我们提供了一个令人兴奋,激励人心和肥沃的环境.能在那里工作,Matt和Greg都深感荣幸.

1999年至2004年期间,斯坦福大学和弗吉尼亚大学的许多学生在课程中使用了本书的初稿,对此我们深表感谢.这些学生为本书和pbrt提供了大量的反馈意见.特别值得一提的是这些课程的助教: 斯坦福大学的 Tim Purcell,Mike Cammarano,Ian Buck和Ren Ng,以及弗吉尼亚大学的 Nolan Goodnight.这些课程中的一些学生提供了特别宝贵的反馈意见,并发送了错误报告和错误修正;我们要特别感谢Evan Parker和Phil Beatty.德克萨斯大学奥斯汀分校的Bill Mark和Don Fussell以及俄亥俄州立大学的Raghu Machiraju曾在他们的课堂上使用过本书的手稿草稿;他们的反馈意见非常宝贵,我们非常感谢他们在本书仍在编辑和修订期间就大胆地将本系统纳入他们的课程.

Matt Pharr在此感谢在渲染相关领域工作的同事和合作者,他们是他学习的重要源泉,对他编写渲染器的方法和对该领域的理解产生了重大影响.特别要感谢Craig Kolb和Eric Veach,前者通过免费提供 rayshade 光线跟踪系统的源代码,为马特早期的计算机图形学教育奠定了基石,后者也慷慨地贡献了自己的时间和专业知识.此外,还要感谢 Doug Shult和Stan Eisenstat,他们分别在高中和大学期间为马特提供了数学和计算机科学方面的启蒙课程;最重要的是,要感谢Matt的父母,感谢他们一路以来提供的教育和不断的鼓励.最后,感谢英伟达公司对本书第一版和最新版编写工作的支持;在英伟达公司,感谢Nick Triantos和Jayant Kolhe在第一版编写的最后阶段给予的支持,感谢Aaron Lefohn,David Luebke和Bill Dally对第四版编写工作的支持.

Greg Humphreys非常感谢他在普林斯顿大学读本科时所有宽容他的教授和助教.许多人鼓励他对图形学的兴趣,特别是Michael Cohen,David Dobkin,Adam Finkelstein,Michael Cox,Gordon Stoll,Patrick Min和Dan Wallach.Doug Clark,Steve Lyon和Andy Wolfe也督促他进行各种独立研究,他们甚至没有取笑过他一次.有一次,在一次关于长达一年的机器人项目的小组会议上,Steve Lyon气急败坏地吼道:“别再告诉我为什么做不到了,快想想怎么做！”这堂即兴课程让人永生难忘.大一结束后,Eric Ristad辞退了Greg的暑期研究助理一职(暑期还没开始),把他推给了毫无戒心的Pat Hanrahan,从此开始了一段长达十年,跨越东西海岸的指导关系.最后,Dave Hanson教会了Greg文学编程是一种很好的工作方式,计算机编程可以是一种美丽而微妙的艺术形式.

Wenzel Jakob在 2004 年攻读本科期间收到邮寄来的pbrt第一版时非常激动.不用说,这对他的职业生涯产生了持久的影响--因此,Wenzel首先要感谢他的合著者邀请他成为本书第三版和第四版的参与者.Wenzel非常感谢Steve Marschner,他是Wenzel在康奈尔大学度过的充实的五年中的博士生导师.Steve将他带入了研究领域,并一直激励着他.Wenzel也非常感谢图形学小组的其他成员,包括Kavita Bala,Doug James和Bruce Walter,为他提供的指导和令人振奋的研究环境.Wenzel与Olga Sorkine Hornung一起度过了美好的博士后时光,是她将他引入几何处理领域.非常感谢 Olga 对 Wenzel 参与本书第三版工作的支持.

我们要特别感谢审稿人,他们阅读了书稿的全部内容;他们在书稿的不同阶段都对书稿提出了富有洞察力和建设性的意见.感谢Ian Ashdown,Per Christensen,Doug Epps,Dan Goldman,Eric Haines,Erik Reinhard,Pete Shirley,Peter-Pike Sloan,Greg Ward以及众多匿名审稿人为本书第一版和第二版提供反馈意见.对于第二版,我们要感谢Janne Kontkanen,Bill Mark,Nelson Max和Eric Tabellion.对于第四版,我们要感谢Thomas Müller和Per Christensen,他们提供的大量反馈意见极大地改进了最终版本.

许多专家向我们解释了他们工作中的微妙之处,并指导我们采用最佳做法.在第一版和第二版中,我们还要感谢Don Mitchell,感谢他帮助我们理解取样和重建的一些细节;感谢Thomas Kollig和Alexander Keller,感谢他们解释了低差异取样的细微之处;感谢Christer Ericson,感谢他为我们的kd-tree实现提出了许多改进建议;感谢Christophe Hery和Eugene d'Eon,感谢他们帮助我们了解次表层散射的细微差别.

在第三版中,我们要特别感谢Leo Grünschloß对采样章节的审阅;感谢Alexander Keller对该章节主题的建议;感谢Eric Heitz在微面细节方面提供的大量帮助,并审阅了我们有关该主题的文本;Thiago Ize全面审阅了有关浮点误差的文本;Tom van Bussel 报告了我们BSSRDF代码中的一些错误;Ralf Habel 审阅了我们的BSSRDF文本;Toshiya Hachisuka和Anton Kaplanyan 对我们的光传输章节进行了广泛审阅并提出了意见.

在第四版中,感谢 Alejandro Conty Estevez 对我们多光采样处理方法的评论;感谢Eugene d'Eon,Bailey Miller和Jan Novák对体积散射章节的评论;感谢Eric Haines,Simon Kallweit,Martin Stich和Carsten Wächter对GPU渲染章节的评论;Karl Li对一些章节的反馈意见;Tzu-Mao Li对我们关于逆渲染和可微分渲染的讨论的评论;Fabrice Rousselle对机器学习和渲染的反馈意见;以及Gurprit Singh对我们关于蒙特卡罗积分的傅立叶分析的讨论的评论.我们还感谢Jeppe Revall Frisvad对前几版中pbrt处理反射模型的方法提出的大量意见和建议.

对于本版中pbrt实现的改进,我们要感谢Pierre Moreau为调试pbrt在Windows上的GPU支持所做的努力,还要感谢Jim Price,他不仅发现并修复了早期发布的pbrt源代码中的大量错误,还提供了比我们最初的实现更好的色度体积媒介(chromatic volumetric media)表示法.我们还非常感谢 Weta Digital 公司的Anders Langlands和Luca Fascione提供了他们的 *PhysLight* 系统实现,该系统已被纳入pbrt的 **PixelSensor** 类和光源实现中.

许多人报告了以前版本中的文本错误或pbrt中的错误.我们要特别感谢Solomon Boulos,Stephen Chenney,Per Christensen,John Danks,Mike Day,Kevin Egan,Volodymyr Kachurovskyi,Kostya Smolenskiy,Ke Xu和Arek Zimny.

我们还要感谢 Rachit Agrawal,Frederick Akalin,Thomas de Bodt,Mark Bolstad,Brian Budge,Jonathon Cai,Bryan Catanzaro,Tzu-Chieh Chang,Mark Colbert,Yunjian Ding,Tao Du,Marcos Fajardo,Shaohua Fan,Luca Fascione,Etienne Ferrier,Nigel Fisher,Jeppe Revall Frisvad,Robert G．Graf,Asbjørn Heid,Steve Hill,Wei-Feng Huang,John “Spike” Hughes,Keith Jeffery,Greg Johnson,Aaron Karp,Andrew Kensler,Alan King,Donald Knuth,Martin Kraus,Chris Kulla,Murat Kurt,Larry Lai,Morgan McGuire,Craig McNaughton,Don Mitchell,Swaminathan Narayanan,Anders Nilsson,Jens Olsson,Vincent Pegoraro,Srinath Ravichandiran,Andy Selle,Sébastien Speierer,Nils Thuerey,Eric Veach,Ingo Wald,Zejian Wang,Xiong Wei,Wei-Wei Xu,Tizian Zeltner和Matthias Zwicker.最后,我们要感谢 LuxRender 的开发者和LuxRender 社区,尤其是Terrence Vergauwen,Jean-Philippe Grimaldi和Asbjørn Heid;看到他们在pbrt的基础上构建的渲染系统,我们感到非常高兴,我们也从阅读他们的源代码和新渲染算法的实现中学到了很多东西.

特别感谢Framestore 的 Martin Preston和Steph Bruning 的帮助,使我们能够使用《地心引力》中的画面(图片由华纳兄弟和Framestore 提供),并感谢 Weta Digital 提供《阿丽塔:战斗天使》中的画面(©2018 二十世纪福克斯电影公司,版权所有).

## [产品](https://pbr-book.org/4ed/Preface#)

在第一版的制作过程中,我们还要感谢编辑Tim Cox,感谢他愿意承担这个略显另类的项目,感谢他在整个过程中的指导和耐心.我们非常感谢Elisabeth Beller(项目经理),她为本书付出的努力远远超出了我们的职责范围;她将这个复杂的项目控制在可控范围内并如期完成的能力非常出色,我们尤其要感谢她对最终成果质量所产生的显著影响.我们还要感谢Rick Camp(编辑助理),感谢他一路以来做出的诸多贡献.Windfall 软件公司的Paul Anagnostopoulos和Jacqui Scarlott负责本书的组稿工作;他们能够将作者自制的文学编程文件格式转化为高质量的最终输出,同时还能处理我们要求的多种不同类型的索引,我们对此深表感谢.还要感谢Ken DellaPenta(校对)和Jennifer McClain(校对),以及Chen Design的Max Spector(文字和封面设计)和Steve Rath(索引编制).

对于第二版,我们要感谢(Greg Chalson),是他说服我们扩充和更新了本书的内容;格雷格还确保了Windfall软件公司的Paul Anagnostopoulos再次为本书作曲.我们要再次感谢Paul在本书复杂的制作过程中所付出的努力.最后,我们还要感谢Elsevier的Todd Green,Paul Gottehrer和Heather Scherer.

对于第三版,我们要感谢Todd Green和Amy Invernizzi,前者是第三版的监督者,后者则在整个过程中确保了工作的顺利进行.我们很高兴 Windfall 软件公司的 Paul Anagnostopoulos 第三次参与了这一过程;他的努力对本书的高制作价值至关重要,这对我们非常重要.

在第四版中,我们搬到了麻省理工学院出版社;非常感谢Elizabeth Swayze热情地邀请我们加入,指导我们完成整个制作过程,并确保Paul Anagnostopoulos再次负责撰稿.我们衷心感谢Paul和我们一起再出一版,同时也非常感谢MaryEllen Oliver出色的校对工作.

## [在线版本](https://pbr-book.org/4ed/Preface#)

截至2023年11月1日,第四版全文可在网上免费获取.非常感谢麻省理工学院出版社和Elizabeth Swayze对本书免费版本的支持.

许多开源系统对《基于物理的渲染》在线版的开发起到了重要作用.我们要特别感谢[Bootstrap](https://getbootstrap.com/),[JERI](https://jeri.io/),[MathJax](https://www.mathjax.org/)和[JQuery](https://jquery.com/)的开发者.我们还要感谢Impallari Type为我们设计了用于正文的Domine字体;Christian Robertson为我们设计了用于代码的[Roboto Mono](https://fonts.google.com/specimen/Roboto+Mono)字体;以及[Font Awesome](https://fontawesome.com/)字体的设计者.

我们还要感谢通过Patreon支持早期网络版的所有人;截至2023年11月1日:3Dscan,Abdelhakim Deneche,Alain Galvan,Andréa Machizaud,Aras Pranckevicius,Arman Uguray,Ben Bass,Claudia Doppioslash,Dong Feng,Enrico,Filip Strugar,Haralambi Todorov,Jaewon Jung,Jan Walter,Jendrik Illner,Jim Price,Joakim Dahl,Jonathan Stone,KrotanHill,Laura Reznikov,Malte Nawroth,Mauricio Vives,Mrinal Deo,Nathan Vegdahl,Pavel Panchekha,Pratool Gadtaula,Saad Ahmed,Scott Pilet,Shin Watanabe,Steve Watts Kennedy,Tom Hulton-Harrop,Torgrim Boe Skaarsmoen,William Newhall,Yining Karl Li和Yury Mikhaylov.不过,随着第四版的推出,我们已经关闭了Patreon.

尽管该书已发布到网上供任何人免费阅读,但书中的文字仍然是©2004-2023 Matt Pharr,Wenzel Jakob和Greg Humphreys版权所有,采用[CC BY-NC-ND 4.0](https://creativecommons.org/licenses/by-nc-nd/4.0/)许可.书中的图表采用[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)许可,希望在教授图形课程时能派上用场.

## [场景,模型和数据](https://pbr-book.org/4ed/Preface#)

许多人和组织慷慨地提供了场景和模型,供本书和pbrt分发使用.他们的慷慨无价,帮助我们在全文中创建了有趣的示例图像.

我们非常感谢Evolución Visual公司[www.evvisual.com](http://www.evvisual.com/)的Guillermo M.Leal Llaguno,他制作并渲染了圣米格尔的标志性场景,该场景曾出现在第二版的封面上,现在仍在书中的许多人物中使用.我们还要特别感谢 RNA 工作室的 Marko Dabrovic [www.3lhd.com](http://www.3lhd.com/) 和Mihovil Odak[www.rna.hr](http://www.rna.hr/),他们提供了大量在本书早期版本中使用过的模型和场景,包括 Sponza 中庭,锡贝尼克大教堂,以及本版图 [16.1](https://pbr-book.org/4ed/Retrospective_and_the_Future/pbrt_over_the_Years.html#fig:tt-pbrt-v1-v4)中的奥迪TT汽车模型.此外,还要非常感谢Florent Boyer,他提供了用于章节:bidir-method中部分图片的当代房屋场景.

我们衷心感谢Laubwerk[www.laubwerk.com](http://www.laubwerk.com/)公司的Jan-Walter Schliep,Burak Kahraman和Timm Dapper创作了本书上一版封面上的乡村风景图,本版的许多图中也使用了这幅风景图.

Jim Price友好地提供了一些以有趣的体积媒介为特色的场景;这些场景大大提高了该主题的数据.此外,我们还要感谢Beeple通过许可提供了 *Zero Day* 和 *Transparent Machines* 场景,并感谢Martin Lubich提供了奥地利帝国皇冠模型.最后,我们衷心感谢华特迪士尼动画工作室提供制作复杂的 *Moana Island* 场景以及详细的体积云模型.

兔子,佛像和龙的模型由斯坦福计算机图形实验室的扫描库提供.其中的"killeroo"模型得到了Phil Dench和Martin Rezard的许可(三维扫描和数字表现由 Headus 完成,设计和泥塑由Rezard完成).第9章中使用的龙模型扫描件由 Christian Schüller 提供,感谢 Yasutoshi Mori 提供材质球和跑车模型.第chap:bidir-methods章中用于说明焦散的玻璃由Simon Wendsche提供.用于说明次表面散射的头部模型由Infinite Realities,Inc.根据知识共享署名3.0许可提供.还要感谢"tyrant monkey"提供的宝马M6汽车模型和"Wig42"提供的早餐桌场景;这两个模型都发布在[blendswap.com](http://blendswap.com/),同样采用知识共享署名 3.0 许可.

我们使用了 *PolyHaven* 网站([polyhaven.com](http://polyhaven.com/))上的大量环境贴图,用于各种场景中的HDR照明;所有贴图均采用知识共享CC0许可.感谢Sergej Majboroda和Greg Zaal,我们使用了他们的环境贴图.

Marc Ellens提供了各种光源的光谱数据,X-Rite的Tom Lianza则提供了各种显示器的光谱RGB测量数据.我们还要感谢Danny Pascale([www.babelcolor.com](http://www.babelcolor.com/))允许我们使用他测量的色图光谱反射率.感谢 Mikhail Polyanskiy 通过[refractiveindex.info](http://refractiveindex.info/)提供折射率数据,感谢 Anders Langlands,Luca Fascione和Weta Digital提供pbrt中包含的相机传感器响应数据.

## 关于封面

封面上的水彩场景由Lucydreams([www.lucydreams.it](http://www.lucydreams.it/))的Angelo Ferretti制作.该场景的几何图形总共需要2GiB的磁盘存储空间,纹理贴图需要836MiB的磁盘存储空间.在渲染时,场景描述需要15GiB内存来存储超过3300万个独特的三角形,412个纹理贴图和相关数据结构.

## 延伸阅读
Donald Knuth的文章 *文学编程*（Knuth 1984）描述了文学编程背后的主要思想以及他的网络编程环境.开创性的 $\rm{T_EX}$ 排版系统就是用网络编写的,并已作为系列丛书出版([Knuth 1986](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Knuth:1986:MP);[Knuth 1993a](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Knuth93)).Knuth 和 Levy 将 cweb 文学编程系统的实现作为一个识字程序([Knuth 和 Levy 1994](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Knuth1994)).Knuth 还出版了 *The Stanford GraphBase* 中的图形算法集((Knuth 1993b)[https://pbr-book.org/4ed/Preface/Further_Reading#cite:Knuth93b])和 *MMIX* 指令集模拟器([Knuth 1999](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Knuth1999)).这些程序读起来令人愉快,是对各自算法的出色介绍.网站 [www.literateprogramming.com](http://www.literateprogramming.com/) 上有许多关于文学编程的文章,可下载的文学编程程序和各种文学编程系统的链接;自Knuth最初提出这一想法以来,已经进行了许多改进.

据我们所知,以书籍形式出版的其他识字程序还包括一个关于lcc编译器实现的程序,该程序由 Christopher Fraser 和 David Hanson 编写,并以《可变目标C编译器:设计与实现》([Fraser 和 Hanson,1995 年](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Fraser95)).另请参阅 Hanson 关于程序界面设计的书([Hanson,1996 年](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Hanson1996)),Mehlhorn 和 Näher 关于 LEDA 库实现的介绍([Mehlhorn 和 Näher,1999 年](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Melhorn1999)),Valiente 的图算法集([Valiente,2002 年](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Valiente2002))以及 Ruckert 对 *mp3* 音频格式的描述(Ruckert,[2005 年](https://pbr-book.org/4ed/Preface/Further_Reading#cite:Ruckert05)).

### 引用
Fraser, C., and D. Hanson. 1995. *A Retargetable C Compiler: Design and Implementation*. Reading, Massachusetts: Addison-Wesley.  
Hanson, D. R. 1996. *C Interfaces and Implementations: Techniques for Creating Reusable Software*. Boston, Massachusetts: Addison-Wesley Longman.  
Knuth, D. E. 1984. *Literate programming. The Computer Journal 27, 97–111*. Reprinted in D. E. Knuth, Literate Programming, Stanford Center for the Study of Language and Information, 1992.  
Knuth, D. E. 1986. *MetaFont: The Program*. Reading, Massachusetts: Addison-Wesley.  
Knuth, D. E. 1993a. *$\rm{T_EX}$ : The Program*. Reading, Massachusetts: Addison-Wesley.  
Knuth, D. E. 1993b. *The Stanford GraphBase*. New York: ACM Press and Addison-Wesley.  
Knuth, D. E. 1999. *MMIXware: A RISC Computer for the Third Millennium*. Berlin: Springer-Verlag.  
Knuth, D. E., and S. Levy. 1994. *The CWEB System of Structured Documentation: Version 3.0*. Reading, Massachusetts: Addison-Wesley.  
Mehlhorn, K., and S. Näher. 1999. *LEDA: A Platform for Combinatorial and Geometric Computing*. Cambridge: Cambridge University Press.  
Reinhard, E., G. Ward, P. Debevec, S. Pattanaik, W. Heidrich, and K. Myszkowski. 2010. *High Dynamic Range Imaging: Acquisition, Display, and Image-Based Lighting*. San Francisco: Morgan Kaufmann.  
Ruckert, M. 2005. *Understanding MP3*. Wiesbaden, Germany: GWV-Vieweg.  
Valiente, G. 2002. *Algorithms on Trees and Graphs*, Berlin, Heidelberg: Springer-Verlag.  