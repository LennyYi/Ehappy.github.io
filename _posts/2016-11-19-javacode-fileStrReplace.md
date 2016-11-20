---
layout: post
title:  "Java工具 高效率替换文件夹下的所有文件中的指定字符串"
date:   2016-11-18 11:09:36 +86
tag: java,文件替换,高效率
categories: utils
---
考虑到以后需要把临时的七牛图床临时链接替换为正式的链接，所以想写一个这样的类。文件如果多起来，遍历是非常耗时间的。当然这个类还有很多改进的地方，自己会慢慢改进。

{% highlight js %}
package com.company.test;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;
import java.nio.file.FileVisitResult;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.SimpleFileVisitor;
import java.nio.file.attribute.BasicFileAttributes;
import java.util.ArrayList;
import java.util.List;

/**
 * 这是一个关于文件操作的工具类，会同步到我的博客中
 * 
 * @author Lenny Yi
 * 
 */
public class FileUtils {

	private FileUtils() {
		throw new AssertionError();
	}

	/**
	 * 修改一个文件夹下的字符串
	 * 
	 * 遍历文件夹下的所有文件 对每个文件的内容进行扫描并替换
	 * 
	 * 有必要首先看一下这个字符串出现的次数,不过由于是内部类，本来思考是否用类似C# out类型来返回这个值后来想了一下还是拆开算了，并非总是想要看到底改了几个
	 * 
	 * 所以最好的方法就是写另一个方法来做，至于总数我想一个数组就可以完成。静态方法中的参数如果要用在内部类中必须是final的。所以设定一个int counter的方式加起来是行不通的。
	 * @throws IOException  这里由于是演示 所以没对异常进行处理直接抛出。想想可能出现的异常都很直接的出现在控制台上。如果正是项目中根据情况来判断需要处理那些异常
	 */
	public static void replaceStrInFiles(String rootPath,
			final String word, final String replaceStr) throws IOException {
        final List<Integer> counterArr = new ArrayList<Integer>();
		Path initPath = Paths.get(rootPath);
		Files.walkFileTree(initPath, new SimpleFileVisitor<Path>() {
          
			@Override
			public FileVisitResult visitFile(Path file,
					BasicFileAttributes attrs) throws IOException {
				int counter = 0;
//				System.out.println(file.getFileName().toString());
				counter = countStrInFile(file.toString(),word);
				counterArr.add(Integer.valueOf(counter));
				replaceStrInFile(file.toFile(), word, replaceStr);
				System.out.println(file.toString()+"--"+counter);
				return FileVisitResult.CONTINUE;
			}

		});
		int sum = 0;
		for (Integer num : counterArr) {
			
			 sum = num +sum;
		}
		//当然这里可以作为返回值来显示
		System.out.println("in total: " +sum);
	}
	/**
	 * 替换一个文件字符传的方法
	 * @param file 文件名称
	 * @param word 文件中有的旧字符
	 * @param relaceStr 要替换成的新字符
	 */

	public static void replaceStrInFile(File file, String word, String relaceStr) {
		boolean flag = false;
		File temfile = new File(file + ".tmp");
		String temp = null;
		//twr 语法 自动finally关资源
		try (FileReader fr = new FileReader(file);
				FileWriter fw = new FileWriter(temfile);) {
			try (BufferedReader br = new BufferedReader(fr);
					BufferedWriter bw = new BufferedWriter(fw);) {

				String line = null;
				while ((line = br.readLine()) != null) {
					int index = -1;
					if (line.length() >= word.length()
							&& (index = line.indexOf(word)) >= 0) {
						temp = line.replaceAll(word, relaceStr);
						fw.write(temp);
						fw.flush();
						flag = true;
					}
				}

			}
			if (flag) {
				file.delete();
				temfile.renameTo(new File(file.getAbsolutePath()));
			}

			temfile.delete();
		} catch (Exception ex) {
			ex.printStackTrace();
		}
	}

	
	/**
	 * 统计每个文件有几个这样的字符串
	 * @param filename
	 * @param word
	 * @return 每个文件中替换的次数
	 */
	
	
	public static int countStrInFile(String filename, String word) {
		int counter = 0;
		try (FileReader fr = new FileReader(filename)) {
			try (BufferedReader br = new BufferedReader(fr)) {
				String line = null;
				while ((line = br.readLine()) != null) {
					int index = -1;
					while (line.length() >= word.length()
							&& (index = line.indexOf(word)) >= 0) {
						counter++;
						line = line.substring(index + word.length());
					}
				}
			}
		} catch (Exception ex) {
			ex.printStackTrace();
		}
		return counter;
	}

	public static void main(String[] args) throws IOException {
//		FileUtils.replaceStrInFiles("F:\\test", "www.lennyyi.cc",
//				"ogu9js0qs.bkt.clouddn.com");
		 FileUtils.replaceStrInFiles("F:\\test","ogu9js0qs.bkt.clouddn.com","www.lennyyi.cc");
	}
} 

{% endhighlight %}



