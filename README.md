# virtual-list

**虚拟列表（vue3 + vite + composition Api）**

在长列表中为了渲染列表的数据，会生成对应的 dom 节点，
当数据很多时 dom 节点也就会非常多，也就导致页面性能变差的情况的情况。

虚拟列表可以很好地解决这类问题。
它的逻辑是这样的，在一个长列表中，只渲染显示区域和显示区域数据的前后的一部分数据。
在滚动时候通过计算属性来截取上述的数据，从而减少 dom 节点的数量。

原理图：
![原理图][1]

效果图：
![原理图][2]


## 安装依赖

```
npm install
```

## 启动服务

```
npm run dev
```

## 打包

```
// "node": ">= 10.16.0"
npm run build
```


[1]: ./img/difference-in-scrolling.jpg
[2]: ./img/gif.gif
