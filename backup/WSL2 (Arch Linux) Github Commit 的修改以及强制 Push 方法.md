# 现象
个人有些私人的小项目，断断续续写了一些内容，也都上传了。由于各种事情影响的缘故，每次上传 repo 的 `commit` 内容标题的形式都没有能统一。
我打算修改这些 `commit` 内容的标题，标准化它们的格式。

# 思路
由于是修改多次的 `push` 信息，于是我打算使用交互式变基 `rebase` 命令来修改。

# 具体步骤
## 1. 启动交互式变基
在 `bash` 中输入以下内容，其中 `n` 为最近的 n 次提交。
```bash
git rebase -i HEAD~n
```
注意，此时可能会触发报错：
```bash
error: cannot rebase: You have unstaged changes.
error: Please commit or stash them.
```
此时我一般会选择隐藏：
```bash
git stash
```
隐藏后再次运行 `git rebase -i HEAD~n` ，可能依然会出现以下报错：
```bash
hint: Waiting for your editor to close the file... error: cannot run vi: No such file or directory
error: unable to start editor 'vi'
```
这是因为 Git 的默认编辑器是 `vi` ，如果你安装了 Vim、 Neovim 或者其他编辑器却没有安装他们的“原型” vi，那的确会报错。
此时指定一个已经安装的编辑器即可，这里以 Neovim 为例：
```bash
git config --global core.editor "nvim"
```
修改后再尝试 `rebase` 会打开一个文本编辑器，其中列出了这几次提交的信息，内容大致如下：
```text
pick abc1234 提交信息1
pick def5678 提交信息2
pick ghi9012 提交信息3
... ...
```
## 2. 标记要修改的提交
在编辑器中，找到你想修改信息的提交行，把行首的 `pick` 改为 `reword`。
以上面样例文本为例，例如我想修改第二条提交信息。于是我做出如下修改：
```text
pick abc1234 提交信息1
reword def5678 提交信息2
pick ghi9012 提交信息3
... ...
```
`:wq` 保存并退出，Git 会为每个标记了 `reword` 的提交依次打开编辑器，所以可以一次修改多个 `pick` 为 `reword`。
## 3. 在编辑器中依次修改内容
依次打开的新文件内容大致如下：
```text
提交信息2
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# Date:      Thu Jan 01 12:00:00 2026 +0800
#
# interactive rebase in progress; onto 123a456
# Last command done (1 command done):
#    reword edf5678 # 提交信息2
# Next commands to do (6 remaining commands):
#    pick ghi9012 # 提交信息3
# You are currently editing a commit while rebasing branch 'main' on '123a456'.
#
# Changes to be committed:
#       modified:   file.py
#       new file:   README.md
#
```
修改开头的 `提交信息2` 即可，其他注释中均有说明。修改完成后 `:wq` 保存并退出。
如果有多个 `reword`，保存退出后会自动跳转至下一个 reword 内容。
## 4. 强制推动到远程
变基操作重写了 `push` 历史，需要强制推送。
```bash
git push --force-with-lease
```
# 其他注意事项
## 1. 注意编辑器嵌套
不要在 Neovim 的 `--TERMINAL--` 模式下输入命令，编辑器嵌套会导致编辑中的文件无法保存和退出，Neovim 命令行中会出现以下报错：
```nvim
E382: Cannot write, 'buftype' option is set
```
## 2. 规范化 Commit 信息结构
<table>
  <tr>
    <td>
      <strong>语言</strong><br>
      <strong>主题行动词</strong><br>
      <strong>主题行长度</strong><br>
      <strong>主题行大小写</strong><br>
      <strong>主题行标点</strong><br>
      <strong>正文</strong><br>
      <strong>关联 Issue</strong><br>
    </td>
    <td>
      统一使用英语<br>
      现在时祈使语气（如 Add, Fix, Update, Remove, Refactor, Rename, Improve 等）<br>
      不超过 50 个字符（便于在 GitHub/GitLab 列表中完整显示）<br>
      首字母大写，其余小写（专有名词除外）<br>
      末尾不加句号<br>
      如果需要进行详细解释，用空行分隔。说明 “是什么” 和 “为什么”，而不是重复代码。<br>
      若有关联，在脚注写 Closes #123 或 Refs #456
    </td>
  </tr>
</table>