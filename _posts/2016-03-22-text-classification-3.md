---
layout: post
title: "文本分类（三）特征提取表达与Weka分类器训练与测试"
date: 2016-03-22 21:26:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- DataMining
- Weka
meta:
  _edit_last: '1'
---
时隔好久好久,文本分类的第三篇终于来了!

言归正传,在分词之后就要特征表达和提取了,什么意思呢?分类器要对文章分类,可是分类器怎么识别每一篇文章呢,它又怎么来对每一篇文章进行数据化的处理呢?

对文章分类有这么几种方法:

* 基于词的匹配
* 基于知识规则
* 基于统计的方法(机器学习)

可以看出来,这三种分类方法正是一个分类的发展过程.词的匹配太过死板,很难准确的提高分类程度.知识规则要由人来总结,而且还要将知识规则表达成数据,也不可靠.现在的分类方法,大多是第三种的机器学习.

机器学习就是给计算机一种能够自主挖掘规则的方法.对文本分类来说,就是用一部分语料库训练计算机,然后给它测试集来进行测试.因此,要将文本表达成计算机识别的数据.

有一种叫做向量空间模型的表达方法-VSM,意思就是把每一篇文章表达成一个词汇的堆积,这种方法也叫词袋.在这种模型中,一篇文章被看作单词特征项集合来看,利用加权特征项构成向量进行文本表示.

<!--more-->
当然,它有很大的缺点,它基本上完全忽略了除词的信息以外所有的部分,这使得它能表达的信息量存在上限.

还有主题模型的表达方式(LSI,LDA),这里先不说这种方法了,着重说VSM方法.

VSM是怎样具体表达一篇文章的呢?这里我们举例"我爱天安门,我的家."这句话就是一篇文章.

把它先用上一篇文章的预处理,分词后就变成了"我 爱 天安门 我 家".

带上权值表示,那么就是:
> D=("我", 2, "爱", 1, "天安门",......)
在Weka中,每篇文章都要表示为

@attribute @@class@@ auto
@attribute 我 numeric
@attribute 爱 numeric
{0 auto, 1 2,2 1}

这样的形式,其中auto为类别名."{}"的意义就是,它是auto类别,第一个词"我"出现2次,第二个词"爱"出现1次.

这样就把每一篇文章表达成了计算机可保存的形式.Weka中,这样的一篇文章就是一个Instance,全部文章所形成的数据词典就是Instances.

那怎么把之前分好词的txt文件转化为Weka需要的表达形式呢?

先定义好训练集测试集,还有获取的数据与处理过的数据变量.

```java
Instances dataRaw;
Instances vectorDataRaw;
Instances trainingSet;
Instances testingSet;
```
然后开始进行处理.

```java
private void initInstance(String segmentedDataPath) {
		TextDirectoryLoader loader = new TextDirectoryLoader();
		try {
			loader.setDirectory(new File(segmentedDataPath));
			dataRaw = loader.getDataSet();
//			System.out.println("\nImported data:\n" + dataRaw);

			convertToARFF("segmentedArticlesRaw.arff", dataRaw);

			if (dataRaw.classIndex() == -1) {
				dataRaw.setClassIndex(dataRaw.numAttributes() - 1);
			}

			vectorDataRaw = stringToVector(dataRaw);
			convertToARFF("vectorArticleRaw.arff", vectorDataRaw);
			vectorDataRaw.setClassIndex(0);

			System.out.println('Dimensions:' + vectorDataRaw.numAttributes());

			divideTrainSetAndTestSet(2, 1);

  			naiveBayesFunction(trainingSet, testingSet);
  			// use Naive Bayes classifier

  			SMOFunction(trainingSet, testingSet);
            // use sequential minimal optimization

		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
	}
```
其中TextDirectoryLoader就是一个txt转Weka需要的arff文件的方法.给它传一个目录就可以.然后再把获取的数据集设置类别.

stringToVector函数是实现对数据词典的处理,如去停用词等一些设置.

```java
private Instances stringToVector(Instances instance) {
		StringToWordVector filter = new StringToWordVector();
		Instances dataFiltered;
		try {
			filter.setStemmer(new NullStemmer());
			filter.setStopwords(new File("resources/stop_words_ch.txt"));
			filter.setUseStoplist(true);
			// delete the stop words
			filter.setOutputWordCounts(true);
			filter.setInputFormat(instance);
			dataFiltered = Filter.useFilter(instance, filter);

			return dataFiltered;
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return instance;
	}
```
函数内容是先设置词干,然后载入停用词,比如哪些词是没意义的就去掉它.然后是设置表达的方式,就是说是想要某个词出现过就标记为1还是说出现过几次就标记为几次.最后函数的返回就是一个用户词典了.

获取到可用的词典后就可以进行分类训练与测试了.divideTrainSetAndTestSet(2, 1)是将数据集分成平等大小2部分给训练集和测试集.

```java
private void divideTrainSetAndTestSet(int totalSize, int testingSize) {
		StratifiedRemoveFolds filter = new StratifiedRemoveFolds();

		String[] options = new String[6];

		options[0] = "-N";
		options[1] = Integer.toString(totalSize);
		options[2] = "-F";
		options[3] = Integer.toString(testingSize);
		options[4] = "-S";
		options[5] = Integer.toString(1);

		try {
			filter.setOptions(options);
			filter.setInputFormat(vectorDataRaw);

			filter.setInvertSelection(false);
			testingSet = Filter.useFilter(vectorDataRaw, filter);
//			System.out.println(testingSet);
			filter.setInvertSelection(true);
			trainingSet = Filter.useFilter(vectorDataRaw, filter);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
}
```

最后一步就是分类了.这里用naiveBayesFunction,SMOFunction这两个函数就是分别对应朴素贝耶斯和SVM分类方法.至于这两个分类算法就不多提了,直接调用Weka的API就可以.

```java
private void naiveBayesFunction(Instances training, Instances testing) {
		long trainingStartTime = System.currentTimeMillis();
		NaiveBayes classifier = new NaiveBayes();
		try {
			classifier.buildClassifier(training);
			long trainingEndTime = System.currentTimeMillis();
//			System.out.println("\n\nClassifier model:\n\n" + classifier);
			evaluation("evaluation/NaiveBayes.txt", classifier, testing, trainingStartTime, trainingEndTime);
		} catch (Exception e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
}
```
evaluation函数是将评测结果存到"evaluation/NaiveBayes.txt"这个文件里.

```java
private void evaluation(String filename, Classifier classifier, Instances data,
			long trainingStartTime, long trainingEndTime) {

	try {
  		Evaluation eval = new Evaluation(data);
  		eval.crossValidateModel(classifier, data, 3, new Random(1));
		String class_detail = eval.toClassDetailsString();
//	    System.out.println(class_detail);
	  	String summary = eval.toSummaryString();
//	    System.out.println(summary);
		String confusion_matrix = eval.toMatrixString();
//	    System.out.println(confusion_matrix);
		long endTime = System.currentTimeMillis();
	    if (!filename.equals("")) {
	    	FileWriter fw = new FileWriter(filename);
	    	BufferedWriter bw = new BufferedWriter(fw);
		  	String result = classifier + "\n" + class_detail + "\n"
		  			+ summary + "\n" + confusion_matrix + "\n"
	      			+ "Training Time:\n" + (long)(trainingEndTime - trainingStartTime)/1000.0 + "s\n"
  	    			+ "Testing Time:\n" + (long)(endTime - trainingEndTime)/1000.0 + "s";
  	    	bw.write(result);
		  	bw.close();
		  	fw.close();
	      }

	} catch (Exception e) {
  		// TODO Auto-generated catch block
  		e.printStackTrace();
	}
}
// evaluate the classifier by confusion matrix
```
Evaluation是Weka提供的评估类,里面有交叉校验等方法,这里就用交叉校验,然后用混淆矩阵来查看分类情况.

这篇的分类方法是调用Weka的API来是实现的,现在成熟的第三方分类器有很多,所以我们只要把重点放在数据的预处理上就好.