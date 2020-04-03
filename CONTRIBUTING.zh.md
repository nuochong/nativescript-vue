# 贡献给nativescript-vue

如果您想为这个项目做贡献，那就太好了！ 本指南应帮助您入门。

# 拉取请求准则

- 可以向`master`分支提交PR
- 每个PR可以有多个提交（合并期间将被压缩）
- 请描述每个PR中的更改，以使其更易于查看。 （请没有空的PR描述）

随着项目的成熟，我们将再次重申这些准则。

# 贡献文档

请参阅[nativescript-vue / nativescript-vue.org信息库]（https://github.com/nativescript-vue/nativescript-vue.org）

# 开发设置

您将需要安装Node.js以及NativeScript。

请确保您使用的是Nativescript 4.x

克隆仓库后，运行：

`npm install`

（在“ nativescript-vue”文件夹中）

# 常用的NPM脚本

```bash
$ # watch and auto re-build dist/index.js
$ npm run dev
```

# 使用示例应用程序进行测试

要测试存储库中提供的示例应用程序，您需要在根目录中执行“ npm run dev”。这将监视更改，并重建nativescript-vue，它将在开发模式下生成到samples / app目录中（这样做是为了减少链接本地程序包所需的步骤，这对于npm的最新版本有很多问题）。

接下来，打开一个新的终端窗口并运行`npm run samples`。这将显示所有可用示例应用程序的列表，您可以使用箭头键从中选择。按Enter / Return键将选择该示例，并提示您选择要在其上运行示例的平台。选择平台后，该应用程序应在您的模拟器上启动，并且输出将在您的终端中。

如果要在激活HMR的情况下测试示例应用，请运行`npm run samples---hmr`。实际上，我们可以将任何参数传递给`tns debug platform`命令，将其放在`--`分隔符之后。

# 项目结构

- `build`: 用于管理和构建项目的定制工具的目录
- `dist`: 捆绑代码的目录
- `packages`: 紧密相关的软件包的目录
- `platform/nativescript`: 包含特定于“ nativescript”的平台代码
  - `compiler`: 这就是模板编译逻辑的去向（vue模板->渲染功能）
  - `renderer`: 将渲染vdom渲染为{N}中实际元素的渲染器
  - `runtime`: {N}个特定的Vue后端
  - `util`: 实用工具
- `samples`: {N}个示例应用程序进行测试

# 故障排除

#### 1. 沙哑的“ binding.open”错误

[当前] devDependencies husky 0.15 beta中存在一个错误，如果缺少.git / hooks，它将中止npm install的运行。

https://github.com/typicode/husky/issues/195

```
> husky@0.15.0-rc.3 postinstall /.../nativescript-vue/node_modules/husky
> node lib/installer/bin install
husky > setting up git hooks
fs.js:663
  return binding.open(pathModule.toNamespacedPath(path),
                 ^
Error: ENOENT: no such file or directory, open '/.../nativescript-vue/.git/hooks/applypatch-msg'
```

Mac:
```
nativescript-vue$ mkdir -p .git/hooks/
```

#### 2. 确保将JAVA_HOME设置为您的JAVA 8 JDK！

如果未正确设置JAVA_HOME，则`npm run samples'将在提示您提供示例和平台后退出并返回命令提示符。

Mac:
```
export JAVA_HOME=`/usr/libexec/java_home -v1.8`
```

#### 3. 由于出现“ ENFILE：文件表溢出...”错误，因此无法在MacOS上部署到Android。

如果看到这样的错误：
```
Transferring project files...
Multiple errors were thrown:
ENFILE: file table overflow, open '/Users/tiagoalves/Projects/ns-vue/(...)/file-name-resolver/package.json'
```
这意味着您必须增加文件限制 (查阅: [http://stackoverflow.com/a/27982223](http://stackoverflow.com/a/27982223)). 您可以执行以下操作：
```
echo kern.maxfiles=65536 | sudo tee -a /etc/sysctl.conf
echo kern.maxfilesperproc=65536 | sudo tee -a /etc/sysctl.conf
sudo sysctl -w kern.maxfiles=65536
sudo sysctl -w kern.maxfilesperproc=65536
ulimit -n 65536 65536
```

#### 4. 使用 XCode 8

检查是否安装了xcodeproj
- `sudo gem install xcodeproj -v 1.4.1`

您可能需要启用系统ruby（macos）
- `rvm use system` // now using system ruby
- repeat `sudo gem install xcodeproj -v 1.4.1`
- tns run ios

您可能会得到一个错误：
`No profiles for 'org.nativescript.MyApp' were found: Xcode couldn't find a provisioning profile matching 'org.nativescript.MyApp'`

- 打开项目
- 导航到 app/iOS/build.xcconfig
- 引入 `PROVISIONING_PROFILE = testapp;`

修改 app.js 为

```javascript
const Vue = require('nativescript-vue');

new Vue({

  data: {
	message: "Hello Vue!"
  },

  template: `
    <Page>
      <StackLayout>
        <Label v-model="message" />
      </StackLayout>
    </Page>
  `,
}).$start()
```
