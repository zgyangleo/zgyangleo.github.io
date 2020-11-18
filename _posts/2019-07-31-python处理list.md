---
layout: post
title:  "python将两个list元素一一对应转换为dict"
date:   2019-7-31 21:23:01 +0800
categories: python
tag: list
---

* content
{:toc}


使用python的zip函数和强大的集合操作可以方便的将两个list元素一一对应转换为dict, 这个问题的思路是先用两个list存储两个不同的数据，之后再对两个list进行处理，代码如下：

        names = ['n1','n2','n3']
        values = [1,2,3]

        nvs = zip(names,values)
        nvDict = dict( (name,value) for name,value in nvs)

我自己需要解决的问题读取两个txt文本，输出文本中数据的一一对应关系，并保存结果到新文本中。
其中一个文本结构如下图所示：

![1]({{ '/styles/images/20190731/2019-07-31 21-33-48屏幕截图.png' | prepend: site.baseurl  }})

另外一个样本结构如下所示：

![1]({{ '/styles/images/20190731/2019-07-31 21-34-44屏幕截图.png' | prepend: site.baseurl  }})

我的代码实现：
        
        f1=open('a.txt','r')   #文件指向
        txt=f1.readlines()
        a=[]
        for w in txt:
            w=w.replace('\n','')
            a.append(w)
        #print(a)

        f2=open('b.txt','r')    #文件指向
        txt=f2.readlines()

        for value in txt:
            value=value.replace('\n','')    
            value=value.replace(', ','')
            value=value.replace('[','')
            value=value.replace(']','')
        #print(value)
        #print(type(value))


        nvs = zip(a,value)
        nvDict = dict((a,value) for a,value in nvs)
        #print(nvDict)
        #for key,value in nvDict.items():
        #	print('{key}:{value}'.format(key=key, value=value))
        f=open('class.txt','w+')           #保存输出文件              
        b = sorted(nvDict.items(), key=lambda x:x[1])
        for x,value in b:
            f.write("{}:{}\n".format(x,value))
        f.close
        #c = "".join(b)
        #print(type(b))
        #print(b)

得到结果如下：

![1]({{ '/styles/images/20190731/2019-07-31 21-43-27屏幕截图.png' | prepend: site.baseurl  }})




