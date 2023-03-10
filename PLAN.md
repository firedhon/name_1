### 一	学习使用 github

### 二	构建代理 IP 池

### 三	爬虫课设

#### 1.选题要求

```
不同专业的就业前景是目前本科学生比较关心的问题，现在分别考虑不同专业的就业招聘信息及其岗位需求等信息，爬取新安人才网或者51job等招聘网站信息，设计网络爬虫程序，采集相关招聘信息及其岗位需求等内容，完成以下具体内容：

（1）第一组专业以“大数据”、“人工智能”、“计算机”为关键字进行搜索；
	第二组专业以“机械”、“电子”、“自动化”、“土木”为关键字进行搜索；
	第三组专业以“生物、化学、环境、材料”为关键字进行搜索；

（2）每一组专业的数据条数5-10万条；

（3）采集的岗位信息，包括：招聘专业、岗位工作、薪资、工作地点等不少于10个特征信息；
	完成信息采集后，对数据进行预处理，如删除或者填充空值、统一单位等；
（4）对所采集的数据进行薪资分布条图显示、招聘专业的分类显示等基本统计分析内容；可视化使用的分析图形至少包含条		图、词云图、折线图等；
（5）根据你爬取得到的数据和分析的结论，对目前三组专业的就业情况进行综合性评述，对相关专业学生的就业前景进行客观	评价。
```

#### 2.爬取单页数据

以boss直聘为目标爬取的网站，爬取第一组专业的**就业招聘信息**及**岗位需求**

特征信息

| 页面等级 | 特征信息     | 对应变量                          |
| -------- | ------------ | --------------------------------- |
| 一       | 工作名称     | job_name                          |
| 一       | 工作地点     | job_area                          |
| 一       | 薪资         | salary                            |
| 一       | 待遇         | **deal**                          |
| 一       | 基本要求     | **basic_need**                    |
| 一       | 所需技能     | **skill**                         |
| 一       | 公司名称     | corporation_name                  |
| 一       | 公司基本信息 | **corporation_basic_information** |
| 二       | 职位描述     | job_description                   |
| 二       | 公司介绍     | **corporation_description**       |



```
工作名称
工作地点
薪资
待遇
基本要求
所需技能
公司名称
公司基本信息
职位描述
公司介绍
```



#### 未解决存在的问题

```python
待遇	不是所有的工作都有待遇
基本要求	数据是以列表的格式存储的，爬取后一个工作的多个要求占用多行
所需技能	数据是以列表的格式存储的，爬取后一个工作的多个技能占用多行
公司基本信息	数据是以列表的格式存储的，爬取后一个工作公司的多个信息占用多行

```

#### 已解决存在的问题

```python
只能爬取到部分二级页面，可以将sleep时间延长后再进行尝试	#失败，改用解决方法1后解决
职位描述	数据可以正常存入，但会将文本以<br>进行分割，每段为一个单独的元素
公司介绍	不是所有的工作都有公司介绍
公司介绍	采用以上方法后，确实可以爬取下公司介绍的数据，但只能爬取至文本的第一个<br>
```



#### 解决问题的方法

```python
1.不使用requests.get(url, cookies=cookies, headers=headers)，改用driver.get(url)后即可爬取到二级页面，可能是后者较前者更容易爬取，并非前者方法不可行
2.爬取后“职位描述”存储为一个一维列表，可采用.join()方法将一个列表中的各元素以指定字符串连接起来，具体使用方法如下：a=['I','love','China','!']		print(' '.join(a))		I love China !
3.不是所有的工作都有公司介绍，可以先获取节点信息，再从节点中获取文本，这样如果该节点有缺失信息，会返回None值，不会空行，详见上课视频31:19处：
	salary_mid=dom.xpath('//div[@class="e"]')	
	salary=[i.text for i in salary_mid]
4.存储两个属性以上的文本数据在列表中，会变成二维列表，转为dataframe格式后会带上[]。将各属性单独存储在一个列表中即可,或者可以尝试对爬取的数据进行处理，如“爬取单页短评数据”中的
	cities = ['' if i == [] else i[0] for i in cities]
5.不采用方法3，用老方法+方法2即可，老方法：salary=dom.xpath('//div[@class="e"]/text()'),并按照单个属性存储在单个列表中的方法，不会出现缺失信息不空行的情况
```

4.处理前

<img src="C:\Users\楚国威\AppData\Roaming\Typora\typora-user-images\image-20221223114020568.png" alt="image-20221223114020568" style="zoom:50%;" />

4.处理后

<img src="C:\Users\楚国威\AppData\Roaming\Typora\typora-user-images\image-20221223111549563.png" alt="image-20221223111549563" style="zoom: 45%;" />



#### 可能存在的问题

```
每次搜索的结果不同，可能会牵扯到去重的问题
爬取次数过多可能会被该网站封禁IP
```





静态网页	request

动态网页	seniune
