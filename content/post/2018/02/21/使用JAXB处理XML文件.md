---
title: "使用JAXB处理XML文件"
date: 2018-02-21T14:21:50+08:00
tags: ["Java","JAXB","XML"]
categories: ["Java","JAXB","XML"]
toc: false
---

# 使用JAXB处理XML文件
JAXB（Java Architecture for XML Binding) 是一个业界的标准，是一项可以根据XML Schema产生Java类的技术。该过程中，JAXB也提供了将XML实例文档反向生成Java对象树的方法，并能将Java对象树的内容重新写到XML实例文档。从另一方面来讲，JAXB提供了快速而简便的方法将XML模式绑定到Java表示，从而使得Java开发者在Java应用程序中能方便地结合XML数据和处理函数。
> 关键字：`JAXB` , `Java` , `Serialize`

# 一个案例

下面是一个测试案例。

## model
```java
//AllProductLevels 模型
package com.serializations.model;

import java.util.ArrayList;
import java.util.Iterator;

import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;

@XmlAccessorType(XmlAccessType.FIELD)
@XmlRootElement(name="AllProductLevels")
public class AllProductLevels {
	@Override
	public String toString() {
		String result = "";
		
		for (Level level : children) {
			result += level.toString() + "    ";
		}
		
		return result;
	}

	@XmlElement(name = "level", required = true)
	private ArrayList<Level> children;

	public ArrayList<Level> getChildren() {
		return children;
	}

	public void setChildren(ArrayList<Level> childs) {
		children = childs;
	}
}

//Level 模型
package com.serializations.model;

import java.util.ArrayList;

import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import javax.xml.bind.annotation.XmlType;

@XmlAccessorType(XmlAccessType.FIELD)
@XmlRootElement(name="level")
public class Level {
	@XmlAttribute(name = "id", required = true)
	private String id;

	@Override
	public String toString() {
		return "Level [id=" + id + ", name=" + name + ", lev=" + lev + ", children=" + children + "]";
	}

	@XmlAttribute(name = "name", required = true)
	private String name;
	
	@XmlAttribute(name = "lev", required = true)
	private String lev;
	@XmlElement(name = "level", required = true)
	private ArrayList<Level> children;

	public String getLev() {
		return lev;
	}

	public void setLev(String lev) {
		this.lev = lev;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public ArrayList<Level> getChildren() {
		return children;
	}

	public void setChildren(ArrayList<Level> child) {
		this.children = child;
	}
}

```

创建好模型之后开始使用帮助工具，该工具可以方便Java对象和XML字符串之间相互转换，具体细节如下：
```java
package com.serializations.util;

import java.io.StringReader;  
import java.io.StringWriter;  
  
import javax.xml.bind.JAXBContext;  
import javax.xml.bind.Marshaller;  
import javax.xml.bind.Unmarshaller; 

public class JAXBUtil {
	 /** 
     * JavaBean转换成xml 
     * 默认编码UTF-8 
     * @param obj 
     * @param writer 
     * @return  
     */  
    public static String convertToXml(Object obj) {  
        return convertToXml(obj, "UTF-8");  
    }  
  
    /** 
     * JavaBean转换成xml 
     * @param obj 
     * @param encoding  
     * @return  
     */  
    public static String convertToXml(Object obj, String encoding) {  
        String result = null;  
        try {  
            JAXBContext context = JAXBContext.newInstance(obj.getClass());  
            Marshaller marshaller = context.createMarshaller();  
            marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);  
            marshaller.setProperty(Marshaller.JAXB_ENCODING, encoding);  
  
            StringWriter writer = new StringWriter();  
            marshaller.marshal(obj, writer);  
            result = writer.toString();  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
  
        return result;  
    }  
  
    /** 
     * xml转换成JavaBean 
     * @param xml 
     * @param c 
     * @return 
     */  
    @SuppressWarnings("unchecked")  
    public static <T> T converyToJavaBean(String xml, Class<T> c) {  
        T t = null;  
        try {  
            JAXBContext context = JAXBContext.newInstance(c);  
            Unmarshaller unmarshaller = context.createUnmarshaller();  
            t = (T) unmarshaller.unmarshal(new StringReader(xml));  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
  
        return t;  
    }  
}

```

转换案例：
```java
package com.serializations.controller;

import java.awt.List;
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.util.ArrayList;

import com.serializations.model.AllProductLevels;
import com.serializations.model.Level;
import com.serializations.util.JAXBUtil;

public class JAXBController {
	public void serialize() {
		ArrayList<Level> children = new ArrayList<Level>();

		Level level = new Level();
		level.setId("1");
		level.setName("张三");
		level.setLev("1");

		Level level2 = new Level();
		level2.setId("1");
		level2.setName("李四");
		level2.setLev("2");
		level2.setChildren(null);

		Level level3 = new Level();
		level3.setId("2");
		level3.setName("李四22");
		level3.setLev("2");
		level3.setChildren(null);

		Level level4 = new Level();
		level4.setId("3");
		level4.setName("李四23");
		level4.setLev("2");
		level4.setChildren(null);

		ArrayList<Level> list = new ArrayList<Level>();
		list.add(level2);
		list.add(level3);
		list.add(level4);
		level.setChildren(list);

		Level level5 = new Level();
		level5.setId("2");
		level5.setName("李四");
		level5.setLev("1");
		level5.setChildren(null);

		children.add(level);
		children.add(level5);

		AllProductLevels levels = new AllProductLevels();
		levels.setChildren(children);

		String str = JAXBUtil.convertToXml(levels);
		System.out.println(str);
	}

	public void deSerialize() {
		String xml = "<?xml version=\"1.0\" encoding=\"UTF-8\" standalone=\"yes\"?>" + "<AllProductLevels>"
				+ "<level id=\"1\" name=\"张三\" lev=\"1\">" + "<level id=\"1\" name=\"李四\" lev=\"2\"/>"
				+ "<level id=\"2\" name=\"李四22\" lev=\"2\"/>" + "<level id=\"3\" name=\"李四23\" lev=\"2\"/>" + "</level>"
				+ "<level id=\"2\" name=\"李四\" lev=\"1\"/>" + "</AllProductLevels>";
		
		AllProductLevels levels = (AllProductLevels)JAXBUtil.converyToJavaBean(xml, AllProductLevels.class);
		System.out.println(levels);
	}

	public void deSerializeFromFile() throws Exception{
		BufferedReader reader = new BufferedReader(new InputStreamReader(new FileInputStream("AllProduct.xml"),"UTF-8"));
		String data = null;
		String result = "";
		while((data = reader.readLine()) != null) result += data;
		
		System.out.println(result);
		
		AllProductLevels levels = (AllProductLevels)JAXBUtil.converyToJavaBean(result, AllProductLevels.class);
		
		System.out.println(levels.getChildren().get(0).getName());
	}
}

```
执行结果如下：
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<AllProductLevels>
    <level id="1" name="张三" lev="1">
        <level id="1" name="李四" lev="2"/>
        <level id="2" name="李四22" lev="2"/>
        <level id="3" name="李四23" lev="2"/>
    </level>
    <level id="2" name="李四" lev="1"/>
</AllProductLevels>
```
解析结果：
```bash
Level [id=1, name=张三, lev=1, children=[Level [id=1, name=李四, lev=2, children=null], Level [id=2, name=李四22, lev=2, children=null], Level [id=3, name=李四23, lev=2, children=null]]]    Level [id=2, name=李四, lev=1, children=null]    
```