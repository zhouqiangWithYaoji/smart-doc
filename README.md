<h1 align="center">Smart-Doc Project</h1>

![maven](https://img.shields.io/maven-central/v/com.github.shalousun/smart-doc)
[![License](https://img.shields.io/badge/license-Apache%202-green.svg)](https://www.apache.org/licenses/LICENSE-2.0)
![number of issues closed](https://img.shields.io/github/issues-closed-raw/shalousun/smart-doc)
![closed pull requests](https://img.shields.io/github/issues-pr-closed/shalousun/smart-doc)
![java version](https://img.shields.io/badge/JAVA-1.8+-green.svg)
[![chinese](https://img.shields.io/badge/chinese-中文文档-brightgreen)](https://github.com/shalousun/smart-doc/blob/master/README_CN.md)

## Introduce
smart-doc is a tool that supports both JAVA REST API and Apache Dubbo RPC interface document generation. Smart-doc is based on interface source code analysis to generate interface documents, and zero annotation intrusion.
You only need to write Javadoc comments when developing, smart-doc can help you generate Markdown or HTML5 document. 
smart-doc does not need to inject annotations into the code like Swagger.
## Features
- Zero annotation, zero learning cost, only need to write standard JAVA document comments.
- Automatic derivation based on source code interface definition, powerful return structure derivation support.
- Support Spring MVC, Spring Boot, Spring Boot Web Flux (Not support endpoint), Feign.
- Supports the derivation of asynchronous interface returns such as Callable, Future, CompletableFuture.
- Support JSR-303 parameter verification specification.
- Support for automatic generation of request examples based on request parameters.
- Support for generating JSON return value examples.
- Support for loading source code from outside the project to generate field comments (including the sources jar package).
- Support for generating multiple formats of documents: Markdown,HTML5,Asciidoctor,Postman Collection 2.0+,OpenAPI 3.0.
- Support for exporting error codes and data dictionary codes to API documentation.
- The debug html5 page fully supports file upload and download testing.
- Support Apache Dubbo RPC.
## Best Practice
smart-doc + [Torna](http://torna.cn) form an industry-leading document generation and management solution, using smart-doc to complete Java source code analysis and extract annotations to generate API documents without intrusion, and automatically push the documents to the Torna enterprise-level interface document management platform.

![smart-doc+torna](https://raw.githubusercontent.com/shalousun/smart-doc/master/images/smart-doc-torna.png)
## Getting Started
[Smart-doc Samples](https://github.com/shalousun/smart-doc-demo.git)。
```
# git clone https://github.com/shalousun/smart-doc-demo.git
```
This example already provides a static html document generated in advance. You can start the Spring Boot project and then go directly to `http://localhost:8080/doc/api.html` to view the interface documentation generated by smart-doc.
Of course, you can also browse `http://localhost:8080/doc/api.html`, 
which looks a html like generated by `asciidoctor-maven-plugin` plugin.
### Add Maven Plugin
Add [smart-doc-maven-plugin](https://github.com/smart-doc-group/smart-doc-maven-plugin) in your pom.xml.
```
<plugin>
    <groupId>com.github.shalousun</groupId>
    <artifactId>smart-doc-maven-plugin</artifactId>
    <version>[latest]</version>
    <configuration>
        <!--Specify the configuration file used to generate the document-->
        <configFile>./src/main/resources/smart-doc.json</configFile>
        <!--smart-doc implements automatic analysis of the dependency tree to load the source code of third-party dependencies. If some framework dependency libraries are not loaded, an error is reported, then use excludes to exclude-->
        <excludes>
            <!--The format is: groupId: artifactId; refer to the following-->
            <exclude>com.google.guava:guava</exclude>
        </excludes>
        <!--Since version 1.0.8, the plugin provides includes support-->
        <!--smart-doc can automatically analyze the dependency tree to load all dependent source code. In principle, it will affect the efficiency of document construction, so you can use includes to let the plugin load the components you configure.-->
        <includes>
            <!--The format is: groupId: artifactId; refer to the following-->
            <include>com.alibaba:fastjson</include>
        </includes>
    </configuration>
    <executions>
        <execution>
            <!--Comment out phase if you don't need to start smart-doc when compiling-->
            <phase>compile</phase>
            <goals>
                <!--smart-doc provides html, openapi, markdown, adoc and other goals-->
                <goal>html</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```
### Configuration
Create a JSON configuration file in your project. The smart-doc-maven-plugin plugin will use this configuration information.
For example, create `/src/main/resources/smart-doc.json` in the project. 
The configuration contents are as follows.

**Minimize configuration:**
```
{
   "allInOne": true, // whether to merge documents into one file, generally recommended as true
   "isStrict": false,//If the strict mode is set to true, Smart-doc forces that the public method in each interface in the code has a comment.
   "outPath": "src/main/resources/static/doc" //Set the api document output path.
}
```
Only the above three simple configuration items can make smart-doc-maven-plugin work. 
In fact, only the outPath configuration item is necessary.

**Detailed configuration content:**

When you need to use smart-doc to generate more API document information, you can add detailed configuration content.
```
{
  "serverUrl": "http://127.0.0.1", // Set the server address, not required
  "isStrict": false, // whether to enable strict mode
  "allInOne": true, // whether to merge documents into one file, generally recommended as true
  "outPath": "D: // md2", // Specify the output path of the document
  "coverOld": true, // Whether to overwrite old files, mainly used for mardown file overwrite
  "style":"xt256", //set highlight
  "createDebugPage": true,//Create a page that can be used to test your APIs like swagger
  "language":"ENGLISH",//support ENGLISH and CHINESE
  "packageFilters": "", // controller package filtering, multiple package names separated by commas
  "md5EncryptedHtmlName": false, // only used if each controller generates an html file
  "projectName": "smart-doc", // Configure your own project name
  "skipTransientField": true, // Not currently implemented
  "requestFieldToUnderline":true, //convert request field to underline
  "responseFieldToUnderline":true,//convert response field to underline
  "sortByTitle":false,//Sort by interface title, the default value is false
  "showAuthor":true,// display author,default is true
  "inlineEnum":true,// Set to true to display enumeration details in the parameter table
  "recursionLimit":7,// Set the number of recursive executions to avoid stack overflow, the default is 7
  "allInOneDocFileName":"index.html",//Customize the output document name
  "requestExample":"true",//Whether to display the request example in the document, the default value is true.
  "responseExample":"true",//Whether to display the response example in the document, the default is true.
  "displayActualType":false,//display actual type of generic,
  "urlSuffix":".do",//Support the url suffix of the old SpringMVC project,@since 2.1.0
  "appKey": "xxx",// torna appKey, @since 2.0.9
  "appToken": "xxx", //torna appToken,@since 2.0.9
  "secret": "xx",//torna secret，@since 2.0.9
  "openUrl": "torna server/api/",//torna server url,@since 2.0.9
  "tornaDebug":false,"// show log while set true
  "ignoreRequestParams":[ //The request parameter object will be discarded when generating the document.@since 1.9.2
       "org.springframework.ui.ModelMap"
  ],
  "dataDictionaries": [{// Configure the data dictionary, no need to set
       "title": "Order Status", // The name of the data dictionary
       "enumClassName": "com.power.doc.enums.OrderEnum", // Data dictionary enumeration class name
       "codeField": "code", // The field name corresponding to the data dictionary dictionary code
       "descField": "desc" // Data dictionary object description information dictionary
  }],
  "errorCodeDictionaries": [{// error code list, no need to set
       "title": "title",
       "enumClassName": "com.power.doc.enums.ErrorCodeEnum", // Error code enumeration class
       "codeField": "code", // Code field name of the error code
       "descField": "desc" // Field name corresponding to the error code description
  }],
  "revisionLogs": [{// Set document change records, no need to set
       "version": "1.0", // Document version number
       "revisionTime": "2020-12-31 10:30", //revision time
       "author": "author", // Document change author
       "status": "update", // Change operation status, generally: create, update, etc.
       "remarks": "desc" // Change description
  }],
  "customResponseFields": [{// Customly add fields and comments. If api-doc encounters a field with the same name later, directly add a comment to the corresponding field. It is not necessary.
       "name": "code", // Override the response code field
       "desc": "Response code", // Override field comment of response code
       "value": "00000" // Set the value of the response code
  }],
  "customRequestFields": [{//@since 2.1.3
       "name":"code", //Override the request code field
       "desc":"request code", //Override field comment of response code
       "ownerClassName":"com.xxx.constant.entity.Result",
       "value":"200", // Set the value of the response code
       "required":true,
       "ignore":false
  }],
  "apiObjectReplacements": [{ // Supports replacing specified objects with custom objects to complete document rendering
       "className": "org.springframework.data.domain.Pageable",
       "replacementClassName": "com.power.doc.model.PageRequestDto" //Use custom PageRequestDto instead of JPA Pageable for document rendering.
  }],
  "rpcApiDependencies":[{ // Your Apache Dubbo api interface module dependency description.
       "artifactId":"SpringBoot2-Dubbo-Api",
       "groupId":"com.demo",
       "version":"1.0.0"
  }],
  "apiConstants": [{//Configure your own constant class, smart-doc automatically replaces with a specific value when parsing to a constant
       "constantsClassName": "com.power.doc.constants.RequestParamConstant"
   }],
  "responseBodyAdvice":{ //Support ResponseBodyAdvice
       "className":"com.power.common.model.CommonResult" // Standard POJO for Response
  },
  "rpcConsumerConfig": "src/main/resources/consumer-example.conf",//dubbo consumer config example
  "requestHeaders": [{// Set global request headers, no need to set
       "name": "token",
       "type": "string",
       "desc": "desc",
       "required": false,
       "since": "-"
  }]
}
```
**Note:** This JSON configuration can be converted into JSON using smart-doc's ApiConfig Object. 
So the project configuration can also refer to the introduction of smart-doc.
### Generated document
#### Run Plugin with MAVEN command
```
// Generate html
mvn -Dfile.encoding=UTF-8 smart-doc:html
// Generate markdown
mvn -Dfile.encoding=UTF-8 smart-doc:markdown
// Generate adoc
mvn -Dfile.encoding=UTF-8 smart-doc:adoc
// Generate postman collection
mvn -Dfile.encoding=UTF-8 smart-doc:postman
// Generate document and send to Torna
mvn -Dfile.encoding=UTF-8 smart-doc:torna-rest

// Apache Dubbo RPC
// Generate html
mvn -Dfile.encoding=UTF-8 smart-doc:rpc-html
// Generate markdown
mvn -Dfile.encoding=UTF-8 smart-doc:rpc-markdown
// Generate adoc
mvn -Dfile.encoding=UTF-8 smart-doc:rpc-adoc
```
**Note:** Under the window system, if you use the maven command line to perform document generation, 
non-English characters may be garbled, so you need to specify `-Dfile.encoding = UTF-8` during execution.

View maven's coding
```
# mvn -version
Apache Maven 3.3.3 (7994120775791599e205a5524ec3e0dfe41d4a06; 2015-04-22T19:57:37+08:00)
Maven home: D:\ProgramFiles\maven\bin\..
Java version: 1.8.0_191, vendor: Oracle Corporation
Java home: D:\ProgramFiles\Java\jdk1.8.0_191\jre
Default locale: zh_CN, platform encoding: GBK
OS name: "windows 10", version: "10.0", arch: "amd64", family: "dos"
```
#### Run Plugin in IDEA
On Use IntelliJ IDE, if you have added smart-doc-maven-plugin to the project, 
you can directly find the plugin smart-doc plugin and click to generate API documentation.

![smart-doc-maven-plugin](https://raw.githubusercontent.com/shalousun/smart-doc-maven-plugin/master/images/idea.png)
### Use Gradle Plugin
If you use gradle to build the project, you can refer to the documentation of the gradle plugin to integrate,
[smart-doc-gradle-plugin](https://github.com/smart-doc-group/smart-doc-gradle-plugin/blob/master/README.md)
### Generated document example
#### Interface header rendering
![header](https://images.gitee.com/uploads/images/2019/1231/223538_be45f8a9_144669.png "header.png")
#### Request parameter example rendering
![request-params](https://images.gitee.com/uploads/images/2019/1231/223710_88933f55_144669.png "request.png")
#### Response parameter example renderings
![response-fields](https://images.gitee.com/uploads/images/2019/1231/223817_32bea6dc_144669.png "response.png")
## Integration through unit tests 
You can generate documentation by adding smart-doc dependencies directly to your project and then writing unit tests to start smart-doc. 
we still recommend that you use the smart-doc-maven-plugin plugin.

[Use smart-doc by junit test](https://github.com/smart-doc-group/smart-doc/wiki/Use-smart-doc-by-junit-test)
## Building
You could build with the following commands. (Java 1.8 is required to build the master branch)
```
mvn clean install -Dmaven.test.skip=true
```
## TODO
- Jakarta RS-API 2.x
## Other reference
- [Smart-doc manual](https://github.com/shalousun/smart-doc/wiki)

## Who is using
These are only part of the companies using smart-doc, for reference only. If you are using smart-doc, please [add your company here](https://github.com/shalousun/smart-doc/issues/12) to tell us your scenario to make smart-doc better.

![IFLYTEK](https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/iflytek.png)
&nbsp;&nbsp;<img src="https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/oneplus.png" title="OnePlus" width="83px" height="83px"/>
&nbsp;&nbsp;<img src="https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/xiaomi.png" title="Xiaomi" width="170px" height="83px"/>
<img src="https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/yuanmengjiankang.png" title="yuanmengjiankang" width="260px" height="83px"/>
<img src="https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/zhongkezhilian.png" title="zhongkezhilian" width="272px" height="83px"/>
<img src="https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/puqie_gaitubao_100x100.jpg" title="puqie" width="83px" height="83px"/>&nbsp;&nbsp;
<img src="https://raw.githubusercontent.com/shalousun/smart-doc/dev/images/known-users/tianbo-tech.png" title="tianbo tech" width="127px" height="70px"/>


## License
Smart-doc is under the Apache 2.0 license.  See the [LICENSE](https://github.com/shalousun/smart-doc/blob/master/LICENSE) file for details.
## Contact
Email： 836575280@qq.com
