---
title : servlet到jsp全过程 
tags : JavaWeb
---

注意：jsp是java封装之后的servlet

在第一次进入页面时：

![img](/images/JspToSev/image-20201014200756522.png)

由于没有一个能直接进行访问的页面（页面已经被放入WEB-INF文件包中隐藏），需要以跳转访问的方式进行访问：

此处用于进行页面跳转的**servlet**的**urlPatterns**为"/topic"：

![img](/images/JspToSev/image-20201014201030039.png)

所以需要在访问网址上进行补充：

![img](/images/JspToSev/image-20201014200932692.png)

进行补充并且再次访问后，对jsp页面进行取关键字找到对应的跳转页面

![img]/images/JspToSev/image-20201014201553141.png)

相应的，位于jsp页面也会有相应的关键字进行对应（下图仅作为其中一种选项，当关键字未被声明时会直接判定为空）：

![img](/images/JspToSev/image-20201014201733503.png)

回到servlet界面，由于此时关键字method并没有被进行赋值，被直接判断为空，所以进入 **if ** 逻辑的第一个分支：

![img](/images/JspToSev/image-20201014202301704.png)

此分支是作为展示界面上的首页论坛状态，需要进行数据库读取，在进行数据库读取之后，需要用键值对的方法将读取的值（对象）进行传送

```java
request.setAttribute("listMainTopic",topics);//第一个参数作为键并在jsp中进行代表，第二个参数作为值
```

在将键值对传入jsp中后，jsp通过El语句进行传递的接收以及一些java语法的实现（例如**for each**循环）：

![img](/images/JspToSev/image-20201014203027139.png)

最后生成页面：

![img](/images/JspToSev/image-20201014203333343.png)

此时由servlet跳转至jsp页面的行为结束

当对标题进行点击操作时，事先在jsp的标题标签中进行标记的**method**将会被赋值为**"topicTitle"**并且传回servlet

注意：由于此界面的业务需要另外的参数，所以在method后面还跟着其他的变量参数，也会被一块传回servlet

![img](/images/JspToSev/image-20201014203845178.png)

在触发这次事件之后，servlet进行判断，进入另一分支

![img](/images/JspToSev/image-20201014204221648.png)

由于刚刚进行了另外一些变量的传输行为，servlet需要对所获得的结果进行对象的处理

![img](/images/JspToSev/image-20201014204427469.png)

与之前第一个界面的跳转相同，当servlet在数据库中获取到相应数据之后，生成相应的键值对，并且跳转至下一个界面

![img](/images/JspToSev/image-20201014204644022.png)

![img](/images/JspToSev/image-20201014204713698.png)

下一个界面根据传入的对象进行相应的处理，最后生成页面：

![img](/images/JspToSev/image-20201014204830387.png)

此次由jsp页面跳转至另一个jsp页面的行为结束