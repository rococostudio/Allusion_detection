# Allusion_detection
## 中文说明（待完善）：



已发表论文工作：End-to-End Multi-task Learning for Allusion Detection in Ancient Chinese Poems


### 项目各目录功能说明：
1. ##### Corpus：典故语料，
   
   * AER_corpus：典故提取任务（Allusion Entity Recognition task）的数据集
   	```
     | 典故辞典      | 数据集文件名称               | 诗词例句数量     | 典故规模       |
     | -------------|-------------------------- | ---------------| --------------|
     | 《全唐诗典故辞典》 | train_quantangshi.data | 26,447         | 14,187        |
     | 《全宋词典故辞典》 | train_quansongci.data  | 21,734         | 7,303         |  
     | 《古诗词典故辞典》 | train_gushici.data     | 18,337         | 1,295         |
     | 《全元曲典故辞典》 | train_yuanqu.data      | 4,200          | 2,114         |
     ```
    * AEC_corpus：典故分类任务（Allusion Entity Classification）的数据集

          ```
       | 文件名                             | 描述                           |
       | --------------------------------- | -------------------------------|
       | sentence_with_single_class.csv    | 以诗句作为输入来对典故进行单类别分类 |
       | sentence_with_milti_class.csv     | 以诗句作为输入来对典故进行多标签分类 |
       | allu_source_with_single_class.csv | 以典源作为输入来对典故进行单类别分类 |
       | allu_source_with_milti_class.csv  | 以典源作为输入来对典故进行多标签分类 |       
          ```
   	

        ```
       各类别的详细样本统计信息
       ---------------------------------------------------------
       
       | 典故类别 | 诗句-单标签 | 诗句-多标签  | 典源-单标签  | 典源-多标签 |
       |--------|------------|------------|------------|-----------|
       | 九流部  | 92         | 259        | 32 		  | 75	      |
       | 人事部  | 634        | 1483 	     | 248 		  | 485		  |
       | 人体部  | 107        | 287 	     | 32 		  | 91		  |
       | 人物部  | 375        | 974 	     | 109 		  | 295		  |
       | 伦类部  | 125        | 326 	     | 45 		  | 99		  |
       | 动物部  | 241        | 584 	     | 80 	  	  | 195		  |
       | 器用部  | 493        | 1127 	     | 119 		  | 353		  |
       | 地理部  | 134        | 365 	     | 32 		  | 103		  |
       | 天文部  | 94         | 249 	     | 29  		  | 74		  |
       | 政事部  | 125        | 333 	     | 59 		  | 111		  |
       | 文明部  | 221        | 498 	     | 94 		  | 164		  |
       | 植物部  | 107        | 297 	     | 32 		  | 91		  |
       | 武备部  | 91         | 205 		 | 38 		  | 65		  |
       |--------|------------|------------|------------|-----------|
       | 总计    | 2839       | 6987 	  | 949 	   | 2201	   |
        ```
   
   
   
   
   * ASI_corpus：典故溯源任务（Allusion Source Identification）的文本对数据集
     
     ```
     | 文件名                | 描述               |
     | -------------------- | -------------------|
     | pair_sample_data.csv | 诗句-典源文本对正负例 |
     ```

   * PR_corpus：诗词可读性（Poetry Readability）的难易度数据集
     ```
      		| 文件名             | 描述           |
        | ----------------- | -------------- |
        | APRD.csv          | 三级难易度标签   |
        | APRD+.csv         | 六级难易度标签   |
        | raw_poem_text.csv | 诗词文档原文     |
     ```





2. ##### AHMTL_model：AHMTL模型实现
   
1. 模型架构图
   
   ![](https://github.com/lailoo/Allusion_detection/blob/master/model_implement-architecture.png)
   
   ![](https://github.com/lailoo/Allusion_detection/blob/master/model_implement-component.png)
   
2. 程序文件基本功能说明
   
   * kashgari：模型实现所借鉴的开源NLP框架(
   
     [https://github.com/BrikerMan/Kashgari]: Kashgari开源框架项目链接
   
     )，我们在1.x的基础上进行修改，使用TensorFlow2.0对该框架进行复现，并在此基础上实现了AHMTL模型，在此向该框架的原作者致谢。
   
     * embeddings：加载BERT embeding 如poetry-BERT  或者 BERT-Base
   
     * keras_transformer：是基于Keras的Transformer框架的开源代码实现，：
   
       [github.com/kpot/keras-transformer]: 源代码地址
   
     * layers：各类中间层的自定义实现，如CRF、Attention层等
   
     * models：各个模型的具体实现，包括单个模型如序列标注模型(labeling)/文本匹配模型(pair_match)等，以及多任务模型AHMTL。
   
     * processors：用于处理各类型的任务的输入数据的处理器，包括分类模型的处理器、序列标注模型的处理器。
   
     * task：任务实例的实现，负责各个任务的初始化、数据加载等工作，比如典故分类任务（allu_class_task）、典故溯源任务（allu_source_task）、典故提取任务（ner_task，由于典故提取模型实现采用了类似于NER任务的建模方式，因此实现模型框架初期使用了ner三个字母代表典故提取任务，在此简单说明）、诗词可读性任务（readability_task）。
   
     * trainer：异构多任务学习的训练器，负责随机抽样任务，输入对应任务的当前数据batch等工作。
   
   3. model_results
   存放了记录训练中间过程以及模型结果的jupyter文件。
   
   ---
   
3. ##### 补充：

   1. 古汉语BERT模型：poetry-BERT将 上传至百度云，详细链接后续更新。
   2. 更多借鉴与引用的工作在此不一一列出，详细请参考论文，但都在此进行致谢，感谢您们的分享，感谢你们的帮助。

4. 下一步工作打算：

   1. 优化模型，考虑在典故提取模型中加入典源信息，以作为判定一个短语是典故词语还是非典词语的依据。
   2. 优化poetry-BERT预训练模型，将所有的典故所涉及的共7.3诗词例句作为预训练语料，在随机mask一个词语改为仅mask典故词语，然后进行预训练。
   3. 实现典故自动提取、溯源、分类应用系统。
   4. 实现人工纠正系统来对当前大部分通过正则表达式自动抽取的典故序列数据进行人工纠正。

# English Introduction(To Be Continued)


