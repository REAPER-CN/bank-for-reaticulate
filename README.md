# bank-for-reaticulate
## 简述

该仓库用于整合适用于 Reaticulate 的 *.reabank* 文件。

Merged *.reabank* file for Reaticulate. 



## 什么是 Reaticulate

**Reaticulate** 是一款适用于 Reaper 的演奏技法（表情）映射扩展，通过该实例扩展了
Program Change（MIDI 的 Bank/Program Select）功能，将支持 Keyswtich、CC 控制技法的音源用高效的图形化界面加以陈列，并允许用户进行编辑。

官方页面：https://reaticulate.com

一般地，我们可能会遇到如下几处痛点：

- 原生 Reaper 不具备类似 Cubase 的表情映射功能
- 通过 Keyswitch 来切换技法，我们经常需要上下翻动钢琴帘，点出技法之后再翻回去，很麻烦
- Reaper 的 MIDI Note 执行逻辑类似于 FL Studio，它们是由上至下的（高位优先），会导致设置于底部（例如 C0-C2 区域内）的 Keyswitch 音符总是需要提前一点点才能正常地发挥作用
- 对于播放演示，在钢琴帘中出现额外的控制音符会打乱布局，显得混乱，不够整洁
- 常用的 Keyswitch 几乎都是单次触发（One-shot）的，对于电吉他音源（例如 Ample Sound 的产品），如果你在曲子末尾用到了 Slide-out，然后将走带位置跳转到歌曲中间某处，可能接下来播放的音符全在魔性地 Slide-out
- 等等……

那么，我们在使用 Reaticulate 这套扩展之后，上面遇到的问题都将得到解决。



## Reaticulate 的安装向导

该向导的知乎链接：https://zhuanlan.zhihu.com/p/38166538

- 为 ReaPack 添加源：Extensions - ReaPack - Import a Repository
- 复制这条链接然后粘贴到对话框内：

```text
http://reaticulate.com/index.xml
```

- 在源列表中双击 Reaticulate 条目
- 点击 Install/update Reaticulate 按钮，然后选择 Install all packages
- 安装完成后，通过 Action List 检索以下词条以运行：

```text
Reaticulate_Main.lua
```

- （进阶）你可以在 SWS 选项中的 Global Startup Action（全局启动时运行命令）添加上面那条 .lua 脚本

运行之后就会弹出一个单独的界面，再把它停靠到一侧就可以开耍了。



## 如何获取与安装本仓库整合的 .reabank

- 下载 `Release\` 目录下的 *Reaticulate.reabank*。
- 在 REAPER 中，访问该指令 `Options - Show REAPER resource path in explorer/finder...` 以打开 REAPER 的资源目录。
- 转到 `Data\`，覆盖同名 *Reaticulate.reabank* 文件即可。
- 您需要在 Reaticulate 面板中刷新一遍才能拉取最新的变动。



## 进阶 - 如何编辑 Reabank 文件

下面的内容供贡献者参考。

### 在本仓库提交的规则

考虑到 *.reabank* 文件是唯一的，您应在 `Release\` 目录下提交整合的 *Reaticulate.reabank*。

为方便管理第三方 bank 数据，TsunetakaRyu 创建了额外的目录叫 `Assets\`，可以在里面单独地存放这些数据。

### 基本格式

注意：GO 语言仅作为语法高亮化使用，实际格式为常规文本文档。

```go
//! g="Three-Body Tech" n="Heavier 7th Strings"
//! m="Set patch to UACC"
Bank 70 65 H7S - Basic Articulations
```

> *g*：分类，*n*：产品名称。一般地可以将 *g* 作为开发商，*n* 为具体音源名；或者将 *g* 作为音源名，*n* 为各个部件名（例如多声部乐器比较适用后者）
>
> *m*：声明该音源的技法遵循 UACC 标准（这个标准严格定义了诸如 Legato、Staccato 应该分配到的技法编号），但其实无所谓的……
>
> 注意 `//!` 是参与整个配置文件解析的。

```go
//! c=legato i=note-whole o=note:29
29 Repeat Normal
```

> 这是一个典型的技法组件参数。
>
> *c*：用到了 legato 这组颜色
>
> *i*：技法的图标
>
> *o*：技法对应的音高序号
>
> `29 Repeat Normal ` 对应的是 `UACC-序号 技法名`
>
> 关于 *c* 与 *i* 参数的样式，可以参考这个链接的末尾：https://reaticulate.com/reabank.html



重点说一下 *o* 参数的特殊用法（下面用 `//` 作为注释内容）：

```go
o=note:29,1 // 代表的是音高为 29，力度为 1 时触发的技法
o=note@2:29,1 // 代表的是使用第二个通道，音高为 29，力度为 1 时触发的技法
o=note-hold:29,1 // 代表的是“按住”这个音高
o=cc:32,20 // 代表的是 32 号 CC 事件值为 20 的时候触发的技法
```


