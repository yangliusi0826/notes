<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [CSS](#css)
  - [1. 设置input中placeholder的样式](#1-%E8%AE%BE%E7%BD%AEinput%E4%B8%ADplaceholder%E7%9A%84%E6%A0%B7%E5%BC%8F)
    - [input placeholder支持的属性](#input-placeholder%E6%94%AF%E6%8C%81%E7%9A%84%E5%B1%9E%E6%80%A7)
  - [2. 图片显示问题](#2-%E5%9B%BE%E7%89%87%E6%98%BE%E7%A4%BA%E9%97%AE%E9%A2%98)
    - [宽高固定的情况，保证图片不被拉伸变形](#%E5%AE%BD%E9%AB%98%E5%9B%BA%E5%AE%9A%E7%9A%84%E6%83%85%E5%86%B5%E4%BF%9D%E8%AF%81%E5%9B%BE%E7%89%87%E4%B8%8D%E8%A2%AB%E6%8B%89%E4%BC%B8%E5%8F%98%E5%BD%A2)
    - [固定宽度，高度自适应](#%E5%9B%BA%E5%AE%9A%E5%AE%BD%E5%BA%A6%E9%AB%98%E5%BA%A6%E8%87%AA%E9%80%82%E5%BA%94)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# CSS
## 1. 设置input中placeholder的样式
```css
::-webkit-input-placeholder { /* Chrome/Opera/Safari */ 
  /* 样式 */
}
::-moz-placeholder { /* Firefox 19+ */  
  /* 样式 */
}
:-ms-input-placeholder { /* IE 10+ */ 
  /* 样式 */
}
:-moz-placeholder { /* Firefox 18- */ 
  /* 样式 */
}
```

### input placeholder支持的属性
* font properties
* color
* background properties
* word-spacing
* letter-spacing
* text-decoration
* vertical-align
* text-transform
* line-height
* text-indent
* opacity


## 2. 图片显示问题
> 问题：将图片的宽度固定后图片被拉伸变形
``` html
<img src="text.png" />
```

### 宽高固定的情况，保证图片不被拉伸变形
1. 使用CSS3属性```object-fit```
    ``` css
    img {
      width: 200px;
      height: 200px;
      object-fit: cover;
    }
    ```
    **object-fit关键属性：**   
    * ```object-fit: fill``` 图片会被拉伸来完全填充。  
    * ```object-fit: contain``` 图片会被缩放，保持其宽高比，如果图片宽高比与框的宽高比不匹配将会出现留白。  
    * ```object-fit: cover```图片大小保持其宽高比来填充整个框，如果如果图片宽高比与框的宽高比不匹配，图片会被剪裁以适应。  

2. 在微信小程序中object-fit属性不生效，可以使用background-size来实现
    ``` html
    <div class="test-img"></div>
    ```
    ``` css
    .test-img {
      background-image: url('test.png');
      background-repeat: no-repeat;
      background-position: center center;
      background-size: cover/contain;
    }
    ```

### 固定宽度，高度自适应
1. 设置图片宽度大小，高度自适应  
    ``` css
    img {
      width: 200px;
      height: auto;
    }
    ```
    **注意：** 在微信小程序中```<image>```标签无法自适应高度，不设置固定高度则高度为0，微信小程序中图片高度自适应需要设置一个属性```mode="widthFix"```
    ``` html
    <image src="test.png" mode="widthFix" />
    ```