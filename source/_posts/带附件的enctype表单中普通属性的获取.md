---
title: 带附件的enctype表单中普通属性的获取
date: 2016-09-07 09:33:04
categories: java
tags: 表单元素获取
---
我们在平时开发过程中，经常会遇到一些表单，比如用户信息修改、数据资源添加等等，并且这些表单都会带有附件：图片、其他文件；所以我们平时开发过程中经常需要处理两种表单，一种是带附件的表单，一种是不带附件的表单；因为两种表单向后台提交数据的格式不同，后者是以流的方式将表单数据提交到后台的。所以我们下面分两块描述两种获取不同表单的方式

### 一、普通表单(不带文件域)，基于普通表单封装成了一个通用的方法
```java

	/**
     * 将request对象解析处理成map
     * @param request
     * @return
     */
    public static Map<String,String> transToMAP(HttpServletRequest request){
            Map returnMap = new HashMap();
            Iterator entries = request.getParameterMap().entrySet().iterator();
            Map.Entry entry;
            while (entries.hasNext()) {
                String name = "";
                String value = "";
                entry = (Map.Entry) entries.next();
                name = (String) entry.getKey();
                Object valueObj = entry.getValue();
                if(null == valueObj){
                    value = "";
                }else if(valueObj instanceof String[]){
                    String[] values = (String[])valueObj;
                    for(int i=0;i<values.length;i++){
                        value = values[i] + ",";
                    }
                    value = value.substring(0, value.length()-1);
                }else{
                    value = valueObj.toString();
                }
                returnMap.put(name, value);
            }
        return  returnMap;
    }

```
  
### 一、普通表单(带文件域)，基于附件表单封装成了一个通用的方法
```java

	/**
     * 将request对象解析处理成map
     * @param request
     * @return
     */
    public static Map<String,String> transToMAP(HttpServletRequest request,String url){
        Map returnMap = new HashMap();
        boolean isMultipart = ServletFileUpload.isMultipartContent(request);

		//如果multipart/form-data类型的表单（带附件）
        if (isMultipart){  
                FileItemFactory factory = new DiskFileItemFactory();
                ServletFileUpload upload = new ServletFileUpload( factory );
                try {
                    List<FileItem> uploadItemList = upload.parseRequest(request);
                    for( FileItem uploadItem : uploadItemList ){

						//文件表单中的普通form表单域，比如username、phone等属性
                        if( uploadItem.isFormField() ){ 
                            String fieldName = uploadItem.getFieldName();
                            String value = uploadItem.getString();
                            returnMap.put(fieldName,value);

                        }else{  //form表单中的file文件域
                        	 
                            try{
                            	/**
                            	 * 1、从type="file"文件域中获取文件流、字节数组等等信息
                            	 */
                            	 String fieldname = uploadItem.getFieldName();
                                 String filename = FilenameUtils.getName(uploadItem.getName());
                                 InputStream filecontent = uploadItem.getInputStream();
                                 
                                 byte[] bytes = uploadItem.get();
                                 	
                                 /**
                             	 * 2、把对应的文件上传到文件服务器中，或者把文件写入磁盘上面
                             	 * 
                             	 * ........
                             	 */
                                 
                                 returnMap.put(fieldname,filename);//此处这是例子，具体根据你自己的业务封装
                            } catch (IOException e) {
                                e.printStackTrace();
                            } finally {
                            }
                        }
                    }
                } catch (FileUploadException e) {
                    e.printStackTrace();
                } catch (Exception e){
                    e.printStackTrace();
                }
        }
		return  returnMap;
	}
```
注：将reques对象封装成map集合工具类，主要解决如下问题：

  1、当仅仅处理简单的form表单时，可以通过该工具类之间获取request对象中的属性集合，并且针对一些特殊情况(为“空”的属性、编码转换)可以达到集中处理并统一返回

  2、当仅仅处理带file的form表单时，提交到后台是已流的方式传输；解决通过request.getParameter不能获取属性的问题，并且可以集中统一处理
