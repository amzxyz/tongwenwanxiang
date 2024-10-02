### 同文万象使用说明
本方案为万象同文定制版 [万象基础版本](https://github.com/amzxyz/rime_wanxiang)  [万象增强版](https://github.com/amzxyz/rime_wanxiang_pinyin)

**前言**：由于同文不具备拼音筛选器，因此想要体验特别好的小于26键的输入是不现实的，而依托万象拼音强大的词频（来自白霜）一定程度上弥补了这种不足。为了实现9键、14键、18键这几种常用的涉及到共键的输入方式，我们需要整体对方案层面进行运算处理，对同文主题进行深度定制，共同配合之下才能实现共键输入，下面我从几个方面阐述和说明。

**原理：** 

1. 对同文输入法皮肤进行定制，实现共键单发，例如WE共键，按下只发送w；
2. 对方案拼写运算进行结构化处理，将共键字母中例如WE中的e转换为w，多个字母同样处理；
3. 配合定制键盘即可打字。
   
<img src="https://storage.deepin.org/thread/202409250204031318_26.jpg" width="400">       <img src="https://storage.deepin.org/thread/202409250204112540_18.jpg" width="400">   
<img src="https://storage.deepin.org/thread/202409250204092790_14.jpg" width="400">       <img src="https://storage.deepin.org/thread/202409250204064425_9.jpg" width="400"> 

**实现细节：** 

1. 键盘的深度定制：
   - 首先要将9键、14键、18键布局设计出来，由于没有选择器，共键的键盘也没有必要使用shift，替换为更有用的剪切板，输入时替换为更有用的分词按钮，对于共键重码增加的情形分词可能会有助于输入的结构化更快获得结果；
   - 需要一个总的切换键盘的地方，我把它放在长按回车键呼出的功能菜单里，部署好主题与方案后首先需要进这里选一下你默认使用的，如果你想把它设置为默认，可以自行修改主题文件，实际上除了重启手机、切换主题它将一直不变；
   - 需要多个键盘隔离，因此首先去掉了开关中冲突的中英状态按钮，改用键盘中的中英切换按钮，实际上是切换键盘，为了使当前的键盘切换英文、符号、数字三个键盘时能有效返回当前键盘，他们之间的互相跳转都在假设18键这个范围内，不会串到26键及其他键盘，我做了各自独立的键盘做隔离，我保留了方案中的中标点、英标点开关，使面板可以在不同状态下获得不同的符号输出；
   - 不同的键盘我尽可能合理的预设了符号，分别定制了共键单发的按键，全部统一发送按键的首字母。
2. 关于方案的定制：
   - 由于共键后基于特定字母触发的反查功能因此被挂掉，中英混输也挂掉了，这块我留给大家自定义，可以通过长按某些按键发送连续的字符从而快速唤起功能，默认我去掉了反查功能；
   - 采用了两个方案文件的方式，一个是未经处理的，全功能的，它可以用于26键手机上也可以用于电脑上的wanxiang，一个是针对同文共键输入优化的tongwenwanxiang，在此方案中我提供了9键、14键、18键的选项，这些选项意味着拼写运算中对应不同键盘方案的公式。你只需要在方案文件里配置词选项，位于文件头的说明里，这个说明里可以选择双拼7种、全拼、8种辅助码，按你的习惯进行选择，在pc或者26键上你还需要对中英混输和反查方案进行表头配置，如果是共键方案，只需配置好tongwenwanxiang方案的表头即可。
   - 由于按键字母转为首字母，因此我无法通过preedit将显示码转回去，打字的时候会看到难以理解的编码，因此我定制了lua来统一将输入码转写为全拼，且对手动分词码做了优化处理，你可以在想加入分词的时候使用分词按钮，进而获得一致的体验。
3. **使用方法：** 

​     词库设计7种辅助码，头部使用全拼编码，可以转化为任何双拼编码，词库解码顺序为：全拼拼音；墨奇;鹤形;自然码;简单鹤;仓颉首末;虎码首末;五笔前2;汉心码，因此万象拼音支持拼音和辅助码任意两两组合。

###### 由于万象支持各种演变双拼编码方式，所以不能一一列举，且方案文件多了容易混乱。然而使用rime不可避免的要自定义，故而干脆将选项前置，初始化选择一番就是自己想要的方案，更深的定制再去修改其它地方。

###### 打开`tongwenwanxiang.schema.yaml`文件，表头按照提示进行选择，定义属于自己的输入法方案:

```
#本方案匹配词库解码顺序为：全拼拼音；墨奇;鹤形;自然码;简单鹤;仓颉首末;虎码首末;五笔前2;汉心码
#############DIY你想要的方案组合,试试搭配一个自然码+墨奇辅助的方案吧！###########################
schema_name: 
  name: 万象拼音   #可以改成与你所选方案一致的描述，不改也行
set_shuru_schema:         #配置此项就是选择什么输入法,同时拆分反查和中英文混输也将匹配该输入方案
  __include: algebra_zrm  #可选解码规则有   algebra_pinyin, algebra_zrm, algebra_flypy,  algebra_ziguang, algebra_sogou, algebra_mspy, algebra_abc  选择一个填入
set_algebra_fuzhu:        #配置此项就是选择什么辅助码
  __include: fuzhu_zrm    #可选辅助码有：fuzhu_kong，fuzhu_moqi, fuzhu_zrm, fuzhu_flypy, fuzhu_tiger, fuzhu_cj, fuzhu_wubi, fuzhu_jdh, fuzhu_hanxin 选择一个填入
pro_comment_format:           # 超级注释模块，子项配置 true 开启，false 关闭
  candidate_length: 1         # 候选词辅助码提醒的生效长度，0为关闭  但同时清空其它，应当使用上面开关来处理    
  fuzhu_type: zrm             # 用于匹配对应的辅助码注释显示，基于默认词典的可选注释类型有：(moqi, flypy, zrm, jdh, cj, tiger, wubi, hanxin)选择一个填入，之所以单独列出是因为这里有更多的可配置性，而真正的辅助码默认只有7种
  corrector: true              # 启用错音错词提醒，例如输入 geiyu 给予 获得 jǐ yǔ 提示
  corrector_type: "{comment}"  # 换一种显示类型，比如"({comment})" 
#共键或者模糊音：如果手机上使用，设定手机上的键盘布局，或者模糊音类型
set_gongjian_mohu:    #可选的值有：18jian, 14jian, 9jian
  __include: 18jian
########################以下是方案配置######################################################
```



​    如果是26键或者PC端编辑wanxiang.schema.yaml文件

​    再次打开`radical_pinyin.schema.yaml`和` melt_eng.schema.yaml`表头进行选择，二者情况一致：

```
###############选择与之匹配的拼音方案#####################
set_shuru_schema:
  __include: algebra_zrm    #可选的选项有（algebra_pinyin, algebra_zrm, algebra_flypy, algebra_mspy, algebra_sogou, algebra_abc, algebra_ziguang）
######################################################
```

​    最后一步修改default.yaml将不使用的方案注释掉，保存后部署方案即可！

  

  **问题：** 九宫格输入一定要选择全拼，但是可能由于同文性能的问题重码量大发生了卡顿，因此建议还是使用14、18键来使 用

​     更多信息关注主项目：https://github.com/amzxyz/rime_wanxiang_pinyin
