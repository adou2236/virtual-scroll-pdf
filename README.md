# pdf阅读器，支持虚拟滚动动态加载，在页数较多的情况下减少页面卡顿
|  配置项   |说明| 默认值  |
|  ----  | ---- | ----  |
| fileSrc|文件地址| 无 |
| scrollLoad|是否开启滚动加载| false |
| loadNum |默认加载页数|10，该项只在scrollLoad为true时生效 |

| 事件 |说明|返回值 |
| ---- |----|---- |
| onLoad |文件加载完毕| 该文件的总页码|


## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
