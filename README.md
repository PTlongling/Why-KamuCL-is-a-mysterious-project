# Why-KamuCL-is-a-mysterious-project
一个用于解释为何KamuCL启动器很神秘的仓库
如题，这是一个关于为什么我要说明为何KamuCL启动器很神秘的
# 鸣谢
**https://github.com/kamubaba-i/KAMUCL/issues/50 有些图片来自于此issue，如果您不愿意提供图片可以开issue找我**
**https://github.com/kamubaba-i/KAMUCL/issues/49 此issue也为我提供了灵感，感谢支持**
# 免责声明
**本文章仅是针对此项目进行客观的说明，不存在任何主观性，我也不想牵扯太多，你跟我争那就是你有理，包括争议内容我一概不做说明**

**我本人是不反对Vide Coding及ai制作内容的，但我反对不经自己思考审核只是一味的把AI生成内容发布的项目，这类项目我认为作者应当自己至少去审核一下而不是开启全自动**
# 叠甲完成，正文开始
## 项目问题

---
## 1.奇异的项目大小
诚然笔者分析项目的前提是获得这个仓库的src，尽管笔者的网络是比较好的，但clone项目仍然花费我3分钟时间，甚至部分群友的clone原地爆炸难以clone

![clone爆炸图片1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/clonebomb1.png)
![clone爆炸图片2](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/clonebomb2.png)

便如上图所示，为什么呢？

![压缩后大小](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/zip.png)

是的，你没看错，即使是压缩过后的项目文件大小也高达**417MB**，这显然是不正常的，我难以想象一种什么样的mc启动器项目可以做到如此竟然的大小

![解压后大小](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/unzip.png)

解压后的文件高达**615MB**的大小还不是结果，将其导入后工作区大小竟然高达**1.1GB**
### 原因
经过对项目内所有文件的查询，罪魁祸首是根目录下的docs文件夹

![docs大小](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/docs.png)

这个docs文件夹占据了高达595MB的大小，99%的空间被docs占用

既然这个docs这么大一定有用吧？
事实是什么，**一点用没有**，纯纯的AI生成的一堆自我思考内容

让我们分析一下这个东西的架构：
**第一层（<1MB，有用）**：`FEATURE_TREE.md`（功能→代码地图）、`DEVELOPMENT.md`（开发指南）、`CONTRIBUTING.md`、。**这才是 docs 该长的样子。**

**第二层（618MB，占 99%+，无用）**：每发一版就产出一整套——
- `validation-1.1.21/README.md` 反复声明「测试失败不会因 Windows 通过而改为 Mac 通过」「不提前写成成功」「分数仅适用于评审实际看过的证据」
- `INDEPENDENT_WINDOWS.json` / `INDEPENDENT_MAC.json`「独立评分」（仓库490个提交来自 `dev@kamucl.local` 一个假邮箱，所谓独立评审是同一个 AI 换皮）
- `evidence/` 里 5870 张 jpg 截图 + 大 JSON，把整轮 QA 的像素/日志/断言全塞进去（你没听错，AI为了审核截的一堆图整整6000多张全塞进去）
  
## 2.不会写.gitignore
### 问题一
整个 `.gitignore` 没有任何一行提到 `docs/validation-*/evidence/` 或截图类型。**5870 张 jpg全塞进项目，根源就是这一行没写。**导致原本代码只有17MB的项目硬生生膨胀到600多MB

### 问题二 6 行重复规则做 `*.log` 一行就能做的事

```gitignore
dist-win*.log        # ← 被 *.log 覆盖
builder-*.log        # ← 被 *.log 覆盖
*-installer.jar.log  # ← 被 *.log 覆盖
*.jar.log            # ← 被 *.log 覆盖
*.log                # ← 第10行，这一行就够了
scripts/*.log        # ← 被 *.log 覆盖
```

6 行能砍成 1 行。中间还有个无意义的空行。

## 3.其他一些奇异问题
1.一堆主题塞一大堆json里面，一个json塞40000多行，您这是在写分布式数据库吗

---

## 代码问题
### 1.莫名其妙的彩蛋
![菜单代码实现](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/caidan.png)
相信各位不难看出这段代码的可读性算是较为低的，我为各位解释一下

**mascotAudio.ts** 里，那个拍击音效不是音频文件，是用代码实时合成的：

`export function slapSamples(sampleRate:number):Float32Array {
 const samples=new Float32Array(Math.ceil(sampleRate*.075))
 let seed=0x6b616d75, ...`

0x6b616d75 拆成 ASCII 就是 k a m u（0x6b='k', 0x61='a', 0x6d='m', 0x75='u'）。用一个 xorshift 伪随机噪声，叠两层低通（260Hz 体感 + 5200Hz 裂响），再加一个 150Hz 带降调的正弦，attack 0.7ms，最后限幅到 0.92。注释写得很文学(实则就是一波莫名其妙的操作)：
> Original palm slap: a dry broadband crack, short skin-like body, no sample library.

### 2.默认皮肤的绘制
![首页皮肤](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/skin.png)
这是KamuCL的首页中的一个皮肤示意（可见是一个默认皮肤），那么好，请各位看下面的代码
![代码皮肤](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/writeskin.png)
没错，这个皮肤是用rect一个个像素点画出来的(PS:力大砖飞的来了),我不明白是否为脑瘫AI不会使用图片实现或是某种奇特想法导致的问题（无意义的行为）

`const skin = 0xd3a582, shade = 0xbf8b6c, hair = 0x49382f
const jacket = 0x398d8a, seam = 0x286b70, light = 0x69b5ad
rect(8, 0, 8, 8, hair); rect(16, 0, 8, 8, shade)
rect(0, 8, 32, 8, skin); rect(0, 8, 32, 2, hair)
...`

注释：「Original KAMUCL pixel artwork. No remote skin or third-party texture required.」——为了零外部依赖，硬生生用代码画了一件青绿色拉链夹克+衬衫的角色。

PS:我并不明白的一个毫无意义的操作

### 3.让人感到奇异的轮播图
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/qiyibanner%201.webp)
![2](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/qiyibanner%202.webp)
![3](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/qiyibanner%203.webp)
我暂且仅放这些，自行评价

## 使用问题(部分内容来源于[KamuCL的issue](https://github.com/kamubaba-i/KAMUCL/issues/50))
**感谢[Cubk1](https://github.com/cubk1)的issue给我的灵感，一部分表达和图片也来源于大佬**
### 1.奇异的环境检测
很显然在我第一次启动我的世界之前他就认为我的环境有问题需要诊断一下，在笔者点击这个按钮之后，他告诉我发现了0个日志，那你诊断了个滚木呢。关键是诊断一次之后并没有消除这个奇妙的状态。
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/statcheck.png)
### 2.不会向溜溜梅表达hover
很显然作者不知为何无法表达"hover"这个词所以他一辈子都无法向他的溜溜梅AI描述这个问题，这个UI里的几乎所有按钮的hover判定都有问题，要不就是上半部分无法选中要不就是某一侧无法选中.
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/hover.gif)
### 3.错误日志
为什么没选择一个账号是一个错误还可能需要导出日志，很可能是作者贯彻了某种极繁主义精神认为能用异常解决的问题就绝对不需要控制流以便显得自己的项目有很NB顶尖的异常处理。
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/errorstart.png)
### 4.奇异登陆系统
最令人蒙古的是虽然项目自身是一个Electron编写的狗屎，并且文件高达100MiB甚至90MiB，并且包含完整的Blink、V8、Skia、Angle意味着可以渲染世界上任何一个能在前端渲染的东西比如MicrosoftVscode甚至是Firefox，但是作者及其高级的大脑还是决定给登录做成了你需要手动打开外置浏览器因为他不仅不会利用程序内的浏览器来渲染，甚至都不会自动帮你拉起一下浏览器，

**甚至作者都懒得自己去申请一个ClientID直接窃取了Prism Launcher的ClientID。（这是后话，稍后我会提到此问题）**
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/useprism.png)
### 5.奇异UI
我也难以描述UI问题了，可能是作者不太清楚outline的使用吧
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/uiprob.png)

## 道德方面的问题
**此处讲述一些严重的问题**
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/prism.png)
相信不少人能发现问题，为何我在KamuCL登录提示的是Prism的登录呢？
# 原因正是**KAMUCL未经Prism Launcher授权直接复制该Client ID并用于自身的OAuth2.0流程**
因此这违反了[《微软开发者身份平台使用条款》](https://learn.microsoft.com/es-es/legal/microsoft-identity-platform/terms-of-use)

事情经过：
Prism Launcher在公开仓库中开源了其Azure应用注册的Client ID`c36a9fb6-4f2a-41ff-90bd-ae7cc92031eb`
KAMUCL未经Prism Launcher授权直接复制该Client ID并用于[自身的OAuth2.0流程](https://github.com/kamubaba-i/KAMUCL/blob/main/src/shared/types.ts#L978)
KAMUCL使用该Client ID向`https://login.microsoftonline.com/consumers/oauth2/v2.0/devicecode发起设备代码请求`
KAMUCL使用该Client ID轮询`https://login.microsoftonline.com/consumers/oauth2/v2.0/token获取MAT与MRT`
KAMUCL继续以Prism Launcher的身份完成Xbox Live/XSTS/Minecraft登录链并最终向用户提供其自己的Launcher
用户在授权页面上看到的是Prism Launcher的应用名称而非KAMUCL
用户以为自己在授权给Prism Launcher而实际Token被KAMUCL获取
[自己去注册一个ClientID很难吗?](https://portal.azure.com/)

所违反的条款详情：

Misrepresent expressly, by omission, or implication, the need for users to obtain a valid license to the Microsoft offering that is accessed through any Microsoft Identity API;
Use the Platform to disrupt, interfere with, or attempt to gain unauthorized access to services, servers, devices, or networks connected to, or which can be accessed via, the Platform;
Redistribute or resell, or sublicense access to, the Platform, any data obtained using the Platform, or any Microsoft offering;
You may not select a Display Name that impersonates someone else, is or may be illegal, is or may be protected by trademark or other proprietary rights, is vulgar or offensive or that may cause confusion.
You may not otherwise assign the Application ID without Microsoft’s prior written consent.
be free from any malicious, deceptive or unlawful code or program or any activities that violate applicable law, regulations, government orders or decrees;
You must not collect or transfer any User’s personal information or data in a misleading, illegal, unauthorized or unfair way.
这已经是极为严重的问题了，笔者在登陆时甚至怀疑是否遭遇了token小偷

## 总结
综上，由于我懒得写更多，所以可见这个奇异客户端的种种诡异行径令人难以理解

# 附录
## 值得庆幸的是，[GongSunFangYun](https://github.com/GongSunFangYun)在Prism的不和谐频道发布了此内容，引起两位contributior惊呼：
![1](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/report.png)
**老实说我觉得微软不会鸟这个事情
但是我没法理解这群byd为什么非得用我们的cid
直接用官启的cid跑oauth2.0都行
那么做岂不是更好
modrinth就是那么做的
你要是告诉我他们的家在哪我给他们全家绑过来批斗**

## 不论如何，我始终坚持不论微软是否在意此事，都不应成为KamuCL盗窃其他客户端的Oauthid的理由，尽管可以辩解为AI自己的行径，但这仍然是KamuCL的开发者没有做到监管的责任
![agentmd](https://v6.gh-proxy.org/https://github.com/PTlongling/Why-KamuCL-is-a-mysterious-project/blob/main/pic/agentmd.png)
甚至在此项目的Agent.md文件中明晃晃出现了
> 用户已明确授权：每批修改完成后，默认完成验证、打包，并更新到 https://github.com/kamubaba-i/KAMUCL ，无需再次询问是否打包或发布。

**这是对开源项目的严重不负责，纯粹让AI仓库那这边不是一个好的项目，不做审核，AI提交什么一律不管，对此我不予置评**

笔者穷尽脑汁写到这里实在没啥写的了，最多是一些设计问题，就此结尾吧
写于2026年10月12日
