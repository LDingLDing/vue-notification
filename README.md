### Vue.js notifications


<p align="center">
  <img src="https://media.giphy.com/media/xUn3C6FmbGmszMem64/giphy.gif">
</p>

与[euvl/vue-notification](https://github.com/euvl/vue-notification/)差别如下：
- 中文化文档
- 新增textAlign属性，用于配置文字对齐方案
- 新增black样式及对应demo补充，用于暗色配色的项目
- 调整默认样式：去除左侧加粗边，更好的适应center、right等位置；默认蓝色修改为较浅的#EAF4FE

### Install

暂无，自行download

### 使用方式

main.js:

```javascript
import Vue           from 'vue'
import Notifications from 'vue-notification'

/*
or for SSR: 
import Notifications from 'vue-notification/dist/ssr.js'
*/

Vue.use(Notifications)
```

App.vue:

```vue
<notifications group="foo" />
```

*.vue (way 1)

```javascript
this.$notify({
  group: 'foo',
  title: '重要提示',
  text: '这是一个通知弹窗!'
});
```

*.vue (way 2)

```javascript
import Vue from 'vue'

Vue.notify({
  group: 'foo',
  title: '重要提示',
  text: '这是一个通知弹窗!'
})
```

### Props

所有props都是`可选项`

| Name           | Type    | Default      | Description |
| ---            | ---     | ---          | ---         |
| group          | String  | ''           | 定义通知弹窗的Name，用于调用时指定，类似html中id。全局多种弹窗时需要。|
| width          | Number/String  | 300          | 通知弹窗的宽，如 `200`, `'200px'`, `'100%'`. 具体说明见[Width](#Width) |
| textAlign      | String  | ''       | 文字对齐方式。如`left`,`center`, `right`, 可以通过样式覆盖  |
| classes        | String/Array | 'vue-notification' | 添加在弹窗元素的样式。具体说明见[Style](#Style) |
| position       | String/Array | 'top right'  | 通知弹窗出现的位置. 具体说明见[Position](#Position) |
| animation-type | String  | 'css'      | 动画的类型， 提供`css动画` 和 基于`velocity`的js动画|
| animation-name | String  | 'vn-fade'  | 动画名，仅`animation-type`为`css`有效|
| animation      | Object  | {enter: {opacity: [1, 0]}, leave: {opacity: [0, 1]}}        | 动画配置，仅`animation-type`为`velocity`有效。配置说明见[Velocity Animation](#Velocity Animation) |
| duration       | Number  | 3000         | 通知弹窗展示时长(ms)，当传值为`negative`时，**永久**展示，点击关闭。**调试阶段适合用**|
| speed          | Number  | 300          | 弹窗出现/隐藏的时间(ms) |
| max            | Number  | Infinity     | 最多可累积展示的通知弹窗个数 |
| reverse        | Boolean | false        | 新出现通知弹窗叠加位置反向 |

**注**
- 自定义模板样式见[自定义模板 (slot)](#自定义模板 (slot))
- velocity动画的配置见[Velocity Animation](#Velocity Animation)


### API


最简单的调用

```javascript
this.$notify('text')
```

完整调用

```javascript
  this.$notify({

    // 调用的通知弹窗Name
    group: 'foo',

    // 通知类型，suceess warn error
    type: 'warn',

    // (optional)
    title: 'This is title',

    text: 'This is <b> content </b>',

    // (optional)
    duration: 10000,

    // (optional)
    speed: 1000

    // (optional)
    // 传入一些数据用于自定义扩展, 配合slot="body"使用
    data: {}
  })
```


### Groups

如果你计划将通知组件用于2个或更多完全不同类型的通知（例如，顶部中心的身份验证错误消息和右下角的通用应用程序通知） - 可以通过`group`指定特定的通知弹窗。

Example:

```vue
<notifications group="auth"/>
<notifications group="app"/>
```

```javascript
this.$notify({ group: 'auth', text: '无权限访问' })
```

### Position

`Position` 属相需要2个关键词分别制定垂直和水平方向上的位置。

Format: `"<vertical> <horizontal>"`.

- vertical 垂直位置: `top`, `bottom`
- horizontal 水平位置: `left`, `center`, `right`

默认时"top right".

Example:

```vue
<notifications position="top left"/>
```

### Style

自定义样式

Structure:

```scss
// SCSS:

.my-style {
  // Style of the notification itself

  .notification-title {
    // Style for title line
  }

  .notification-content {
    // Style for content
  }

  &.my-type {
    /*
    Style for specific type of notification, will be applied when you
    call notification with "type" parameter:
    this.$notify({ type: 'my-type', message: 'Foo' })
    */
  }
}
```
通过`classes`定义自己的样式

```vue
  <notifications classes="my-style"/>
```

**Default:**

```scss
.vue-notification {
  padding: 10px;
  margin: 0 5px 5px;

  font-size: 12px;

  color: #ffffff;
  background: #44A4FC;
  border-left: 5px solid #187FE7;

  &.warn {
    background: #ffb648;
    border-left-color: #f48a06;
  }

  &.error {
    background: #E54D42;
    border-left-color: #B82E24;
  }

  &.success {
    background: #68CD86;
    border-left-color: #42A85F;
  }
}
```

### 自定义模板 (slot)

slot="name"

Scope props:

| Name  | Type     | Description                         |
| ---   | ---      | ---                                 |
| item  | Object   | 配置项，同[Props](#Props)             |
| close | Function | 当通知关闭时的回调 |

Example:

```vue
<notifications group="custom-template"  
               position="bottom right">
   <template slot="body" slot-scope="props">
    <div>
        <a class="title">
          {{props.item.title}}
        </a>
        <a class="close" @click="props.close">
          <i class="fa fa-fw fa-close"></i>
        </a>
        <div v-html="props.item.text">
        </div>
    </div>
  </template>
</notifications>
```
<a name="velocity_animation"></a>


### Width

`width`支持使用字符串或者百分比。

Examples: '100%', '50px', '50', 50

### Velocity Animation

如果需要使用`Velocity`动画，需要另外安装一下`velocity-animate`包, 并且声明vue-notification对其的依赖。具体如下：
```
npm install velocity-animate --save-dev
```

main.js

```javascript
import Vue           from 'vue'
import Notifications from 'vue-notification'
import velocity      from 'velocity-animate'

Vue.use(Notifications, { velocity })
```

在这个例子里组件的prop需要设置为`animation-type="velocity"`
```vue
<notifications animation-type="velocity"/>
```

配置项参考如下，包含`enter`和`leave`两个属性，可以为对象或者函数方法

Example:
```javascript
animation = {
  enter (element) {
    
     let height = element.clientHeight

     return {
       // 高度从0px到$height
       height: [height, 0],

       // 透明度从0到0.5至1之间的随机数
       opacity: [Math.random() * 0.5 + 0.5, 0]
     }  
  },
  leave: {
    height: 0,
    opacity: 0
  }
}
```

```vue
<notifications
  animation-type="velocity"
  animation="animation"/>
```

**注** 完全配置见[velocityjs官网](http://velocityjs.org/)

### Cleaning

如果需要展示新的通知弹窗并 强制清除其他所有通知弹窗，可以使用 `clean: true`

```javascript
this.$notify({
  group: 'foo',
  clean: true
})
```
