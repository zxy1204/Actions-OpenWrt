# Actions-OpenWrt

[![LICENSE](https://img.shields.io/github/license/mashape/apistatus.svg?style=flat-square&label=LICENSE)](https://github.com/P3TERX/Actions-OpenWrt/blob/master/LICENSE)
[![GitHub Stars](https://img.shields.io/github/stars/cuiqingwei/Actions-OpenWrt.svg?style=flat-square&label=Stars)](https://github.com/cuiqingwei/Actions-OpenWrt/stargazers)
[![GitHub Forks](https://img.shields.io/github/forks/cuiqingwei/Actions-OpenWrt.svg?style=flat-square&label=Forks)](https://github.com/cuiqingwei/Actions-OpenWrt/fork)

## Method
### 一键编译 

点击自己仓库页面上的 **Star** 开始编译，为了防止被滥用，这个功能默认未开启。

编辑 work­flow 文件（`.github/workflows/build-openwrt.yml`）取消注释下面两行，后续点击自己仓库上的 star 即可开始编译。

```none
#  watch:
#    types: [started]
```

> **TIPS:** 字段`started`并不是“开始了”的意思，而是“已经点击 Star”。
> **吐槽:** 官方并没有提供一个开始按钮，通过搜索找到过很多奇怪的一键触发方式，但都是通过 Web­hook 来实现的。通过点击 Star 来触发，这样就相当于把 Star 当成开始按钮。这个`started`有种一句双关的意思。

可以在 **Actions** 页面进行查看编译进度。

#### 云 menuconfig（SSH 连接到 Actions）

通过 tmate 连接到 GitHub Ac­tions 虚拟服务器环境，可直接进行 `make menuconfig` 操作生成编译配置，或者任意的客制化操作。也就是说，你不需要再自己搭建编译环境了。这可能改变之前所有使用 GitHub Ac­tions 的编译 Open­Wrt 方式。

- 编辑 workflow 文件（`.github/workflows/build-openwrt.yml`），修改`SSH_ACTIONS`环境变量的值为`true`：

```none
SSH_ACTIONS: true
```

- 在触发工作流程后，在 Actions 页面等待执行到`SSH connection to Actions`步骤，会出现下面的信息。

```none
To connect to this session copy-n-paste the following into a terminal or browser:

ssh Y26QeagDtsPXp2mT6me5cnMRd@nyc1.tmate.io

https://tmate.io/t/Y26QeagDtsPXp2mT6me5cnMRd
```

- 复制 SSH 连接命令粘贴到终端内执行，或者复制链接在浏览器中打开使用网页终端。
- `cd openwrt && make menuconfig`
- 完成后按快捷键`Ctrl+D`或执行`exit`命令退出，后续编译工作将自动进行。

> **TIPS:** 默认连接30分钟后会断开并终止编译工作流程，防止资源浪费与封号风险。如果你想解除这个限制，可以根据提示操作，后果自负。

#### 添加软件包

在 DIY 脚本中加入对指定软件包的远程仓库的克隆指令。就像下面这样：

```none
git clone https://github.com/P3TERX/xxx package/xxx
```

这样做的好处是每一次编译都会拉取最新的源码。

> **TIPS:** 生成`.config`文件时记得选中相应的软件。如果添加的软件包与 Open­Wrt 中已有的软件包同名的情况，则需要把源码中的同名软件包删除，否则会优先编译 Open­Wrt 中的软件包。这同样可以利用到的 DIY 脚本。

最后不要忘了添加更新 feeds 的命令

```none
./scripts/feeds update -a
./scripts/feeds install -a
```

#### Custom files（自定义文件）

俗称 “files大法”，在仓库根目录下新建 `files` 目录，把文件放入即可。

### 定时编译

编辑 work­flow 文件（`.github/workflows/build-openwrt.yml`）取消注释下面两行。

```none
#  schedule:
#    - cron: 0 8 * * 5
```

例子是北京时间每周五下午 4 点钟开始编译。如需自定义则按照 cron 格式修改即可，Ac­tions 虚拟环境中的时区是 UTC ，注意按照自己所在地时区进行转换。

### DIY 脚本

仓库内有一个 `diy.sh` 文件，你可以把对源码修改的指令写到这个文件中，比如修改默认 IP、主机名、主题、添加 / 删除软件包等操作。但不仅限于这些操作，发挥你强大的想象力，可做出更强大的功能。

> **TIPS:** 脚本工作目录在源码目录，内附一个修改默认 IP 的例子供参考使用。

### 自定义环境变量与功能

<details open="" style="box-sizing: border-box; display: block; color: rgba(0, 0, 0, 0.86); font-family: &quot;Droid Serif&quot;, -apple-system, system-ui, sans-serif; font-size: 18px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: justify; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; background-color: rgb(255, 255, 255); text-decoration-style: initial; text-decoration-color: initial;"><div id="details-content"><p style="box-sizing: border-box; margin: 0.92em 0px;">打开 work­flow 文件（<code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">.github/workflows/build-openwrt.yml</code>），你会看到有如下一些环境变量，可按照自己的需求对这些变量进行定义。</p><pre class=" language-none line-numbers" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 0.96em; position: relative; width: 820px; overflow: hidden; line-height: 1.5; border: 1px solid rgba(0, 0, 0, 0.05); border-radius: 5px; background: rgb(241, 243, 243); text-align: left; white-space: pre; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; counter-reset: linenumber 0;"><code class=" language-none" style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: 0px 0px !important; padding: 10px 10px 10px calc(3em + 10px); border-radius: 3px; text-align: left; white-space: inherit; word-spacing: normal; word-break: normal; overflow-wrap: normal; tab-size: 4; hyphens: none; display: block; overflow: auto;">env:
  REPO_URL: https://github.com/coolsnowwolf/lede
  REPO_BRANCH: master
  CONFIG_FILE: .config
  DIY_SH: diy.sh
  FREE_UP_DISK: false
  SSH_ACTIONS: false
  UPLOAD_BIN_DIR: false
  UPLOAD_FIRMWARE: true
  TZ: Asia/Shanghai</code><span aria-hidden="true" class="line-numbers-rows" style="box-sizing: border-box; position: absolute; pointer-events: none; top: 10px; font-size: 17.28px; left: 0px; width: 3em; letter-spacing: -1px; border-right: 1px solid rgba(153, 153, 153, 0.2); user-select: none; background: rgb(241, 243, 243);"><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span><span style="box-sizing: border-box; pointer-events: none; display: block; counter-increment: linenumber 1;"></span></span></pre><blockquote style="box-sizing: border-box; margin: 0.92em 0px; border-width: 4px; border-left-style: solid; border-left-color: rgb(218, 218, 218); padding-left: 12px; color: rgba(0, 0, 0, 0.6);"><strong style="box-sizing: border-box; font-weight: 700; color: rgba(0, 0, 0, 0.98);">TIPS:</strong><span>&nbsp;</span>修改时需要注意<code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">:</code>(冒号)后面有空格。</blockquote><table style="box-sizing: border-box; width: 820px; max-width: 100%; border-collapse: collapse; border-spacing: 0px; margin-bottom: 1.5em; font-size: 0.96em;"><thead style="box-sizing: border-box; background-color: rgb(239, 239, 238);"><tr style="box-sizing: border-box;"><th style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218);">环境变量</th><th style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218);">功能</th></tr></thead><tbody style="box-sizing: border-box;"><tr style="box-sizing: border-box;"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">REPO_URL</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">源码仓库地址</td></tr><tr style="box-sizing: border-box; background-color: rgb(239, 239, 238);"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">REPO_BRANCH</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">源码分支</td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">CONFIG_FILE</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">.config</code>文件名</td></tr><tr style="box-sizing: border-box; background-color: rgb(239, 239, 238);"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">DIY_SH</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">DIY 脚本文件名</td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">FREE_UP_DISK</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">释放磁盘空间。编译磁盘空间不足报错时使用。默认<code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">false</code></td></tr><tr style="box-sizing: border-box; background-color: rgb(239, 239, 238);"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">SSH_ACTIONS</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">SSH 连接 Actions 功能。默认<code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">false</code></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">UPLOAD_BIN_DIR</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">上传 bin 目录。即包含所有 ipk 文件和固件的目录。默认<code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">false</code></td></tr><tr style="box-sizing: border-box; background-color: rgb(239, 239, 238);"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">UPLOAD_FIRMWARE</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">上传固件目录。默认<code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">true</code></td></tr><tr style="box-sizing: border-box;"><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;"><code style="box-sizing: border-box; font-family: Menlo, Monaco, Consolas, &quot;Courier New&quot;, monospace; font-size: 1em; background: rgb(235, 237, 237); padding: 0px 3px; border-radius: 3px;">TZ</code></td><td style="box-sizing: border-box; text-align: left; padding: 4px 8px 4px 10px; border: 1px solid rgb(218, 218, 218); vertical-align: top;">时区设置</td></tr></tbody></table><p style="box-sizing: border-box; margin: 0.92em 0px;"></p></div></details>

## Usage

- Sign up for [GitHub Actions](https://github.com/features/actions/signup)
- Fork [this GitHub repository](https://github.com/P3TERX/Actions-OpenWrt)
- Generate `.config` files using [Lean's OpenWrt](https://github.com/coolsnowwolf/lede) source code.
- Push `.config` file to the GitHub repository, and the build starts automatically.Progress can be viewed on the Actions page.
- When the build is complete, click the `Artifacts` button in the upper right corner of the Actions page to download the binaries.

## Acknowledgments

- [Microsoft](https://www.microsoft.com)
- [Microsoft Azure](https://azure.microsoft.com)
- [GitHub](https://github.com)
- [GitHub Actions](https://github.com/features/actions)
- [tmate](https://github.com/tmate-io/tmate)
- [mxschmitt/action-tmate](https://github.com/mxschmitt/action-tmate)
- [csexton/debugger-action](https://github.com/csexton/debugger-action)
- [Cisco](https://www.cisco.com/)
- [OpenWrt](https://github.com/openwrt/openwrt)
- [Lean's OpenWrt](https://github.com/coolsnowwolf/lede)

## License

[MIT](https://github.com/cuiqingwei/Actions-OpenWrt/blob/master/LICENSE) © GaryCUI