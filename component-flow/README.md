# vue学习笔记 - flow

# 安装

安装flow

```

npm install -g flow-bin
flow init

```

# 配置

.flowconfig

```
[ignore]
.*/node_modules/.*
.*/lib/.*

[include]
.*/src/.*

[libs]

[lints]

[options]

[strict]

```

# 测试

编写代码
```JavaScript

// @flow
function square(n: number): number {
  return n * n;
}

square("2"); // Error!
```

进行测试，直接运行 `flow` 测试。

```
Error: src/demo1.js:8
  8: square("2"); // Error!
            ^^^ string. This type is incompatible with the expected param type of
  2: function square(n: number): number {
                        ^^^^^^ number
```

# 编译

最后发布的文件需要去掉参数限制的信息。

```
npm install --save-dev babel-cli babel-preset-flow
```

配置 `.babelrc`。

```
{
  "presets": ["flow"]
}
```

`package.json` 增加编译命令。

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel src/ -d lib/"
}
```

运行 `npm run build` ，在lib下生成了对于的文件。


```

> flow@1.0.0 build D:\OutPut\VUE\VueStudyNote\component-flow
> babel src/ -d lib/

src\demo1.js -> lib\demo1.js
```

生成对应的文件：

```JavaScript
function square(n) {
  return n * n;
}

square("2"); 
```

# 检查通过才编译

使用webpack的 `prebuild` 钩子来实现。

>任何脚本 `script` （包括自定义）都可以增加 `pre` ， `post` 2个钩子。

```
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "babel src/ -d lib/",
    "prebuild": "flow check"
  },
```

再次运行 `npm run build` 编译代码。flow检查会报错，*不会* 执行编译操作。

```

> flow@1.0.0 prebuild D:\OutPut\VUE\VueStudyNote\component-flow
> flow check

Error: src/demo1.js:7
  7: square("2"); // Error!
            ^^^ string. This type is incompatible with the expected param type of
  2: function square(n: number): number {
                        ^^^^^^ number


Found 1 error
npm ERR! code ELIFECYCLE
npm ERR! errno 2
npm ERR! flow@1.0.0 prebuild: `flow check`
npm ERR! Exit status 2
npm ERR!
npm ERR! Failed at the flow@1.0.0 prebuild script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     C:\Users\Administrator\AppData\Roaming\npm-cache\_logs\2017-12-29T06_25_16_457Z-debug.log
```


修正 `scr\demo1.js` 里面的错误。

```JavaScript
// @flow
function square(n: number): number {
  return n * n;
}

square(2); 
```


再次运行 `npm run build`

```

> flow@1.0.0 prebuild D:\OutPut\VUE\VueStudyNote\component-flow
> flow check

Found 0 errors

> flow@1.0.0 build D:\OutPut\VUE\VueStudyNote\component-flow
> babel src/ -d lib/

src\demo1.js -> lib\demo1.js
```


检查成功，并编译新代码。