---
layout: post
title: "c++新建thread 调用java静态方法"
date: 2013-08-30 16:25:06 +0800
comments: true
category: c++
tag: [c++]
---
首先需要3个静态变量

static JavaVM *gs_jvm=NULL;//这个是JavaVM 两个线程共享这个变量

static jobject gs_createFolder = NULL;//通过 gs_createFolder = env->NewGlobalRef(ret);可以做到让两个线程共享一个jobject

static jmethodID gs_methodId = NULL;

在新建thread之前做以下事情：
{% highlight c %}
gs_jvm = JniHelper::getJavaVM();

CCLog("save javavm after");

JNIEnv *env;

gs_jvm->AttachCurrentThread(&env, 0);

jclass ret = 0;

ret = env->FindClass("com/gumichina/lib/Storage");

gs_createFolder = env->NewGlobalRef(ret);

gs_methodId = env->GetStaticMethodID(ret, "createExternalFolder", "(Ljava/lang/String;)Z");

//接下来就开你的thread吧：
int rc = pthread_create(&s_zipThread, NULL, unzipFileOnThread, NULL);
{% endhighlight %}

在thread中这样获取:
{% highlight java %}
JNIEnv *env;
gs_jvm->AttachCurrentThread(&env, NULL);//这里就获取了JavaVM的env
jclass cls = env->GetObjectClass(gs_createFolder);//把class巧妙的拿过来了

//调用的时候

std::string tempPath = path.substr(0, path.rfind("/"));

bool result = false;

jstring StringArg1 = env->NewStringUTF(tempPath.c_str());

result = env->CallStaticBooleanMethod(cls, gs_methodId, StringArg1);//cls 和methodId 齐活了！

env->DeleteLocalRef(StringArg1);
{% endhighlight %}

