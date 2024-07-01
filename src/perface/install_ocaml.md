# 安装 OCaml

如果您只需要跟随本书中的代码示例，您实际上不需要安装 OCaml！每页上的代码在浏览器中可执行，如前面在这本[前言](./about_this_book.md)中所述。

如果您想进一步，但还不准备花时间安装 OCaml 自己，我们提供了一个带有预安装的 [OCaml 虚拟机](../appendix/virtual_machine.md)，运行在 Linux 操作系统中。

但是如果你想自己做 OCaml 开发，你需要在机器上安装它。没有一种通用的“正确”方法来实现这个目标。下面提供的指南是 Cornell CS 3110 课程的一部分，这个课程有其自己的目标和需求，远远超出了只是 OCaml。但是，即使你不是这个课程的学生，你也可能会发现这些指南很有用。

我们将要安装：

- Unix 开发环境
- OPAM，OCaml 软件包管理器
- 一个 OPAM 开关带有 OCaml 编译器和一些包
- Visual Studio Code 编辑器，具有 OCaml 支持


安装过程将主要依赖于计算机的终端或文本界面。如果您不太熟悉它，您可能想学习一下[终端教程](https://ubuntu.com/tutorials/command-line-for-beginners)。

> 如果这是你第一次安装开发软件，那么值得注意的是，“关闭”不算：尝试继续执行错误通常只会导致更糟糕的错误，并且感到沮丧。这是因为我们正在安装一座软件塔，每层塔都基于前一个。如果你没有建立在坚实的基础上，这整个结构可能会塌陷。好消息是，如果你遇到错误，你可能不是唯一的人。快速搜索一下 Google 通常可以找到他人已经发现的解决方案。当然，也要对互联网上的随机陌生人的建议进行批判性思考。

让我们开始！

## Unix 开发环境

> 首先，升级您的操作系统。如果您计划进行任何主要的操作系统升级，请现在做。否则，当您实际升级时，您可能需要重复部分或全部安装过程。事先处理更好。

### Linux

如果您已经在运行 Linux 了，那么这个步骤就完成了。请继续到下面的[安装 OPAM](./install_ocaml.md#%E5%AE%89%E8%A3%85-opam)步骤。

### Mac

以下面层面，macOS 实际上已经是一个基于 Unix 的操作系统。但是，您需要一些开发工具和一个 Unix 包管理器。有两个选择：[Homebrew](https://brew.sh/) 和 [MacPorts](https://www.macports.org/install.php)。从本书籍和 CS 3110 的角度来说，这并不重要：

- 如果你已经习惯使用某个编辑器，随时继续使用它。确保在继续这些指令之前运行其更新命令。
- 否则，选择一个，然后按照其网站上的安装说明进行安装。Homebrew 的安装过程通常更简单、更快，这可能会使您朝这个方向发展。如果您选择 MacPorts，确保遵循其页面上的所有详细说明，包括 XCode 和 X11 服务器。不要同时安装 Homebrew 和 MacPorts；它们不能共存。如果您后来改变主意，请在安装另一个之前卸载其中的一个。

安装完成 Homebrew 或 MacPorts 更新后，请继续[安装 OPAM](./install_ocaml.md#%E5%AE%89%E8%A3%85-opam)，以下。

### Windows

Unix 开发在 Windows 中是由 WindowsSubsystem for Linux（WSL）所可能的。如果您拥有 Windows 的最新版本（20262，2020 年 11 月发布或更新），那么 WSL 安装起来很容易。如果您没有那么新的版本，可以尝试运行 Windows 更新以获取它。

> 如果您在安装 WSL 时遇到关于“虚拟机”的错误，您可能需要在机器的 BIOS 中启用虚拟化。该操作步骤取决于您的机器制造商。尝试搜索“启用虚拟化 [制造商] [型号]”，将制造商和型号替换为您的机器信息。这[一页](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/virtualization_administration_guide/sect-virtualization-troubleshooting-enabling_intel_vt_and_amd_v_virtualization_hardware_extensions_in_bios)可能也会有助于您。

使用最新版本的 Windows，并假设您从未安装过 WSL，以下是您需要做的事情：

- 打开 Windows PowerShell 以管理员身份。要做到这一点，请点击“开始”，输入“PowerShell”，它应该显示为最佳匹配。点击“以管理员身份运行”，然后单击“允许更改”以允许更改。
- 运行 `wsl --install` 。(如果您已经安装了 WSL，但之前没有安装 Ubuntu，那么请运行 `wsl --install -d Ubuntu` 。)当 Ubuntu 下载完成后，它可能会询问您是否需要重新启动。请重新启动。安装将在重新启动后自动恢复。
- 你将被提示创建一个 Unix 用户名和密码。你可以使用任何用户名和密码，你愿意。它与你的 Windows 用户名和密码无关（虽然你可以重复使用它们）。不要在用户名中输入空格。不要忘记你的密码。你将需要它未来。

> 不要继续执行这些指令，如果您没有被提示创建 Unix 用户名和密码。可能 Ubuntu 安装未完成正确。尝试从 Windows 开始菜单中卸载 Ubuntu 并重新安装它。

现在跳到下面的“Ubuntu 设置”段落。

没有最新版本的 Windows，您需要遵循 [Microsoft 的手动安装指南](https://docs.microsoft.com/en-us/windows/wsl/install-manual)。WSL2 是 OCaml 所 prefer 的选择（WSL2 还提供了性能和功能改进），因此，如果可以，请安装 WSL2。


**Ubuntu 安装**。这些剩余的指令假设您安装了 Ubuntu（22.04）作为 Linux 发行版。这是 WSL 中的默认发行版。在原则上，其他发行版也应该可以工作，但是可能需要从现在开始使用不同的命令。

打开 Ubuntu 应用程序。（可能已经打开，如果你刚刚安装了 WSL。）你将在 Bash 提示符下，这个界面看起来像这样：

```bash
user@machine:~$
```

> 如果该提示看起来像 `root@...#` ,那么出了问题。您在上一步中为 Ubuntu 创建了 Unix 用户名和密码吗？如果是这样，那么这个提示中的用户名应该是您之前选择的那个，而不是 `root` 。如果您的提示看起来像 `root@...#` ,不要继续执行这些指令。可能需要卸载 Ubuntu 并重新安装它。

在 Windows 终端的当前版本中，Ctrl+Shift+C 将复制到终端，而 Ctrl+Shift+V 将粘贴到终端。请注意，您需要将 Shift 作为该快捷键的一部分。在 older 版本的终端中，您可能需要在终端设置中找到一个选项以启用这些键盘快捷键。

更新 APT 包管理器，这是安装 Unix 软件包的工具，可以使用以下命令:

```bash
sudo apt update
```

你将被提示输入你选择的 Unix 密码。前缀 `sudo` 表示以管理员身份运行命令，也就是所谓的“超级用户”。换言之，这个命令应该以超级用户身份执行，因此是“sudo”。

> 运行命令时使用 `sudo` 可能危险，应该谨慎。不要养成将 `sudo` 加在命令前面的习惯，也不要随意尝试没有原因的操作。

现在运行这个命令来升级所有 APT 软件包：

```bash
sudo apt upgrade -y
```

然后安装一些有用的包，我们将需要它们：

**文件系统**。WSL 拥有自己的文件系统，这与 Windows 文件系统不同，虽然存在访问每个的方法。

- 当你启动 Ubuntu 并获得 $ 提示符时，你就在 WSL 文件系统中。你的家目录在那里被命名为 `~` ，这是对 `/home/your_ubuntu_user_name` 的内置别名。你可以运行 `explorer.exe` . （注意最后的点）以在 Windows 探索器中打开你的 Ubuntu 家目录。
- 从 Ubuntu 中，您可以访问您的 Windows 主目录在路径 `/mnt/c/Users/your_windows_user_name/`
- 从 Windows 资源管理器，您可以在左侧列表中找到 Linux 图标，访问 Ubuntu 主目录（靠近“这个计算机”和“网络”），然后导航到 Ubuntu → `home` → `your_ubuntu_user_name` 。或者，您可以直接输入 Windows 资源管理器路径栏： \\wsl$\Ubuntu\home\your_ubuntu_user_name 。

实践访问您的 Ubuntu 和 Windows 主目录现在，并确保您可以识别当前是哪一个。对于高级信息，请查看 Microsoft [关于 Windows 和 Linux 文件系统的指南](https://docs.microsoft.com/en-us/windows/wsl/filesystems)。

我们建议将 OCaml 开发工作存储在 Ubuntu 主目录中，而不是 Windows 主目录中。由此，微软也推荐这样做，如上链接中的指南所示。

## 安装 OPAM

Linux。[遵循您的发行版说明](https://opam.ocaml.org/doc/Install.html)。

Mac。如果您使用 Homebrew，运行以下命令：

```bash
brew install opam
```

如果您使用 MacPorts，运行以下命令：

```bash
sudo port install opam
```

Windows。从 Ubuntu 运行以下命令：

```bash
sudo apt install opam
```

## 初始化 OPAM

> 不要在任何 `opam` 命令前面加上 `sudo` 。那样将会破坏您的 OCaml 安装。

Linux、Mac 和 WSL2。运行：

```bash
opam init --bare -a -y
```

不要担心如果您收到一条关于确保 `.profile` 在 `.bashrc` 中“充分来源”的提示。您不需要对此进行任何操作。

WSL1。希望您正在运行 WSL2，而不是 WSL1。但是在 WSL1 中，请运行：

```bash
opam init --bare -a -y --disable-sandboxing
```

因为 OPAM 和 WSL1 存在问题，需要[禁用沙盒](https://github.com/ocaml/opam-repository/issues/12050)。

## 创建 OPAM 切换

A switch 是 OCaml 的一个命名安装，它具有特定的编译器版本和一组包。您可以拥有多个开关，并且可以在它们之间切换——因此得名。为本学期的 CS 3110 创建一个开关，运行以下命令：

```bash
opam switch create cs3110-2024sp ocaml-base-compiler.5.1.1
```

> 如果那个命令失败说 5.1.1 编译器找不到，你可能以前安装过 OPAM 现在需要更新它。使用 `opam update` 进行更新。

你可能会被提示运行下一个命令。无论你是否执行它，因为我们即将做的下一步操作（即，登出）都无关紧要。

现在我们需要确保您的 OCaml 环境正确配置。退出操作系统（或直接重启）。然后重新打开终端，运行以下命令：

```bash
opam switch list
```

你应该获得输出类似这样

```bash
#  switch         compiler                    description
→  cs3110-2024sp  ocaml-base-compiler.5.1.1   cs3110-2024sp
```

如果您之前有过 OCaml 开发经验，那可能会出现其他行。下面是需要检查的内容:

- 你不能收到警告“环境不与当前开关同步。你应该运行 `eval $(opam env)` ”。如果以下两个问题也出现，你需要先解决这个问题。
- 必须在 `cs3110-2024sp` 开关旁边的第一列中有一个正确的箭头。
- 那个开关必须具有正确的名称和正确的编译器版本，5.1.1。

> 如果你收到关于 `opam env` 的警告，事情出了错。你的 shell 可能没有运行 `opam init` 所安装的 OPAM 配置命令。你可以尝试 `opam init --reinit` 来看是否能解决问题。另外，请确保你真的退出了操作系统（或重新启动）。

继续安装我们需要的 OPAM 包:

```bash
opam install -y utop odoc ounit2 qcheck bisect_ppx menhir ocaml-lsp-server ocamlformat
```

确保复制整个行。您将获得关于编辑器配置的输出，除非您打算使用 Emacs 或 Vim 进行 OCaml 开发，可以安全地忽略该输出。我们将在这些说明中使用 VS Code 作为编辑器，所以让我们忽略它。

你现在应该能够启动 utop，OCaml 通用终端。

```bash
utop
```

> 您应该看到一条消息“欢迎使用 utop 版本…（使用 OCaml 版本 5.1.1）！”如果 OCaml 版本不正确，那么你可能遇到环境问题。见上面的 `opam env` 命令提示。

输入 3110 后跟两个分号。按回车键。#是 utop 提示符；你自己不需要输入它。

```ocaml
# 3110;;
- : int = 3110
```

停止欣赏 3110 多么美丽。然后退出 utop。注意这次你必须在 quit 指令前添加额外的#符号。

```ocaml
# #quit;;
```

退出的更快方法是键入 Control+D。

## 双重检查 OCaml

如果您遇到安装问题，请遵循以下检查指南。其中一些重复了我们之前提供的提示，但我们将它们都放在一起，以帮助诊断任何问题。


首先，重新启动您的计算机。我们需要一个干净的白板来进行这个双重检查程序。

第二，运行 utop，并确保它工作正常。如果不工作，请查看以下常见问题：

- **你在正确的 Unix 提示符中吗？** 在 Mac 上，确保你在 Terminal 中的默认 Unix shell 中，不要手动运行 bash 或 zsh 或其他 shell。 在 Windows 上，确保你在 Ubuntu 应用程序中，而不是 PowerShell 或 Cmd。

- OPAM 环境是否已经设置？如果 utop 不是 recognized 命令，运行 `eval $(opam env)` 然后再次尝试运行 utop。如果 utop 现在工作正常，那么你的登录 shell 可能没有正确地激活 OPAM 环境；你不需要手动使用 `eval` 命令激活环境。可能是之前执行了 `opam init` 命令时出了问题。要解决这个问题，请按照以下“redo”指令进行操作。

- 您的开关列表了吗？运行 `opam switch list` ，确保有一个名为 `cs3110-2024sp` 的开关，它具有 5.1.1 编译器，并且是活动开关（以箭头旁边表示）。如果该开关存在但不是活动开关，请运行 `opam switch cs3110-2024sp` ,然后查看 utop 是否工作。如果该开关不存在，请遵循以下“redo”指令。

**红色重做指令**：使用 `rm -r ~/.opam` 删除 OPAM 目录。然后，返回上面的 OPAM 初始化步骤，并继续前进。特别注意在执行上述 OPAM 命令时不要遗漏任何部分，因为有时会出现错误。最后，重新启动并检查 utop 是否仍然可用。

> 你想让 utop 在重启后立即工作，不需要输入任何额外命令。

## Visual Studio Code

Visual Studio Code 是一款适合 OCaml 代码编辑器的不错选择。（如果您已经是 Emacs 或 Vim 的高级用户，那么它们也很不错。）


首先，下载并安装 [Visual Studio Code](https://code.visualstudio.com/)（以下简称 VS Code）。启动 VS Code。打开扩展面板，可以通过“视图”→“扩展”，或者单击左侧图标栏中的四个小方块图标，其中右上角的方块与其他三个方块分离。

在以下指令中，您将被要求“打开命令面板”。要做到这一点，请转到视图→命令面板。同时，也有操作系统特定的键盘快捷键，您将看到该快捷键的右侧，在该视图菜单中的“命令面板”字样下方。

第二步，如果您使用的是 Windows 或 Mac，请遵循以下一步骤：

- Windows-only：安装“WSL”扩展。
- Mac 仅：打开命令面板，输入“shell command”，找到“Shell Command: 安装‘code’命令到 PATH 中”命令。运行它。

第三，無論你使用的是哪种操作系统，请先关闭所有打开的终端窗口或直接注销或重启，以便让新的路径设置生效，然后你将能够从终端中启动 VS 代码。

第四，仅在 Windows 上，打开命令面板并运行“WSL：连接到 WSL”命令。（如果您使用 Mac，则跳过下一步。）第一次执行此操作时，它将安装一些额外的软件。完成后，您将看到 VS Code 窗口底部左侧的“WSL：Ubuntu”指示符。确保在继续下一步之前，您已经看到“WSL：Ubuntu”。如果您只看到一个 > < 图标，请点击它，然后从打开的命令面板中选择“连接到 WSL”。

五、再次打开 VS Code 扩展面板。搜索并安装来自 OCaml Labs 的“OCaml 平台”扩展。确保安装该扩展时名称与“OCaml 平台”完全匹配。

> OCaml 名为“OCaml”或“OCaml 和 ReasonIDE”的扩展不是正确的。它们都是老旧的，已经不再由开发者维护了。

## 双重检查 VS Code

让我们确保 VS 代码的 OCaml 支持正在工作。

- 重新启动你的计算机一次。（是的，这真的不应该必要。但现在它会检测许多潜在错误，因此这笔努力值得。）
- 开启一个新的 Unix 壳。Windows：记住这是 Ubuntu，而不是 PowerShell 或 Cmd。Mac：记住你不应该手动切换到不同的 shell，输入 `zsh` 或 `bash` 。
- 导航到您选择的目录，尽量是一个家目录的子目录。例如，您可能在家目录中创建一个用于您的 3110 工作的目录：

```sh
mkdir ~/3110
cd ~/3110
```

在那个目录中运行以打开 VS 代码：

```bash
code .
```

去到“文件”→“新建文件”。将文件以名称 `test.ml` 保存。VS Code 应该为其添加橙色骆驼图标。

- 类型以下 OCaml 代码，然后按 Return/Enter 键：

```ocaml
let x : int = 3110
```

当你输入代码时，VS Code 应该将语法着色、建议一些完成项，并在代码行上添加一个小注释。尝试将你输入的 `int` 改为 `string` 。下面 `3110` 应该出现一条波浪线。鼠标悬浮在它上面可以看到错误信息。同时，在“视图”→“问题”中也可以看到它。将整数加上双引号，使其变成字符串，这个问题就解决了。

如果您不观察这些行为，安装出了问题。下面是解决方法：
- 确保从您启动 VS Code 的同一个 Unix 提示符下，可以成功完成 OPAM 开关的双重检查指令：您可以运行 utop 吗？当前是否激活了正确的开关？如果不是，那么这是你需要解决的问题。然后返回到 VS Code 问题。如果现在已经修复了。
- 如果您在 WSL 中运行 VS Code，但它并没有添加上述描述的语法高亮和 squiggles，那么请检查 VS Code 窗口底部左侧是否显示“WSL”指示符。如果您不见到，请确保安装了“WSL”扩展，并且从 Ubuntu 启动 VS Code，而不是从 PowerShell 或 Windows GUI 启动。如果您已经看到，请确保安装了“OCaml 平台”扩展。


如果您仍然遇到问题，请尝试卸载 VS Code，重新启动，然后从头开始执行上述安装指令。请注意任何警告或错误信息。

> VS Code 中遇到任何问题时，不管您在线找到什么建议，请不要在 VS Code 设置文件中硬编码路径。这只是解决问题的 Band-Aid，而不是真正问题的根源。实际上，真正的问题可能是 OCaml 环境问题，您可以使用上述 OCaml 双重检查指令来调查。

## VS Code 设置

我们建议调整一些编辑器设置。打开用户设置 JSON 文件，方法如下：(一）转到视图→命令面板，(二）输入“用户设置 json”，(三）选择打开用户设置（JSON）。将这些设置粘贴到窗口中：

```json
{
    "editor.tabSize": 2,
    "editor.rulers": [ 80 ],
    "editor.formatOnSave": true
}
```

保存文件并关闭标签。

## 使用 VS 代码协作

VS Code 的 [Live Share](https://code.visualstudio.com/learn/collaboration/live-share) 扩展使得与其他人类一起编写代码变得轻松和有趣。您可以像在 Google 文档中协作一样编辑代码。它甚至支持共享语音通道，因此不需要单独启动 Zoom 会议。要安装和使用 Live Share，请遵循 [Microsoft 的教程](https://code.visualstudio.com/learn/collaboration/live-share)。

如果您是康奈尔学生，请使用您的 Microsoft 账户登录，而不是 GitHub。输入您的康奈尔 NetID 电子邮件，例如 `your_netid@cornell.edu` 。这将带您到康奈尔的登录网站。使用与您的 NetID 相关联的密码。
