---
layout: post
title:  "React-Native笔记"
tags:
  - React
  - React Native
---


# React Native笔记







在开始运行命令前，需要将adb配置到系统环境变量中。然后打开cmd窗口，就可以直接运行adb命令了

显示系统中全部Android平台：
android list targets
显示系统中全部AVD（模拟器）：
android list avd
创建AVD（模拟器）：
android create avd -n 模拟器名称 -t target的id（android-8）
启动模拟器：
emulator -avd 模拟器名称
删除AVD（模拟器）：
android delete avd -n 模拟器名称
启动Activity
adb shell am start -n 包名/包名＋类名（-n 类名,-a action,-d date,-m MIME-TYPE,-c category,-e 扩展数据,等）。
指定虚拟机安装app
adb -s emulator-554 install ..apk

作者：望月成三人
链接：http://www.jianshu.com/p/8211954b6049
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。








