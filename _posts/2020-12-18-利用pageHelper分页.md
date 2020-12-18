---
title: 利用pageHelper进行分页
tags: TeXt
---

首先需要去maven把包地址复制到pom.xml中

```xml
<!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
    <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>5.2.0</version>
    </dependency>
```

然后需要去mybatis的全局配置中进行分页拦截器的设置

```xml
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor">
        <!--配置你所使用的数据库-->
        <property name="helperDialect" value="mysql"/>
    </plugin>
</plugins>
```

dao层不做修改，Sev层写接口与实现

```java
public PageInfo<TInsect>getPage(int pageNo,int pageSize);
```

```java
public PageInfo<TInsect> getPage(int PageNo, int PageSize) {
        String config = "mybatis-configuration.xml";
        InputStream resourceAsStream = this.getClass().getClassLoader().getResourceAsStream(config);
        SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(resourceAsStream);
        SqlSession sqlSession = factory.openSession();
        InsectDao insectMapper = sqlSession.getMapper(InsectDao.class);
        //实现对数据拦截
        PageHelper.startPage(PageNo,PageSize);
        List<TInsect>list = insectMapper.showAll();
        //将集合对象放入PageInfo
        PageInfo<TInsect>page = new PageInfo<TInsect>(list);
        sqlSession.close();
        return page;
  }
```

servlet层进行页面数据的接收与封装传输

```java
protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html;charset=utf-8");
        PrintWriter pw = response.getWriter();
        int pageNo = 1;
        String Page = request.getParameter("page");
        if(Page!= null){
            pageNo = Integer.parseInt(Page);
        }
        System.out.println("pageNo = " + pageNo);
        String limit = request.getParameter("limit");
        int pageSize = Integer.parseInt(limit);

        PageInfo<TInsect>show = is.getPage(pageNo,pageSize);
        ObjectMapper objectMapper = new ObjectMapper();
        String json = objectMapper.writeValueAsString(show);
        System.out.println("json = " + json);
        pw.write(json);
  }
```

在html界面需要指明

```javascript
,parseData: function(res){ //res 即为原始返回的数据
                return {
                    "code": 0, //解析接口状态
                    "count": res.total, //解析数据长度
                    "data": res.list
                };
			}
```

注意，此处使用的js是基于LayUi的使用，res.list与res.total是指从servlet传入界面的被封装后的json参数，我们打印后就可以看到：

```
json = {"total":11,"list":[{"pkId":11,"insect":"牵牛","host":"屎","danger":"吃屎"}],"pageNum":2,"pageSize":10,"size":1,"startRow":11,"endRow":11,"pages":2,"prePage":1,"nextPage":0,"isFirstPage":false,"isLastPage":true,"hasPreviousPage":true,"hasNextPage":false,"navigatePages":8,"navigatepageNums":[1,2],"navigateFirstPage":1,"navigateLastPage":2}

```

