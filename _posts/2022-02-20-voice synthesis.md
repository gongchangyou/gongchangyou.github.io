---
layout: post
title: "voice"
date: 2022-02-20 10:05:06 +0800
comments: true
category: python
tag: [python]
---



#  python AI语音合成

https://mp.weixin.qq.com/s/ROETeFo4Q__8hkX9Ss-CAw 看到这篇报道。觉得可以试试将自己的语音变成语音包，这样就可以给自己读文章了，或者去喜马拉雅领任务。



pyttsx3是一套基于实现SAPI5文语合成引擎的Python封装库，该库的设计者为Natesh M Bhat，该库其实是 [pyTTS](https://pypi.org/project/pyTTS/) 和 [pyttsx](https://github.com/RapidWareTech/pyttsx) 项目的延续，pyttsx3主要是为Python3版本设计的，但同时也兼容Python2。JaysPySPEECH使用的是pyttsx3 2.7。



但是因为依赖的是当前PC的语音环境，所以无法将我们自己的语音导入。



## [MockingBird](https://github.com/babysor/MockingBird)

https://github.com/babysor/MockingBird/blob/main/README-CN.md

## 注意第8步下载数据集非常大，建议先做



我是ubuntu系统

1. 安装 [PyTorch](https://pytorch.org/get-started/locally/) 

   1. 先看本地有没有python.一般是python3了

   2. 再安装pip: apt install python3-pip

   3. 安装python torch  : pip3 install torch torchivision torchaudio

   4. 验证:

      ```
      import torch
      x = torch.rand(5,3)
      print(x)
      ```

2. 安装 [ffmpeg](https://ffmpeg.org/download.html#get-packages)。

   1. 下载源码/source code 并解压缩

   2. 库的安装

      ```
      sudo apt-get install -y autoconf automake build-essential git libass-dev libfreetype6-dev libsdl2-dev libtheora-dev libtool libva-dev libvdpau-dev libvorbis-dev libxcb1-dev libxcb-shm0-dev libxcb-xfixes0-dev pkg-config texinfo wget zlib1g-dev
      ```

      ```
      apt install libavformat-dev
      apt install libavcodec-dev
      apt install libswresample-dev
      apt install libswscale-dev
      apt install libavutil-dev
      apt install libsdl1.2-dev
      ```

      如果没用的话 先 apt-get update, 再 apt-get install XXX.

   3. 安装libx264 

      ```
      apt-get install libx264-dev
      ```

   4. 安装 yasm
       ```
       apt-get install yasm
       ```
   5. 安装 ffmpeg

   6. ```
      ./configure   --enable-shared  --prefix=/usr/local/ffmpeg  --enable-gpl --enable-libx264 --extra-cflags=-I/usr/local/ffmpeg/include --extra-ldflags=-L/usr/local/ffmpeg/lib
      make
      make install
      ```

3. 安装 MockingBird

   ```
   git clone git@github.com:babysor/MockingBird.git
   
   pip install -r requirements.txt
   ```

4. 安装 webrtcvad

   ```
   pip install webrtcvad-wheels
   ```

5. 在 MockingBird/synthesizer 下新建文件夹 saved_models

6. 把下载到的或者训练好的 pt文件拷贝到 saved_models文件夹下

7. 浏览器访问 localhost:8080 ， 如果端口被占用的话，可以修改default.py 变更 PORT

8. 下载数据集 [https://github.com/babysor/MockingBird/blob/main/README-CN.md#1%E6%95%B8%E6%93%9A%E9%9B%86%E5%93%AA%E8%A3%A1%E4%B8%8B%E8%BC%89](https://github.com/babysor/MockingBird/blob/main/README-CN.md#1%E6%95%B8%E6%93%9A%E9%9B%86%E5%93%AA%E8%A3%A1%E4%B8%8B%E8%BC%89)

9. 将上一部的数据集 放到某个文件夹下 <datasets_root>, 该解压的解压

10. 打开工具

11. ```
    python demo_toolbox.py -d <datasets_root>
    ```

    

Notice：

1. 如果报错 size mismatch https://github.com/babysor/MockingBird/issues/37 一个兼容性的问题

   ```
   这个是我最近一个修复导致的不兼容问题， 你可以把文件中：synthesizer/utils/symbols.py 第11行的内容 改为：
   _characters = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz12340!\'(),-.:;? '
   即可。暂时先不要关闭这个issue吧。我看下遇到的人太多的话我做个兼容
   ```

2. 演示视频 [https://www.bilibili.com/video/BV1uh411B7AD/](https://www.bilibili.com/video/BV1uh411B7AD/)



### 可是实际操作下来，上传了我的音频，转换成一段刺耳的声音，无法将中文text 转换成音频

