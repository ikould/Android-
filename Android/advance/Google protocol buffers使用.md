# 前言
> Google potocol buffers主要作用于序列化能够更省空间和时间，主要在空间上体现比较明显。而本次在项目上使用它也是因为其节省空间这个特性。

> 环境配置说明：如果是Java后端，需要在本地安装Protocol编译器，用于生成Protocol实体类。如果是Android开发，则不需要，在Android studio中安装插件，写好.proto文件，build clean下就可以了，具体见下文。

# 安装Protocol编译器
## Clone代码
> git clone https://github.com/google/protobuf.git

## 运行autogen
> 进入protobuf目录并执行 autogen.sh

./autogen.sh 

## 查看配置
> 通过如下命令了解我们可以对protobuf的编译做哪些配置，以及默认配置的信息

./configure --help

## 编译配置
> 执行configure对编译进行配置，并生成makefile文件

    ./configure 

## 编译并安装

    make
    sudo make install

## 安装之后执行如下命令以确认已经装好
    protoc --version

##  创建 .proto 文件

    syntax = "proto3";
    option java_outer_classname = "ProtoMsg"; // 输出的java文件名

    message Msg{
        int32 code = 1;
        string msg = 2;
        Data data = 3;
    }

    message Data{
        int32 position = 1;
        string content = 2;
    }

> 文件格式简析：
2. 赋值：必须赋值，而且不能相同，但是不区分类型

## 编译 .proto 文件

    protoc -I=$SRC_DIR --java_out=$DST_DIR $SRC_DIR/Msg.proto

# Android Studio使用Protocol buffers
## 在项目的build.gradle中添加插件依赖

    dependencies {
        classpath 'com.google.protobuf:protobuf-gradle-plugin:0.8.6'
    }

## 在app build.gradle中添加：

    // 插件
    apply plugin: 'com.google.protobuf'
    // android区域块添加
    android {
        protobuf {
            protoc {
                artifact = 'com.google.protobuf:protoc:3.1.0'
            }
            generateProtoTasks {
                all().each { task ->
                    task.builtins {
                        remove java
                    }
                    task.builtins {
                        java { }
                    }
                }
            }
            generatedFilesBaseDir = "$projectDir/src/generated"
        }
        sourceSets {
            main {
                proto {
                    srcDir 'src/main/proto'
                    include '**/*.proto'
                }
                java {
                    srcDir 'src/main/java'
                }
            }
        }
    }
    // 依赖
    dependencies {
        api 'com.google.protobuf:protobuf-java:3.4.0'
        api 'com.google.protobuf:protoc:3.1.0'
    }

## 创建.proto文件
> 在我们项目的src/main下建立proto文件夹
右键创建file文件，命名以.proto结尾
格式见安装proto编译器

## build clean 
将在src/generated/debug/java中看到生成的文件

## 复制到项目
将生成的文件复制到指定的java bean包下，以便项目使用。

# 具体使用Google protocol buffers场景
## 创建实体类
    ProtoMsg.Data.Builder dataBuilder = ProtoMsg.Data.newBuilder();
    dataBuilder.setPostion(1);
    dataBuilder.setConetent("内容");
    ProtoMsg.Msg.Builder msgBuilder = ProtoMsg.Msg.newBuilder();
    msgBuilder.setCode(200);
    msgBuilder.setMsg("提示");
    msgBuilder.setData(dataBuilder);

## 输入输出流
> 正常读写文件，或者使用socket来通讯，也是将实体类转换为byte[]来保存或者传输，写的时候重新将byte[]转换为实体类。当然也有很多框架支持protocol buffer，可以直接传入protocol buffer实体类，比如Netty。但针对业务内部数据在偶尔使用的情况下，依然要主动序列和反序列化。
转换见下方：

> 转换为byte[]

    mBytes = mData.toByteArray();

> byte[]转换为实体类

    ProtoMsg.Msg msg = ProtoMsg.Msg.parseFrom(bytes);









