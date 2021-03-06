# C²engine2
*本文档仍在写作中......*

## 简介
一个外部接口极简，而内部实现却并不简单，使用链入方便，最小化外部依赖的交互图形二次开发技术平台框架。关键实做功能会成为Extensions可插拔，但并不追求build后包（模块）的“热”插拔，脚本层面会自然能“热”。

## 1 Features of C²engine2（FIXME：本章节内容是旧的，暂请忽视）
- 数据驱动，为逻辑减少耦合，也可以更方便多线程/进程/多物理机联合的体系。
- 模块边界清晰。层次清晰。
- 用户接口精简。
- 尽可能的stateless。
- occlusion culling。
- voxel平滑。
- terrain。
- 材质模板化，二次元/物理海浪/3A材质等。
- 并不打算面向普通用户提供shader扩展或者material编辑工作，因为这不符合用户画像，做出来也是个半吊子。而且现在shader规范还没有形成国际统一标准。不过会给有经验开发人员提供扩展shader的机制，就如同我们的Director里的Action/Unit的扩展机制一样。
- Director with NLP ready。
- ？坐标空间扭曲后处理。逻辑坐标、重力方向、形体。
- 支持webassembly。
- webgl/webgpu/vukan/directx12图形API无关。
- 三角图元优化。
- DR/MRT/PBR/GI/OA。
- Click-to-Play/streaming。支持unity3d/cocos/ue等任意产品。

## 2 提示和注意
- 本框架伪代码暂时未考虑线程安全。
- ？Spacetree的优化问题。转化为二叉树优化遍历，对我们有用吗

## 3 模块关系
- 依赖关系是一个DAG，尽量按近似树的方式进行绘制，方便人类脑理解。
- VS的Hierarchy、File Layout等很有用。基于Doxygen的Code Graph扩展还没试。
- 围绕github生态的工具里，可能有比较好的，例如看看sourcegraph.com等好不好。
- 可以从UML标准官网作为线索去找。

## 4 约定和设计，Thinking In Nature
### 4.1 思维范式（paradigm）与方法
- Simpler, simpler and simpler! More simple, more easy。要考虑团队成员良莠不齐这个现实问题。让团队和用户都能简单工作，玩花样没有实际意义，务实。能少定义个东西就少定个东西，能少绕个弯就少一个，用最直接了当的方式来写，例如函数少有返回值，成功失败返回值不要错误码。不要炫技，返璞归真，务实务实。代码写的精炼，就像早期追求极简代码成就强大功能的geek精神（不过我们反对使用于人类脑可读性差的寻章摘句层次的奇淫巧技），深入浅出。以前有个KISS原则。
    - 一切设计上的花拳绣腿、奇淫巧技、又或高超智慧（sophisticated）皆无他，唯“高内聚低耦合”六字尔。1）职责单一，边界清晰；2）扩展避免契约冲突；3）依赖关系（h文件、包的依赖）最好是个TREE，顶多只能是DAG，绝对不能有环，i.e.具象和抽象不可倒置；4）内部对外界尽量故意所知甚少；5）对外暴露的东西越少越好，接口极简，内部复杂；6）开闭原则。
    - 不要死板的拘泥于什么面向过程、面向对象、面向切面、函数编程、设计模式、XXX框架这些教条，别像以JAVA界一些coder走火入魔，随时提醒软件产品目标导向下的六字真言为第一性原理。

- 复杂性的来源主要有两个：代码的含义模糊和互相依赖。A复杂性隔离；B接口要简单，实现可以很复；C错误内部处理，减少抛错。
- 无论是概念，还是函数接口用法，消除二义性、歧义。做某事，尽可能不要给用户多的选择。
- Glossary里的概念也尽量按最朴素的无专业领域人为污染的自然思维来进行定义。
- 尽量用C方式写，而不是C++。就像go语言，把一整个代码文件看成一个类，内部函数用static，暴露给外面的才放到h中。
- Engineering Structure: Human-brain & Code Architecture。
- 架构设计，可以自上而下，先写软件产品使用文档（就像产品已经开发完成），然后再确定接口乃至写接口文档，后对接口编程。这里的接口是个广义的概念，泛指对外暴露的东西，UI也是一种对外暴露的东西（故产品的用户体验设计也本质一样）。Graph仅适用于领域建模阶段，领域阶段主要是思路，用类图（容易让人定势思维的什么东西一上来都搞成一个教条主义的类）、脑图都行，动态视角建议多画画是时序图理清逻辑脉络，而具体建模还是用coding的方式更能入戏。程序逻辑脉络就像一根一根的线或者绳子，逻辑的线不理清楚，就同我们去解开缠绕严重的线团一样非常费劲，90%的时间和成本可能都并非用在功能开发往前走上，而是“理线”，甚至在“死疙瘩”上打转。再加上测试覆盖率低，无有效回归测试，甚至导致大型项目走两步退三步。

- 抽象、具象是相对概念。在抽象层尽量抽象，但也可有能够接收具象参数的接口，具象层重载的接口内就可以decode/parse具象层的参数。这个参数可以是jason等字符串甚至脚本。这不是什么OO思想，GPU可编程也就是这么回事。

- 无状态化编程。滥用类是一个让状态化横行的原罪，类本身当然也并无罪，罪在滥用，但这个滥用确实来源于类这种事物的潜在误导，滥用类成员变量的危害性质同全局变量的危害其实类似。如果一个语言没有类，只有结构体（实体类）、方法聚合（行为类），是不是就可以逼着新手没法滥用？GO等语言有接口但无方法聚合。

- 。。。。。。关于线程问题。Lock-Free、OneRoute、状态和状态变化的触发、原子操作、transaction等话题。。。。。。

### 4.2 基本理念
- 分为用户态、核心态，用户态层面完全避免多线程编程。不过尽管如此，但内核态为了利用多核CPU或者其他原因而仍然会用到多线程。注意，后端应用可以通过多进程的模式来利用多核CPU，而不必一定多线程。
 
- 只有两种被使用的方式：A被调用；B被继承。B继承会让代码阅读头大，因为当你看着一个继承类时，潜意识会想：我靠，基类里是不是还有哥不知道的东西，阅读代码还得翻来翻去的看，真累。而A中的调用基本形态是很朴素简单的，但因为OO所谓的成员变量方法的封装范式，使得对象元定义时（就是指“写类”的时候，但我不喜欢“类”这种多余人造概念，让人脑子想问题多个“指代弯”而不直观），又出现了关联关系。
  - Part互相之间的关联关系：包含、引用、继承，同OOD的概念类似。“引用”（学习tinyxml以link为操作方法的动词）、“包含”（？Insert为动词）在运行期对应着前面的A被调用。其中“引用”意味着“实例”内数据会在别处被修改，所有权不排他。我们的关联关注重点同UML的OO定义有所差别，按UML中用到的OO定义的关联关系，无论是成员还是成员函数参数所导致的关联都是关联：l依赖r（虚线线式箭头）、l继承自r（空心三角箭头，接口实现为虚线）、l组合成r（实心菱形箭头），被箭头所指的都是上面动词的rhs。插座线表示法（使用，母头我要我要被插）、棒棒糖表示法（提供，公头表射出）。仅只是个指针的（我们称为引用），不视为有关联关系，因为无需知道其内部细节，对其操作是通过他的父元对象（父类）或者接口级所定义的借口，如存在相关箭头那么应指向其父元对象或者接口。“包含”尽量直接用实例以及&引用，若是指针（有能让h文件能隐藏相应元对象的define，对高效编译有利）。
  - Part零件元对象（类）兼具着可被继承，同时也是“引用和包含”行为的客体和主体。而Node（继承于Part）兼具这些特性的前提，而断绝了被继承的可能，实质相当于Unity3D的GameObject，胶水黏着物的底料。
  - 本来在GO等现代语言中，有舍弃继承特性的趋势。而我们仅为了类脚本语言操作而仍用到了继承（于Part，从这一点看Part很像很多工程里的Object，便于序列化、字符串化的创建、属性等)。针对被继承性质的抽象类，放入meta中，极力避免“继承”这一特性污染我们的工程。
  - 关于B继承式，一般是供C类用户使用，开发Extension（比Addons用词更恰当，注意同Utility的区别）是一种特殊的使用模式，我们自己团队部分成员也可以视为C类用户。被用户态继承的类，尽量都只是一些纯H全/半接口。

### 4.3 理清容易迷糊的概念
- Part可成为别人的零件。有两种方式被造出来，一种compile期coding写的，另外一种是运行期由别的零件构建出来。

- Part都可以被重用、并且序列持久化。
 
- Asset、Prefab、Templet等概念在Runtime里并不存在，Unity3D里好像也是如此。在应用开发工序流中存在的一种术语。

- Node可以胶水联合上任何Part，例如某个Part也可是个自成体系的某种Space（下面叫做XSpace），从而实现各种不同的空间管理XSpace，通过Node融入到基础空间管理Spacetree中来。而XSpace内部又可能有自己的不可被继承，适合其自身空间管理算法的胶水黏着物的底料，例如叫做XNode。例如Terrain/BSP/骨骼动画/高级组合模型（似乎可以同骨骼动画等同）等都会有自己的Space管理办法和Node需求。但当前情况下，编辑器内暂时只支持SpaceTree的编辑，虽然骨骼动画、Terrain等某些Space也能同SpaceTree的树结构近似甚至兼容（以至于似乎可以直接展开在Space的Tree中），但为了概念清晰并且统一，在我们没有完全想清楚利弊之前，暂时不要把非SpaceTree的Space在SpaceTree中展开，而是在Spacetree中把它看作某Node的Part而已。对不同种类的Space编辑，就算也能看成一棵树的Space，但宁愿是从基础功能层面和Spacetree用同一个编辑器功能去实现而也不能展开到Spacetree树编辑器中去。这一点同Unity3D不同，Unity3D是可以把骨骼动画展开到场景树的——其实这没有实用性空混淆了概念。

- 在Unity3D中，Prefab同Scene没有实质区别，所以在我们这里概念统一为Spacetree，Spacetree的一部分可以被存储起来（相当于Prefab），Spacetree里面实际上就是Node组成的树。
    - 关于Uniy3D中“Prefab”（我们还是叫Spacetree）同来源实例及使用入"Scene"内的实例之间的关联关系，好像是一旦Scene内的实例自行修改过值后，Prefab数据才会断开联动，否则Prefab的修改会带动Scene内的实例修改。也就是说，似乎有一种隐性的从包含关系到引用关系的转变。
    - 于此我们同Unity3D的规则也不完全相同，我们沿用New3D时代的Cluster节点性质，生成Prefab（Spacetree）时，实际上是从原Spacetree树的一个分支作为源进行拷贝整个分支的所有节点成为一个“Spacetree树枝预制件”，同之前的“源树枝”就再无关系。
    - 后面被使用，会有两种性质的使用操作，一种是作为一个Part零件黏合到别的Node或者Part对象上去，其本身可作为一个整体，是一种引用关系。一种是往某“新Spacetree”中放入，这相当于是把此“Spacetree树枝预制件”做了拆解操作而拷贝融入“新Spacetree”中，与“Spacetree树枝预制件”再无关系。

- 层次及概念：

|Layer			|Buzz
|:-------------:|---------
|uBuild			|FSS/BSDK<br>TARGET: Wechat/GF/Facebook/Browser/iOS/Android/MacOS/Windows/AR/VR
|Editor			|Creator/Director/Gizmos/Templet/Asset/SpacetreePrefab/Extension
|**LIVELESS**<br>*CG Presentation Layer*|INFO COLLECTOR: Navigation/Placemark/SketchSampler/ReflectProbe<br>GEOMETRY: Spacetree/Mesh/Voxel/Terrain/Skeleton/UI/Sketch/Line/Point<br>VISUAL: Particle/Flare/Fog/Halo/Sky/Surface/VolumeLight/Airwave/LOD/Projector<br>WORKS: Wave/Cloth/Hair/Fluid<br>CONTROLLER: Animation/Physical/Camera/Portal/OccVolume/Transform/Conspos<br>RENDERING: DLS/HDR/Bloom/DOF/GI<br>RENDER BASICS: *RPath/RChunk/RDevice/Material*<br>AUDIO: *AudDevice*/SFX/BGM
|**ALIVE**<br>*Presentation Layer Independence*|Activity: Factory/Signal/Observer<br>Command: Arbitrator/BT/Action/C#/Python/Lua/Java/GO/NLP
|BASICS<br>*Presentation Layer Independence*|Stream: Filestream/Netstream<br>Math: Matrix/Interplation<br>Debugger: Logger/Dump/UnitTest/Console
|META<br>*Presentation Layer Independence*|Part/OneRoute/Serialization/Device/IOStream
>- Space：挂接到某空间区域根节点，具有自己特定的阻力、重力加速度、浮力等模拟不同空间的物理特性。模拟水下、外星球等。
>- Surface：水面等表面，自动产生边缘浪花。
>- META：并不会直接作为API来使用，顶多只有C类用户在制作Extension时用来继承。
>- CONTROLLER：自身视觉上不会绘制呈现。
>- DLS：Dynamic Light and Shadow。
>- R：Render。
>- Aud：Audio。
>- FSS：File Stream Service。
>- uBuild：User Build。
>- BSDK：Blockchain SDK。
>- OccVolume：辅助用来体积用来快速进行遮挡查询，同Portal一样用来进行。
>- LIVELESS：非斜体的一般都是用来形成各种公用的Node的Part（Unity3D里的Component）的，这些Part都能被序列化，并且还会引用文件形态的更复杂的数据，此种数据文件在Editor层称为Asset。
>- INFO COLLECTOR：用来收集数据或者信息，例如供交互逻辑等使用，收集调试信息的也可暂时归为此类。
>- Arbitrator：各自为政，自私的考虑自己的订阅处理。但遇到冲突需要有仲裁——自然思维编程。
>- Action：是BT里惯用的概念，故我们沿用。在Activity层面，执行的行为用Command这个词来抽象。Command的实现可以是C也可以是任何脚本来实现的，也可就是一棵BT。

### 4.4 CGS
- Package、Module等抽象概念，各人内心自定义的含义都不一样。这种模糊的用词并不科学，实质也无非想表达段落层次。但是现实中一般人对层次的理解能力局限于"事不过三"，顶多四（这也是我们组织内万事都惯归纳为ABCD的原因），故仍可选用朴素的自然思维的生活中名词来规范组织内术语。我们使用包的概念，包可以有路径，也就是说包可以有层次，但是为了简便起见，在我们自己项目的范畴，也并不打算把包同多层namespace挂钩。C海关（Customs）级包及逻辑关口（接口Interface）、G国道包及逻辑流、S省道包及逻辑流。再往下X县道包、Y乡道包等更低层次就不讲了，对理解整体脉络影响不那么大，就如同来到一个新城市，记住几个主干道就可在人类脑中快速建立坐标系形成格局脉络。
    - Runtime内有CGS级，C级就是Runtime供外部使用的海关级，就像IC里的引脚清晰可见对于IC工程过程非常关键。
    - 而Creator虽然没有明显的C，但是内部也是存在GSXY等级别逻辑流体系的。
    - 相关函数、类（结构体）、接口、变量、枚举、宏等都加上相应级别大写字母进行标示，但C级稍例外。原因一是为了避免上层应用层（用户态）开发者不适应，而是因为此级别设定是为了我们内部的代码可读性以及团队人类脑工程协调，具体约定请见下。

- Runtime有些模块及接口是上层应用程序（用户态）若需要某项功能而必须使用的到的，某些未必。
    - 对于前者才是真正的C海关级接口，定义他们的头文件名及目录名加上小写c2前缀以明示，接口和函数也是小写c2打头，类、结构体和宏是大写C2打头，而C前缀词省略。C海关级使用C2Interface修饰。
    - 后者（例如一些内部数学函数、mempool等），我们并不主动推荐使用，能够在C语言层面开发的人一般都有自己熟悉的一套库，不要给别人太多信息以及选择，未必是好事。当然作为功能辅助库，他要用也是可以的，反正我们的包间依赖关系都需要尽量低耦合。为用户使用方便起见，C海关级不纳入c2的namespace。
    - Event体系通过考量还是归位海关级，虽然C类用户肯定有自己使用某种Event体系的习惯。但是毕竟我们整套系统是时间驱动状态变化的方式（但在复杂的GamePlay层面，我们并不推崇违背低耦合理念的FSM来触发状态的变化，而是建议运用决策树性质的Behaviour Tree的方式）。

### 4.5 包、目录、代码等命名及书写规则
- 一个包常常是一个目录，但是如果此包内存在C海关级对外接口，则在目录平等处放一个专门仅定义对外接口的文件。否则不用。
- 项目的逻辑结构同磁盘物理路径结构。
- 尽量不要使用泛型编程。
- ？命名中含有直属上一层种类命名的缩写。
- 每行不超过80个字符；使用四种分隔符；说明初步定为使用doxygen格式；代码+注释方式代替代码设计文档，接近文学编程。

### 4.6 文件体系

## 5 Organizational process glossary(A draft of WIKI)
- 签名（英文是？）。

## 6 Quick Start
### 6.1 Build
#### 6.1.1 第三方库及工具
##### 6.1.1.1 第三方库
- 包括字体、物理（Physx）、纹理资源、导入库用到文件格式（图片，FBX模型等）、图形库、声音处理、压缩、网络协议kcp、光影及空间算法（lightmapper、nvmesh、waveworks等）、一般算法和容器（插值、数学、内存池等）、脚本环境（mono）、基础库（stl等）、调试（Log等）、序列化持久化（Protobuf、hocon等）。。。。。。。。。
Boost/Nuklear/Angle/GLFW/Lightmapper/Physx/Works/Json

##### 6.1.1.2 第三方工具
- 调试、发布

#### 6.1.2 Visual Studio Community 2017

#### 6.1.3 Emscripten

#### 6.1.4 CMake

### 6.2 Try in Console

### 6.3 Check examples

### 6.4 Try the Creator

## 7 Externsion
- 我们有三种用户，A终端用户，指用户B所开发产品的用户；B使用我们平台做二次开发的用户；C扩展我们平台的用户。本章节仅需要C类用户才关心，我们内部很多同事也可看作C类用户，让他们在用户态层面工作。注意同Plugin概念的区别（按我们习惯，所谓Plugin只是以我们的软件为启动器而已），暂时我们不考虑Plugin。
### 7.1 Logger extension
### 7.2 Asset file Importor extension
### 7.3 Shader extension
### 7.4 Renderpath extension
### 7.5 Renderdevice extension

### 7.6 Part extension(Visual FX, Sound FX, and etc.)
- C类用户可能会继承c2::Part，Part仍旧放在Metas目录下，由c2Factor.h隐式使用。Part尽量保持是一个纯H半接口。

### 7.7 Script extension
### 7.8 Debugger extension

---
---

# DevLog
## 要点备忘
- Creator可以同Runtime无关吗？或者说我的Runtime可以很容易封装一个cocos的实现不？当然也可以吧u3d/unreal等作为基座？
- 类间的逻辑流关系外，还存在横向关系，为了减少横切代码。AOP日志记录，性能统计，安全控制，事务处理，异常处理等等。AOP就是行为类？
- 脚本的支持：python/c#/lua
- 如何进行模板化
- 区域
- 单元测试。每个模块的test试验装置，我设计好接口后，就开始弄Makefile，然后写单元测试，科学的明确各包工作目标。其他人基本只用写C/CPP。ps.teamviewer的insider build把免费用户当做测试员。
- 数据库事务的几个特性：原子性(Atomicity )、一致性( Consistency )、隔离性或独立性( Isolation)和持久性(Durabilily)，简称就是ACID。
- 性能调试
- 界面的command
- voxel 是一种node。
- GPU vertex同voxel关系。
- tree（二叉加速？）/dag快速遍历
- vfs补充云存储？ipfs。wasm下的文件访问。
- terrain？
- verbose模式
- 配置文件体系。使用hocon？
- log体系。
- utf8。
- 和unity3d一样的编辑。不了解的部分还包括GUI、API。。。？
- 前后端心跳包。同步、异步。
- 文档的生成。
- OpenEXR。
fire/用户态、核心态、组件构建树。
- 脚本层的封装，需要批量化么？如果量不大，最好是手工严格管控，不要批量。而且为了清晰，应该弄一个类似gateway性质的在一个地方集中注册，严格把控暴露的接口。并且结合各种脚本的包管理机制。
- FONT。阿拉伯、泰语、藏文等。

## 实施步骤
- s1 建立wasm+微信（pc&手机）运行环境。
- s2 数据层、数据结构、及Index方式。
