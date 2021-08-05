# Rokid语音Android SDK使用文档

## 目录

* [一、概述](#一、概述)
* [二、集成准备](#二、集成准备)
  * [2.1 开发者账号注册](#2.1 开发者账号注册)
  * [2.2 设备注册](#2.2 设备注册)
  * [2.3 SDK获取](#2.3 SDK获取)
* [三、集成说明](#三、集成说明)
  * [3.1 硬件及系统环境](#3.1 硬件及系统环境)
  * [3.2 所需Android权限](#3.2 所需Android权限)
  * [3.3 配置文件](#3.3 配置文件)
    * [3.3.1 使用说明](#3.3.1 使用说明)
    * [3.3.2 主要文件说明](#3.3.2 主要文件说明)
  * [3.4 Android 项目gradle引入SDK相关aar](#3.4 Android 项目gradle引入SDK相关aar)
  * [3.5 SDK初始化](#3.5 SDK初始化)
    * [3.5.1 启动语音服务](#3.5.1 启动语音服务)
    * [3.5.2 注册返回结果监听](#3.5.2 注册返回结果监听)
  * [3.6 校验机制](#3.6 校验机制)
  * [3.7 基础识别结果处理](#3.7 基础识别结果处理)
    * [3.7.1 语音识别Asr处理](#3.7.1 语音识别Asr处理)
    * [3.7.2 语音识别技能nlp处理](#3.7.2 语音识别技能nlp处理)
    * [3.7.3 语音识别Event处理](#3.7.3 语音识别Event处理)
    * [3.7.4 语音识别Error处理](#3.7.4 语音识别Error处理)
* [四、SDK具体功能使用](#四、SDK具体功能使用)
  * [4.1 使用NlpConsumer来继续分发NLP](#4.1 使用NlpConsumer来继续分发NLP)
  * [4.2 使用RokidAiSkillEngine、RokidAiCloudApp 来处理Rokid 云端技能](#4.2 使用RokidAiSkillEngine、RokidAiCloudApp 来处理Rokid 云端技能)
  * [4.3 使用RokidAiSkillEngine、RokidAILocalSkillSdk 来处理Rokid 本地技能](#4.3 使用RokidAiSkillEngine、RokidAILocalSkillSdk 来处理Rokid 本地技能)
  * [4.4 使用算法处理过程中的相关pcm数据](#4.4 使用算法处理过程中的相关pcm数据)
  * [4.5 使用kenobi设备](#4.5 使用kenobi设备)
  * [4.6 关闭speech，只接收语音event、离线asr、pcm](#4.6 关闭speech，只接收语音event、离线asr、pcm)
  * [4.7 使用离线asr功能](#4.7 使用离线asr功能)
  * [4.8 使用有线mac地址来做校验](#4.8 使用有线mac地址来做校验)
  * [4.9 使用turenproc方式运行语音SDK](#4.9 使用turenproc方式运行语音SDK)
* [五、主要API参考](#五、主要API参考)
  * [5.1 IRokidAudioAiService.java](#5.1 IRokidAudioAiService.java)
  * [5.2 IRokidAudioAiListener.java](#5.2 IRokidAudioAiListener.java)
  * [5.3 ServiceConfig.java](#5.3 ServiceConfig.java)
  * [5.4 VoiceRecognize.Event](#5.4 VoiceRecognize.Event)
  * [5.5 RecordClientManager](#5.5 RecordClientManager)
  * [5.6 ClientSocketManager.IConnnectListener](#5.6 ClientSocketManager.IConnnectListener)
  * [5.7 PcmClientManager](#5.7 PcmClientManager)
  * [5.8 IReceiverPcmListener](#5.8 IReceiverPcmListener)
  * [5.9 错误码说明](#5.9 错误码说明)
* [六、其他说明](#六、其他说明)
  * [6.1 最新版本](#6.1 最新版本)
  * [6.2 参考demo地址（包含最新SDK）](#6.2 参考demo地址（包含最新SDK）)
  * [6.3 主要调用流程图](#6.3 主要调用流程图)



## 一、概述

RokidAiSdk 集合前端算法Turen相关功能，通过speech、云端来进行语音语义识别，并返回处理信息给三方开发者，附带Rokid的TTS功能，同时适用于Rokid外置语音Kenobi方案。
面向Android 软件开发工程师, 提供aar形式的SDK，简化接入流程，适应单独apk形式的集成，更方便用户接入Rokid AI语音技术。
RokidAiSdk 是语音技能全链路解决方案的核心引擎，用户接收到nlp数据，可以选择继续集成Rokid云端技能方案（RokidAiSkillEngine、RokidAiCloudApp）、本地技能方案（RokidLocalSkillSdk），打造一款完美的智能语音交互体验设备。

## 二、集成准备

### 2.1 开发者账号注册

Rokid 有自己的开发者账号、社区体系，使用Rokid的技术需要在[Rokid开放平台](https://developer.rokid.com/#/)的注册**开发者账号**，同时可以获取相关技术支持。

### 2.2 设备注册

Rokid 语音技术需要硬件设备做承接，相关的硬件信息同时也需要在Rokid开发者网站上进行产品报备。在[Rokid开放平台](https://developer.rokid.com/#/)的控制台的语音接入中，创建对应的产品，Rokid后台会自动生成`Key`、`Secret`、`deviceTypeId`，同时会为每个设备预生成10个测试用的`deviceId(sn)`和`seed`，这些信息会被组合起来做授权认证，保证语音SDK正常初始化。

  * **Rokid 开放平台介绍** ： https://developer.rokid.com/docs/
  * **产品创建及Sn导入** ： https://developer.rokid.com/docs/5-enableVoice/rokid-vsvy-sdk-docs/rookie-guide/rookie-guide-end.html

### 2.3 SDK获取

1、可以从[Rokid开放平台](https://developer.rokid.com/#/)开放平台获取，注册相应类型设备的同时，会生成相应的SDK，供开发者下载。参考[产品创建及Sn导入](https://developer.rokid.com/docs/5-enableVoice/rokid-vsvy-sdk-docs/rookie-guide/rookie-guide-end.html)。

2、从参考Demo程序获取最新SDK：https://github.com/rokid/RokidVoiceAIDemo

### 2.4 主要API参考

{% urlembed %}
https://leoeco2000.github.io/ActionTest/vai-sdk-docs/java-api/annotated.html
{% endurlembed %}

{% urlembed %}
https://leoeco2000.github.io/ActionTest/vai-sdk-docs/cxx-api/annotated.html
{% endurlembed %}

<!-- {% urlembed %} -->
<!-- https://github.com/leoeco2000/ActionTest/vai-sdk-docs/cxx-api/index.html -->
<!-- {% endurlembed %} -->

<!-- {% urlembed %} -->
<!-- https://github.com/leoeco2000/ActionTest.git/vai-sdk-docs/cxx-api/index.html -->
<!-- {% endurlembed %} -->

<!-- {% include "../../../vai-sdk-docs/cxx-api/index.html" %} -->
<!-- {% include "git+https://github.com/leoeco2000/ActionTest.git/vai-sdk-docs/cxx-api/index.html" %} -->
