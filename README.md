# 速查表

## Node 和 Npm

### 参数传递

package.json

```json
{
  "name": "nev",
  "version": "1.0.0",
  "description": "",
  "main": "app.js",
  "scripts": {
    "test": "node app.js",
    "test-argv": "node app.js --arg=123",
    "test-npm-config": "npm run test --arg=123",
    "test-npm-package-config": "npm run test --arg=123",
    "set-npm-package-config": "npm config set nev:arg 123"
  },
  "author": "",
  "license": "ISC"
}
```

app.js

```javascript
console.log(process.argv) // 一个数组 ['node 的 路径', 'app.js 的 路径', ......]
console.log(process.env.npm_config_arg)
console.log(process.env.npm_package_config_arg)
// npm_package_config_arg 的值取决于 .npmrc 文件中的内容。
// 使用 npm config list 查看。

```
