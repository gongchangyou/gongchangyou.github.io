---

layout: post
title: "mmap"
date: 2022-06-01 10:25:06 +0800
comments: true
category: java
tag: [java]

---

# 零拷贝技术 mmap

参考文章: [https://www.jianshu.com/p/f90866dcbffc](https://www.jianshu.com/p/f90866dcbffc)

[https://juejin.cn/post/7016498891365302302](https://juejin.cn/post/7016498891365302302)



代码仓库: [https://github.com/gongchangyou/mmap](https://github.com/gongchangyou/mmap)



常用方法

force : 强制刷盘

slice: 创建一个新的字节缓冲区,其内容是该缓冲区内容的共享子序列



示例代码

```
@Slf4j
@SpringBootTest
class MmapApplicationTests {

    String str = "mouse 中文";

    int size = str.getBytes().length;

    @Test
    public void writeWithMap() throws IOException {
        try (RandomAccessFile file = new RandomAccessFile(new File("src/main/resources/big.www.flydean.com"), "rw"))
        {
            //get Channel
            FileChannel fileChannel = file.getChannel();
            //get mappedByteBuffer from fileChannel
            MappedByteBuffer buffer = fileChannel.map(FileChannel.MapMode.READ_WRITE, 0, 1024 );
            // check buffer
            log.info("is Loaded in physical memory: {}",buffer.isLoaded());  //只是一个提醒而不是guarantee
            log.info("capacity {}",buffer.capacity());
            //write the content
            buffer.put(str.getBytes());
        }
    }

    @Test
    public void readWithMap() throws IOException {
        try (RandomAccessFile file = new RandomAccessFile(new File("src/main/resources/big.www.flydean.com"), "r"))
        {
            //get Channel
            FileChannel fileChannel = file.getChannel();
            //get mappedByteBuffer from fileChannel
            MappedByteBuffer buffer = fileChannel.map(FileChannel.MapMode.READ_ONLY, 0, fileChannel.size());
            // check buffer
            log.info("is Loaded in physical memory: {}",buffer.isLoaded());  //只是一个提醒而不是guarantee
            log.info("capacity {}",buffer.capacity());
            byte[] bytes = new byte[size];

            //read the buffer
            for (int i = 0; i < bytes.length; i++)
            {
                bytes[i] = buffer.get();
//                log.info("get {}", buffer.get());
            }

            val str = new String(bytes);
            log.info("str= {}", str);
        }
    }


}

```

