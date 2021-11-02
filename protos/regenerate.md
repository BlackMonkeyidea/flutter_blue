// Copyright 2017, Paul DeMarco.\
// All rights reserved. Use of this source code is governed by a\
// BSD-style license that can be found in the LICENSE file.

# Generate protobuf files in Dart
1. If upgrading, delete all proto files from /home/.pub-cache/bin
1. Clone the latest dart-protoc-plugin from https://github.com/dart-lang/protobuf
1. Run `pub install` inside protobuf/protoc_plugin
1. Run `pub global activate protoc_plugin` to get .dart files into /home/.pub-cache/bin/
1. Get the latest linux protoc compiler from https://github.com/google/protobuf/releases/ (protoc-X.X.X-linux-x86_64.zip)
1. Copy /bin/protoc into /home/.pub-cache/bin/
1. Run the following commands from this project's protos folder
```protoc --dart_out=../lib/gen ./flutterblue.proto```
```protoc --objc_out=../ios/gen ./flutterblue.proto```
```protoc --java_out=../android/src/main/java ./flutterblue.proto```


在build.gradle修改以下内容

```groovy
protobuf {
    // Configure the protoc executable
    protoc {
        // Download from repositories
        artifact = 'com.google.protobuf:protoc:3.19.1'
    }
    plugins {
        javalite {
            // The codegen for lite comes as a separate artifact
            artifact = 'com.google.protobuf:protoc-gen-javalite:3.0.0'
        }
    }
    generateProtoTasks {
        all().each { task ->
            task.plugins {
                javalite { }
            }
        }
    }
}

//获取local.properties配置文件
def localProperties = new Properties()
def localPropertiesFile = rootProject.file('local.properties')
if (localPropertiesFile.exists()) {
    localPropertiesFile.withReader('UTF-8') { reader ->
        localProperties.load(reader)
    }
}
//获取flutter的sdk路径
def flutterRoot = localProperties.getProperty('flutter.sdk')
if (flutterRoot == null) {
    throw new GradleException("Flutter SDK not found. Define location with flutter.sdk in the local.properties file.")
}

dependencies {
    implementation 'com.google.protobuf:protobuf-java:3.19.1'
    implementation 'com.google.protobuf:protoc:3.19.1'
    implementation 'androidx.appcompat:appcompat:1.0.0'
    api 'androidx.core:core:1.0.1'

    compileOnly files("$flutterRoot/bin/cache/artifacts/engine/android-arm/flutter.jar")
    compileOnly 'androidx.annotation:annotation:1.1.0'
}

```
