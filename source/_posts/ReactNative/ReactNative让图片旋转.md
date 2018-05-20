---
title: ReactNative 让图片旋转
---

>简单使用 Animated 实现图片旋转

```react
import React, {Component} from "react";
import {Animated, Easing, TouchableOpacity} from "react-native";

const TIMES = 5;		// 旋转次数

export default class CheckIn extends Component {
  constructor(props) {
    super(props);
    this.state = {
      rotateValue: new Animated.Value(0)
    };
  }

  render() {
    return (
      <TouchableOpacity onPress={() => this.onStartAnimate()}>
        <Animated.Image
          style = {[
            styles.roundImg,
            {
              transform:[
                {
                  rotate:this.state.rotateValue.interpolate({
                    inputRange: [0, 360],outputRange: ['0deg', '360deg']
                  })
                }
              ]
            }
          ]}
          source={ImageUrl}		// 旋转图片路径
          resizeMode = "contain" />
      </TouchableOpacity>
    );
  }
  
  // 点击开始旋转
  onStartAnimate() {
    this.state.rotateValue.setValue(0);
    Animated.timing(this.state.rotateValue,{
      toValue: 360 * TIMES,
      duration: 800 * TIMES,
      easing: Easing.linear
    }).start();
  }
}

const styles = {
  roundImg: {
    width: 50,
    height: 50
  }
};
```

