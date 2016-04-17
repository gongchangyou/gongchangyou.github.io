---
layout: post
title: "c++读写文件"
date: 2013-11-08 16:25:06 +0800
comments: true
category: c++
tag: [c++]
---

C++代码
 

{% highlight c++ %}
std::string filePath = "/file/to/path";

ofstream ofile;

ofile.open(filePath.c_str());

Json::Value dlcVersion;

dlcVersion["version"] = pageJson["data"]["dlc_version"].asCString();

ofile << dlcVersion.toStyledString();

//std::string fileContent = std::string("filecontent");

//ofile << fileContent;

ofile.close();
{% endhighlight %}
<span style="color: #ff0000;">
注意 ofstream是写入模式，文件中原有的内容会被清空。
ifstream ifile；是读取模式,
</span>

 {% highlight c++  %}
 /bool fileToJSON( string path, Json::Value &value ){
std::ifstream t( path.c_str() );

std::string str;

t.seekg(0, std::ios::end);

str.reserve(t.tellg());

t.seekg(0, std::ios::beg);

str.assign((std::istreambuf_iterator<char>(t)),

std::istreambuf_iterator<char>());

Json::Reader reader = Json::Reader();

return reader.parse(str, value);
}
{% endhighlight %}
