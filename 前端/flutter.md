# 图像是如何显示的？

我们在屏幕上看到的所有内容都是计算机绘制出来的图像。无论是`视频、gif图片`、操作系统给我们看到的图形化界面中的画面，`都是图像`。

当图片连续播放的频率超过16帧（16张图片），人眼就会感觉非常流畅。

帧率（fps）：

刷新率：显示器的频率



# flutter概述

flutter是一个UI SDK（软件开发包）。

flutter中，`万物皆是Widget`。最终 形成Widget树。

 widget中不能定义状态，需创建一个单独的类，这个类负责维护状态。

- StatefulWidget：有状态的Widget
- StatelessWidget：无状态的Widget

## flutter环境搭建

1. 下载flutter SDK
2. 配置flutter环境变量、dart环境变量
3. flutter --version
4. flutter create xx
5. 跑到模拟器：ios模拟器---Xcode        andriod模拟器---Android Studio AVD Manager（开发工具创建模拟器）
6. 配置镜像

## 运行flutter项目

1. 冷启动：从0开始启动
2. 热重载：执行build方法
3. 热重启：重新运行整个app