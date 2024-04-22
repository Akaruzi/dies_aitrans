# Diesirae Fansclub

![汉化staff](https://github.com/Akaruzi/dies_aitrans/assets/119655555/8cb6c93f-4d68-490e-aca5-9a6178ac72f3)

补丁由Diesirae Fansclub汉化组所制作

特别感谢老组所做的初翻，前程序提供的启发和帮助

内容包括steam免费版汉化补丁，steam版英转日补丁，steam版粉丝回赠aef版英转日补丁

di的浅薄考据：https://diesiraefansclub.gitbook.io/di_wiki

---

本项目的主要启发来自隔壁做di翻译巴西哥Knox，所以也来分享一下di补丁制作的过程，希望能给其他汉化基于malie引擎游戏的坑友些许启发。

## 工具
涉及到的工具一览（见Tools文件夹）

- **malie_script_tool**，**[Malie's packer](https://github.com/satan53x/SExtractor/tree/main/tools/Malie)**，Garbro, Enigma Virtual Box

**具体使用参考之前的项目[说明文件](./前言.md)**

di的游戏剧本文件为封包中的data/system/exec.dat。利用garbro解包后，通过malie_script_tool提取文本，因为文本编码是utf-8，不存在调整编码问题，修改后直接回封即可。

- **免封包**:
malie可以读取封包外的文件，但优先度是封包文件大于未封包文件，所以可以用garbro提取封包内所有文件并保持相同的文件分布结构，移除原封包文件后即可实现免封包。这种方法便于测试，但如果要制作补丁，体量太大。
- **dat封包**
  malie对封包资源文件的读取是根据文件序号名由大到小的，存在相同文件时，malie会优先调用大序号的封包里的资源。所以可以通过将修改后的视频文件（op，ed），图片，以及音频重新封包并命名为data4.dat，来替代原文件。

​	使用Malie's packr封包，python运行dat_pack.py即可。但注意英版和日版的加密不同，需要用二进制编辑器（010Editor）查询原封包0x_01-0x_07字段，来更改dat_pack.py

## Steam版的补丁制作

### 剧本

解包data3.dat会发现data/system下存在这三个文件 exec.dat, patch.dat, translation.csv

- **patch.dat**其实跟exec.dat都是exec剧本封包文件。对比两者解包剧本就会发现，两者文本不一样，patch的剧本要多一行，但跟exec的文本大致相同，但有改掉一些病句和漏字，可以猜测patch应该是基于exec的文本二次校对过后的产物。steam版实际读取的是patch.dat，所以需要修改的也是patch.dat

- **translation.csv**，用于更改提示弹窗的文本。注意编码只能是shift-jis。用resource hacker也通过修改exe文件翻译弹窗文本，支持utf-8编码，但只能部分修改（没脱壳的话？）

- 翻译文本。di的英版翻译本土化（美国化）过于严重，相较原日版偏差太多，只建议基于日语文本来翻译。

日版和英版的剧本相差了300+行，部分语音调用也被修改了。制作steam版补丁必须基于steam版的剧本文件，所以我对照着steam英版的文本做了一个英日对照的字典 (**steam2jp.json**) ，可以以此替换steam版剧本方便对着日语进行翻译。替换好的文件见(**exec.msg_steam2jp.txt**)

### 字体&窗体标题

**malie.ini**指定了游戏的调用字体和窗体标题

- 更改Title字段就可以修改窗体标题
- 游戏默认使用的字体是anticcezannepro.otf
1. 修改ini文件SystemFont和FONT01字段，然后在游戏目录下创建路径/data/font/，将新字体放入即可，注意只能是otf的字体文件
2. 或者打包时在/data/font提供新字体，把名字命名为anticcezannepro.otf以覆盖旧字体

### 整体打包

使用**Enigma Virtual Box**，把做好的新封包data4.dat和更改后的malie.ini跟malie.exe一并打包即可

新的exe会优先读取虚拟环境里的malie.ini
