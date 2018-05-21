---
title: Realm的使用—(React Native平台)
---

> 简单阐述 **Realm** 移动数据库的使用.

## Installation

```shell
npm install --save realm
```

## Link

- React Native **>=** 0.31.0

  ```shell
  react-native link realm
  ```

- React Native **<** 0.31.0

  ```shell
  rnpm link realm
  ```

## Use

- 创建文件（在这里取名为**RealmExample.js**）

  ```react
  const Realm = require("realm");

  const Example={
    name:"Example",		//取名为Example
    properties:{
      id: "int",			//唯一标识，类型为int
      title: "string"		//需要写入的内容，类型为string
    }
  };

  export const realmExample = new Realm({schema:[Example]});	//导出
  ```


- 使用**import**将该文件导入需要使用Realm的文件（在这里取名为**Example.js**）

  ```react
  import {realmExample} from "./RealmExample";
  ```

- **RealmExample.js**的文件中为 **Example** **添加**元素

  ```react
  realmExample.write(() => {
    realmExample.create("Example", {
      id: 1,			//该元素id为1
      title: "Test"	//该元素title为Test
    });
  });
  ```

- 在另一个文件 **读取** 以及**删除** **Example** 中的元素，首先也需要导入

  ```react
  import {realmExample} from "./RealmExample";
  ```

  - **读取**

    ```react
    let favorite = realmExample.objects("Example");

    //对Example进行遍历
    favorite.map((example) =>{

      	//输出对应元素的id
      	<Text>{example.id}</Text>
      
        //输出对应元素的title
        <Text>{example.title}</Text>
    })
    ```

  - **删除**

    ```react
    realmExample.write(() => {
      let DelExample = realmExample.objects("Example");
      
      //将Example中id为1元素取出
      let delExample = DelExample.filtered("id == 1");
      
      //将Example中id为1的元素删除
      realmExample.delete(delExample);
    });
    ```

## Install examples（官网Demo）

```shell
git clone https://github.com/realm/realm-js.git
```

```shell
cd realm-js
git submodule update --init --recursive
```

安卓平台上，需要添加下载NDK，还要设置ANDROID_NDK环境变量

```scala
export ANDROID_NDK=/usr/local/Cellar/android-ndk/r10e
```

