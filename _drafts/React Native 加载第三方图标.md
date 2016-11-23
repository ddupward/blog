# React Native 加载fontawesome图标

## 使用场景

我们在编写html代码的时候，可以引用fontawesome等网站图标，而我们在使用React Native的时候，也想要使用到这些图标

## iOS端配置方法

当我们按照https://github.com/oblador/react-native-vector-icons中的配置方法来进行操作的时候，会发现结果并非满意，下面是我自己经过一番“艰难险阻”之后总结出来的最快捷的方法。

### 安装

```shell
npm install react-native-vector-icons --save
```

### 配置iOS工程

将`node_modules/react-native-vector-icons`目录中的`Fonts`文件夹拖拽到Xcode中，注意拖拽到Xcode中时，要将`Add to targets`和`Create groups`勾选。

紧接着编辑`Info.plist`，添加一个`Fonts provided by application`的Array值，并将需要的字体库加入到这个数组中。例如：FontAwesome.ttf

重新编译工程

### js代码中使用

先进行导入

```javascript
import Icon from 'react-native-vector-icons/FontAwesome';
```

代码中使用

```javascript
<Icon name="address-book" size={30}/>
```

这里注意，我们在官网中查看到的使用方法是

```javascript
<i class="fa fa-address-book" aria-hidden="true"></i>
```

我们需要使用名字fa-address-book的后半部分address-book在React Native在代码中使用。