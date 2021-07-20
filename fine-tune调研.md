
### 微调模型调研

|工具包名称|使用语言|是否能够微调|准确率|召回率|F1值|
|:---:|:---:|:---:|:---:|:---:|:---:|
|StanfordCoreNLP|java|||
|Hanlp|pyhton|调用api结构，提供fine-tune||
|foolNLTK|python|能够fine-tune||
|LTP|python|仅提供使用，没有看到fine-tune的方式|||
|BiLSTM+CRF|python|需要模型训练|||
|百度LAC|python|提供增量训练、提供字典定义|||
|复旦大学FNLP|java||||
|腾讯texsmart|python、java|提供API，不提供fine-tune仅调用|||

对词性标注与实体识别中的词典进行标注。


#### 使用流程
- 百度LAC
```
lac=LAC
train_file="xxx.train"
test_file="./xxx.test"
lac.train(model_save_dir='xxx',train_data=train_file,test_data=test_file)
```
百度提供较为成熟的lac模型其中，主要是进行词性标注与实体识别。lac中将模块中的词性标注和实体识别合并到一个任务中进行了。

*部署过程中的问题*
安装LAC模块的过程中，pip就自动安装其依赖的第三方库包括paddlepaddle，搜索相关文献发现由于最新版本之间存在的👇不兼容性，导致目前的lac模块在部署到服务器上时会出现较为常规的“段错误”问题。该问题还未解决

- texsmart
腾讯提供的命名实体识别模型相较于传统的模型来说缺乏重写的接口，其中命进行命名实体识别的过程需要api来进行访问，每次调用一个api，较为耗时，但由于texsmart提供更为细粒度的划分因此相较来说，效果较好。









### 知识图谱问答系统详细流程
主要聚焦点是关系预测
eg:"'where', 'did', 'the', 'event', '<e>', 'take', 'place', '_EOS'"
对应到的原始数据集
![图片](example.png)
主要是对原始数据集中的实体进行了剔除（对原始文本进行了实体识别并抽取出去的过程）。第一个项表示实体项，第二个项表示关系项，第三个项表示尾实体。变为如上图中的例子的模样。查看数据集中相关信息可以看到基本上都是简单的实体对问答(仅包含一个实体)
利用python中内置的word2vec库将对应的向量转化为词向量，丢入GRU模块进行encoder（将对应的整句话编码成一个向量），利用decoder模块对该向量进行解密，与encoder阶段对应，使用GRU进行解密。（该过程中是关于实体表、关系表）