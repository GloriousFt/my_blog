---
layout: post
title: "文本分类（二）语料库文本预处理之分词"
date: 2015-12-04 03:29:59.000000000 +08:00
type: post
published: true
status: publish
categories:
- Technology
tags:
- DataMining
meta:
  _edit_last: '1'
---
<p>上一篇文章写了如何用爬虫抓取文章形成语料库，这一篇就来讲获得语料库之后如何对之进行预处理，使之转化为质量较高、计算机可分类处理的语料库。</p>
<p>语料库预处理的意义是什么呢，为什么预处理占了文本分类的大部分工作量？这是因为，计算机如果直接接收大量文本信息，会极大的降低运算效率，最重要的是，文本中还可能含有很多不合法字符或者毫无意义的其他字符，这些在文本分词之后会形成大量冗余的词汇，影响分类器效果。所以，在特征表达之前，一定要进行文本预处理。</p>
<!--more-->
<p>语料库一共10个类别，每个大概1000篇左右的文章。因为内容都是中文的，所以分词时没办法像英文一样，直接按空格就分开。这里要用到一个<a title="官方网站" href="http://ictclas.nlpir.org/" target="_blank">中文分词的工具ICTCLAS</a>（还有其他的如jieba等）。它是由中科院一个博士开发的，支持多种语言，但是不支持Mac操作系统。这里就用Java来进行分词。</p>
<p>先来下载这个分词工具，然后查看目录，Data是我们要加入到项目中的文件夹，直接将它导入到项目中就好，lib是需要的库，根据操作系统的不同，用户自行导入不同的库到项目中即可。sample是一些例子，里面有不同编程语言的例子。</p>
<p>首先创建项目，然后在代码中定义方法接口，需要什么方法就添加什么接口方法：<br />
<code lang="java"><br />
	public interface CLibrary extends Library {</p>
<p>		CLibrary Instance = (CLibrary) Native.loadLibrary("resources/NLPIR", CLibrary.class);</p>
<p>		public boolean NLPIR_Init(byte[] sDataPath, int encoding, byte[] sLicenceCode);</p>
<p>		public String NLPIR_ParagraphProcess(String sSrc, int bPOSTagged);</p>
<p>		public String NLPIR_GetKeyWords(String sLine,int nMaxKeyLimit,boolean bWeightOut);</p>
<p>		public String NLPIR_GetLastErrorMsg();</p>
<p>		public void NLPIR_Exit();<br />
	}<br />
</code><br />
先读入相应的库NLPIR，它在下载好的文件夹中lib里面，是windows操作系统就用dll的，linux就用so的。resources是我自己创建的文件夹，里面就是装一些资源的，这个只是一个路径，用户自己定义就好，只要能够定位到NLPIR.dll和NLPIR.lib。第二个是初始化，分词之前一定要初始化，具体怎么调用后面会讲。第三个就是分词啦。第四个是获取关键词，第五个是出错的话查看错误信息，最后一个Exit就是退出。</p>
<p>下面是具体遍历文件夹对每一个文档进行分词并保存的代码：<br />
<code lang="java"><br />
private void wordsSegmentation(String dirname) {<br />
		Vector<string> dirs = new Vector<string>();<br />
		File root_dir = new File(dirname);<br />
		FileReader fr;<br />
		FileWriter fw;</p>
<p>		if (!root_dir.exists()) {<br />
			System.err.println("数据集文件夹不存在！");<br />
			return;<br />
		}<br />
		File[] classes = new File(root_dir.getPath()).listFiles();<br />
		for (int i = 0;i < classes.length;i++) {<br />
			if (classes[i].isDirectory()) {<br />
				dirs.add(classes[i].getName());<br />
			}<br />
		}</p>
<p>		String argu = "";<br />
		try {<br />
			if (!CLibrary.Instance.NLPIR_Init(argu.getBytes("UTF-8"),<br />
					1, "0".getBytes("UTF-8"))) {<br />
				System.err.println("初始化失败！" + CLibrary.Instance.NLPIR_GetLastErrorMsg());<br />
			}<br />
		} catch (UnsupportedEncodingException e1) {<br />
			// TODO Auto-generated catch block<br />
			e1.printStackTrace();<br />
		}</p>
<p>		for (int i = 0;i < dirs.size();i++) {<br />
			File[] files = new File(dataSetPath + "\\" + dirs.get(i)).listFiles();<br />
			for (int j = 0;j < files.length;j++) {<br />
				if (files[j].getName().contains("txt") && files[j].getName().length() > 4) {<br />
					// prevent empty file as a txt<br />
					try {<br />
						fr = new FileReader(files[j]);<br />
						BufferedReader br = new BufferedReader(fr);<br />
						String input = "";<br />
						String text = "";<br />
						while ((text = br.readLine()) != null) {<br />
							input = input + text;<br />
						}</p>
<p>						br.close();<br />
						fr.close();</p>
<p>						String newtext = null;<br />
						newtext = CLibrary.Instance.NLPIR_ParagraphProcess(input, 1);<br />
						System.out.println("分词结果为：\n" + newtext);<br />
						newtext = getNounAndVerb(newtext);<br />
						System.out.println("取名词和动词结果为：\n" + newtext);</p>
<p>						fw = new FileWriter(segmentedDataSet + "\\" + dirs.get(i) + "\\" + files[j].getName());<br />
						// for windows<br />
						BufferedWriter bw = new BufferedWriter(fw);<br />
						bw.write(newtext);</p>
<p>						bw.close();<br />
						fw.close();</p>
<p>					} catch (IOException e) {<br />
						// TODO Auto-generated catch block<br />
						e.printStackTrace();<br />
					}<br />
				}<br />
			}<br />
		}<br />
		CLibrary.Instance.NLPIR_Exit();<br />
	}<br />
</string></string></code><br />
参数是根目录的路径名，然后将根目录下的所有文件夹放入dirs中，做后续遍历处理。之所以这样做，是因为之前做语料库的时候，是把每一类的文章放在一个文件夹下，然后把这些文件夹一起放到了一个目录下，所以才有上边遍历文件夹的做法，如果只是遍历文件的话，直接找到文件遍历即可。然后是初始化工具，如果失败会提示出错信息。接下来就是循环，对每一个文件进行分词并存储到segmentedDataSet文件夹下对应的类文件夹的新文件里。</p>
<p>分词结束后查看分词结果吧。每个词带有词性后缀。</p>
<p>之后还要做一些处理，取名词、动词或者其他（根据用户需求），去停用词，加入用户词典。getNounAndVerb这个函数是我写的用来取名词和动词的，分词后会有词性后缀，所以根据词性后缀就可以取到想要的词。<br />
<code lang="java"><br />
	private String getNounAndVerb(String text) {<br />
		String result = "";<br />
		String[] new_words = text.split(" ");<br />
		for (int i = 0;i < new_words.length;i++) {<br />
			int indexSlash = new_words[i].lastIndexOf("/");<br />
			if (indexSlash != -1) {<br />
				String suffix = new_words[i].substring(indexSlash, new_words[i].length());<br />
				if (suffix.contains("n") || suffix.contains("v")) {<br />
					String word = new_words[i].substring(0, indexSlash);<br />
					result = result + word + " ";<br />
				}<br />
			}<br />
		}<br />
		if (result.length() > 1) {<br />
			result = result.substring(0, result.length() - 1);<br />
		}<br />
		// delete the last space<br />
		new_words = null;<br />
		return result;<br />
	}<br />
</code><br />
先取得后缀，然后判断是不是包含相应的字符，是的话就连接字符串，最后去掉最后的一个空格。返回新字符串。</p>
<p>在这之后的工作就要用到一个文本分类工具Weka了。有关Weka的具体用法将在下一章讲到，包括去停用词等等。这里的预处理其实不够好，还有很多好的方法能够将最后的字典维度降到很低。那么更多有关Weka的内容还是放到下一章吧，这次就更新到这里。</p>
