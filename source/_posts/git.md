 
#  Git Commit message 编写指南

## 一、 作用：
**1.提供更多的历史信息，方便快速浏览。**
>`git log <last tag> HEAD --pretty=format:%s`

**2.可以过滤某些commit（比如文档改动），便于快速查找信息。**  
>`git log <last release> HEAD --grep feature`

**3.可以直接从commit生成Change log。**  
Change Log 是发布新版本时，用来说明与上一个版本差异的文档。

## 二、格式

Commit message 包括三个部分：Header，Body 和 Footer。可以用下方的格式表示它的结构。

`<type>(<scope>): <subject>// 空一行<body>// 空一行<footer>`
>其中，Header 是必需的，Body 和 Footer 可以省略(默认忽略)，一般我们在 git commit 提交时指定的 -m 参数，就相当于默认指定 Header。
<br/>

>不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。

### Header

Header部分只有一行，包括三个字段：type（必需）、scope（可选）和subject（必需）。

#### type
- feat：新功能（feature）
- fix：修补bug
- docs：文档（documentation）
- style：格式（不影响代码运行的变动）
- refactor：重构（不是新功能，也不是修改bug的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

如果 `type` 为 `feat` 和 `fix` ，则该 `commit` 将肯定出现在 `Change log` 之中。其他情况（docs、chore、style、refactor、test）由你决定，要不要放入 `Change log`，建议是不要。

#### scope
scope用于说明 `commit` 影响的范围，比如数据层、控制层、视图层等等，视仓库不同而不同。

#### subject
`subject` 是 `commit` 目的的简短描述，不超过50个字符。

- 以动词开头，使用第一人称现在时，比如 `change` ，而不是 `changed` 或 `changes`
- 第一个字母小写
- 结尾不加句号（.）

### Body
Body 部分是对本次 commit 的详细描述，可以分成多行。下面是一个范例。
>More detailed explanatory text, if necessary. Wrap it to about 72 characters or so. Further paragraphs come after blank lines.- Bullet points are okay, too- Use a hanging indent

有两个注意点。
- 使用第一人称现在时，比如使用 `change` 而不是 `changed` 或 `changes` 。
- 应该说明代码变动的动机，以及与以前行为的对比。

### Footer
Footer 部分只用于两种情况。

#### 1、不兼容变动
#### 2、关闭 Issue
如果当前 `commit` 针对某个 `issue` ，那么可以在 Footer 部分关闭这个 `issue` 。
>Closes #234

也可以一次关闭多个 `issue` 。
>Closes #123, #245, #992

### Revert
还有一种特殊情况，如果当前 `commit` 用于撤销以前的 `commit` ，则必须以 `revert:` 开头，后面跟着被撤销 `Commit` 的 `Header。`
>revert: feat(pencil): add 'graphiteWidth' option<br/><br/>
This reverts commit 667ecc1654a317a13331b17617d973392f415f02.

## 三、相关工具

### 1. Commitizen
[Commitizen](https://github.com/commitizen/cz-cli)是一个撰写合格 Commit message 的工具。

#### 安装
>`npm install -g commitizen`

#### 使用
(1) 在项目目录里，运行下面的命令，使其支持 Angular 的 Commit message 格式。
>`commitizen init cz-conventional-changelog --save --save-exact`

(2) 凡是用到`git commit`命令，一律改为使用`git cz`。这时，就会出现选项，用来生成符合格式的 Commit message。

### 2. validate-commit-msg
[validate-commit-msg](https://github.com/conventional-changelog-archived-repos/validate-commit-msg) 用于检查 Node 项目的 Commit message 是否符合格式。

它的安装是手动的。首先，拷贝下面这个[JS文件](https://github.com/kentcdodds/validate-commit-msg/blob/master/index.js)，放入你的代码库。文件名可以取为`validate-commit-msg.js`。  

接着，把这个脚本加入 Git 的 hook。下面是在`package.json`里面使用 [ghooks](https://www.npmjs.com/package/ghooks)，把这个脚本加为`commit-msg`时运行。
```js
"config": {
  "ghooks": {
    "commit-msg": "./validate-commit-msg.js"
  }
}
```
然后，每次`git commit`的时候，这个脚本就会自动检查 Commit message 是否合格。如果不合格，就会报错。

```
$ git add -A 
$ git commit -m "edit markdown" markdown" 
INVALID COMMIT MSG: does not match "<type>(<scope>): <subject>" ! was: edit markdown
```

### 3. conventional-changelog 
[conventional-changelog](https://github.com/conventional-changelog/conventional-changelog) 就是生成 Change log 的工具

如果你的所有 Commit 都符合 Angular 格式，那么发布新版本时， Change log 就可以用脚本自动生成。

生成的文档包括以下三个部分。
- New features
- Bug fixes
- Breaking changes.

#### 安装
>`npm install -g conventional-changelog`
#### 使用
>`conventional-changelog -p angular -i CHANGELOG.md -w`

上面命令不会覆盖以前的 Change log，只会在`CHANGELOG.md`的头部加上自从上次发布以来的变动。

如果你想生成所有发布的 Change log，要改为运行下面的命令。
>`conventional-changelog -p angular -i CHANGELOG.md -w -r 0`

为了方便使用，可以将其写入`package.json`的`scripts`字段。
```js
{
  "scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -w -r 0"
  }
}
```
以后，直接运行下面的命令即可。
>` npm run changelog`

### 相关文章：

[阮一峰：Commit message 和 Change log 编写指南](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)