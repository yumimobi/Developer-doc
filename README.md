# Developer-doc[TOC]

#Warning Log

1. Unity Interstitial & Video SDK， GameID需使用同一个。
2. 300之后版本非Wi-Fi不请求视频广告。
3. 验证 podspec 文件需注意： s.vendored_libraries = libxxx.a (前缀必须为lib)
4. 视频SDK调用debugcenter之后会导致单例对象delegate被释放
5. 303，304版本不支持 Xcode9 之前版本
6. Admob 某些情况需要GLKit.framework
