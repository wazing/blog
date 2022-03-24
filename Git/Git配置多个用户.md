### 全局配置

```xml
# 查看全局配置
git config --global --list
# 清除
git config --global --unset user.name
git config --global --unset user.email
# 生成
git config --global user.name "wazing"
git config --global user.name "dev.wazing@gamil.com"
```

### 生成密钥

在`~/.ssh/`目录下生成密钥，注意尾部文件名称(id_rsa_github、id_rsa_gitee)。

```xml
# github
ssh-keygen -t rsa -C 'dev.wazing@gmail.com' -f ~/.ssh/id_rsa_github
# gitee
ssh-keygen -t rsa -C 'dev.wazing@gmail.com' -f ~/.ssh/id_rsa_gitee
```

### 将`~/.ssh/`目录下的密钥保存至云端平台(Github/Gitee/...)

首先将`~/.ssh/id_rsa_github.pub`文件打开，`全选` `复制`，然后进行下面操作：

`Gihub`平台流程：`Sign In` -> 右上角点头像 -> `Settings` -> 左侧`SSH and GPG keys` -> `New SHH key` -> 粘贴后保存即可。

其他平台类似。

##### 在`~/.ssh/`目录下创建`config`文件

`~/.ssh/`目录下如果没有`config`文件，那就创建一个(和密钥同目录)，然后输入以下内容：

```xml
Host github
    HostName github.com
    User wazing # 用户名
	Port 22 # 默认22端口
    IdentityFile ~/.ssh/id_rsa_github

Host gitee
    HostName gitee.com
    User wazing
	Port 22
    IdentityFile ~/.ssh/id_rsa_gitee
```

### 测试是否成功

```xml
ssh -T git@github
ssh -T git@gitee
```

成功后会有以下提示:

> Hi wazing! You've successfully authenticated, but GitHub does not provide shell access.

后面的`does not provide shell access`可能是同时配置多用户原因导致(不明)，但不影响正常的操作。

 ### 为项目配置 Name&Email

上面的步骤成功后，就能`clone`项目了。在`pull/push`前，需要进行以下操作(配置一次就可以了)：

```xml
git config --local user.name "wazing"
git config --local user.email "dev.wazing@gmail.com"
```

很明显，`--global `被替换成`--local`，配置完成后就能正常玩耍了~

### 常见问题

有时候提示没权限啥的，需要把密钥添加到缓存中(可能是叫缓存)

```xml
ssh-agent bash
# 添加到本地缓存
ssh-add ~/.ssh/id_rsa_github
ssh-add ~/.ssh/id_rsa_gitee
ssh-add ~/.ssh/id_rsa_gitlab
# 查看本地缓存
ssh-add -l
```

