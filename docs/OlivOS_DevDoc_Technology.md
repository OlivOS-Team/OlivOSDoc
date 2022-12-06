# 技术选型

## 主用语言：Python
本项目的主要编程语言使用`Python`，这一点时从项目早期就已经决定的，在此之前，曾被纳入考虑范围的编程语言与其没有被最终选择的原因如下：  

> 注意：以下观点仅站在OlivOS开发的实际需求角度，并不能普适的表明任何对于各种编程语言及其相关技术与人群的评价。

### 未选用`C/C++`的原因
`C/C++`曾是酷Q时代较为热门的编程语言之一，并且是本项目组的初创团队在开始OlivOS项目前实际使用最多的编程语言。  
无论如何，C/C++都是任何领域的任何开发者都无法绕开的庞大集合，它功能强大，贴近底层，并且语法灵活。  
但这一切的代价则是，一旦想要用它实现一个长期公共项目时，  
**对于社区：**它需要基于冗杂的维护规则与大量的共识，主流编译器的碎片化也导致了社区进行二次开发与发布时将带来碎片化的传导，而其不存在事实上通用且统一的包管理机制则加剧了这个过程，而其基本没有开源且成熟的Web生态则更是让其不适合在新时代中独立开发面向用户的应用项目。  
**对于开发者：**它需要开发者个人拥有较高的编程水平，它并不是最易于学习的编程语言，且通常情况下，即便是本科学历的相关专业毕业生，也很难说得上拥有合格的水平，且对于普通人而言过于复杂的开发环境配置更是拉高了学习成本，将使得多数初学者望而却步，这将导致项目难于应用和推广。  
**对于创始人个人：**仑质的本职工作中会大量使用`C/C++`且涉密，所以需要在业余生活中尽量避免使用同类语言。  

### 未选用`易语言`的原因
`易语言`曾是知名机器人框架酷Q的原生编程语言，尽管其在众多的`资深开发者`眼中难登大雅之堂，但它确实是许多华语地区的编程初学者最早能接触到的语种之一。`易语言`自有编译器与IDE，且运行速度**较快**，借鉴于`Visual Basic`的控件事件架构下的GUI编程也显著降低了初学者入门的难度，且由于早期IM机器人领域的行业实际情况基本与外挂等灰色甚至黑色产业类似，所以在事实上，`易语言`在基于`Java`的开源第三方`QQ`协议端`Mirai`出现前一直占有主导地位。  
但也正因为长期受到灰产与黑产的青睐也导致了其编译的二进制文件**经常成为杀毒软件攻击的目标**，这将极大的阻碍最终成品的传播；且通常情况下，它也不具备独立进行现代Web开发的能力，最终还是将会依赖其它语种的生态（例如JavaScript）完成核心功能的实现；除此之外，由于其编译器与IDE为**闭源**实现，让人难以看到其未来发展的可能性，**且可能产生知识产权方面的纠纷**，故而不予选择。  

### 未选用`Java/Kotlin`的原因
尽管出现了很多新的编程语言，但是`Java`的名声从未下降过。大多数专家都不能否认`Java`是最强、最有效的编程语言之一，并且在众多领域广泛应用。  
而在基于`Java`的开源第三方`QQ`协议端`Mirai`出现后，`Java`则更是又一次被推到了IM机器人领域首选的地位；截至目前，本领域内已经有大量优秀的基于`Java`的项目。  
然而，虽然`Java`最初是设计用于嵌入式设备的编程语言，但是随着编程技术的逐年发展，`Java`更广泛的被用于企业级的大型后端服务项目，而IM机器人往往与这类用途存在重合，这就导致了运行于云服务器的IM机器人最终成品往往都表现出臃肿的形态，且对硬件资源的使用缺少节制，这将对用户提出过高的要求，从而导致项目的推广受到阻碍。  

### 未选用`C#`的原因
`C#(C-Sharp)`是由微软主导的，基于`.Net`框架的编程语言，其语法与`C/C++`类似，且得益于其诞生较`Java`更晚，其在设计之初就在整体上比`Java`更成熟（当然，这也是具有时代性的结论）。得益于微软的强力支持，它提供了大量的功能支持与接入，让开发更为高效，且其基于`C/C++`进行实现的，也决定了它在性能开销上有着良好的表现，所以实际上，即便在酷Q时代它也有着甚至高于`Java`的活跃度。  
但是，也正是由于其主导者为微软，所以`C#`的跨平台较为繁琐，且没有较好的保证。并且由于它实际上的主流应用领域高度垂直（Windows平台的桌面应用与Web服务），其技术迭代也很难与后续的项目发展方向契合，站在`OlivOS`开发的角度，它并不适合用于进行长期跨平台项目的开发与维护。  

### 未选用`JavaScript/Node.js`的原因
在浏览器领域`JavaScript`已成为事实上的唯一选择，随着`Html5`与`Electron`的普及，它也逐渐成为桌面应用市场的主流，而这一切都要归功于`Node.js`。`Node.js`是一个基于`Chrome JavaScript`运行时建立的平台，`V8`引擎本身使用了一些最新的编译技术，这使得用`Javascript`这种脚本语言编写出来的代码运行速度获得了极大提升，又节省了开发成本。且虽然`Node.js`本身较为年轻，但是由于其开发者社区较为“狂热”，所以涌现出很多优秀的库与项目。  
但也正是因为如此，其作为服务端的应用仍然需要更多的考验，由于其快速迭代的特性，导致其各类第三方库的质量参差不齐、向下兼容性也较差，并且由于`JavaScript`单线程的原因，其在性能上也存在较为严重的瓶颈。  
更主要的原因是，对于创始人团队而言，`JavaScript/Node.js`被用于另一个同类项目，所以不作优先考虑。  

### 未选用`PHP`的原因
`PHP`是一个历史悠久的后端编程语言，其在后端编程领域有着相当垂直的应用。`OlivOS`的原型版本实际上就是采用`PHP`进行编写的，由于`go-cqhttp`以及`onebot`项目的存在，基于`Nginx`使用的`PHP`对调试与开发带来了很大的便利，使得不需要复杂的架构就可以实现一个高可靠的应用。但这种实现方式并不具有可移植性，并且若是要将其作为产品分发，也并不现实，总体而言，由于`PHP`的应用场景过于局限，所以其基本只在项目初期作为原型研究的工具，而被排除于最终的项目选型之外。

### 未选用`Go`的原因
`Go`是一个非常年轻的编程语言，以至于直到2020年`酷Q`时代落幕时，社区中都几乎没有与之相关的内容。很长一段时间里，会优先考虑`Go`作为项目主用语言的通常是初次进入这个领域的新手。当然，这个现象随着`MiraiGo`与其事实的可用项目`go-cqhttp`的推出而得到了极大的改善。  
`Go`是一个适合云计算场景的编程语言，这在未来的使用中将会给出很多新的可能性，但也由于其过于年轻，这导致了它的生态即便在现今的节点上看依旧略显单薄，缺少完善的原生UI编程能力使得它对用户的设备提出了新的要求，也将引入许多无法回避的新问题，使得项目的开发周期被显著延长；而对于WebGUI的选项，任何编程语言都不会比`JavaScript/Node.js`更自然，更何况`Go`的生态中依然缺少一个成熟的Web框架。综合考虑，`Go`显然是一个不够好的选择。  

### 选用`Python`的原因
选择`Python`的首要原因是其众所周知的较低的上手难度，这将**对社区发展带来决定性的优势**。  
其次，`Python`诞生于上世纪90年代，于2000年前后整体稳定，并在2008年发布最后一个带有破坏性变化的版本`3.0`，此后`Python`的主流解释器`CPython`已经基本趋于稳定与可靠。且由于其从设计之初就是基于具有共识的解释器与标准为基础的，所以它几乎不存在由于各自运行时实现差异导致的社区碎片化问题；历史悠久却又不缺乏活力的社区使得`Python`拥有海量的可选的库，这为项目的**复杂性**提供了保证。  
除此之外，`Python`与其它语言在互操作上的便捷性，使得其拥有“胶水语言”的称号，这给项目的**扩展性**提供了保证。  
在此基础上，随着近些年人工智能的兴起，由于谷歌将`Python`作为了项目的主要语言，这使得`Python`毫无疑问的被推到了史无前例的高度，其在人工智能领域的大放异彩意味着它将有更多机会跻身于更多的新技术之中，这将自然而然的给使用`Python`进行开发的各类项目带来新的**可能性**与更广阔的应用场景。  
除此以外，由于近年来`Python`基金会在相关项目上的努力，`Python`的提速效果也相当明显，加上还存在`Pypy`这样的衍生项目，并可以在相当多的领域使用`C/C++`扩展进行提速，`Python`作为解释型语言的**性能瓶颈也将在可以预见的未来得到解决**。  
综上，`OlivOS`选择`Python`作为主要开发语言几乎成为了必然。  

### 反正你最后就是要吹`Python`呗？
> *反正你最后就是要吹`Python`呗？*  
**是的。**