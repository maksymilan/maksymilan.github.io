---
title: "Windows WSL 深度学习环境配置教程"
description: "本教程介绍如何在 Windows 系统上使用 WSL 安装和配置深度学习环境，包括 Python、PyTorch、Jupyter Notebook 等。"
publishDate: "26 Apr 2025"
updatedDate: "26 Apr 2025"
tags: ["Windows", "WSL", "深度学习", "Python", "PyTorch", "Jupyter"]
---

# Windows WSL 深度学习环境配置教程

**前提**：本教程面向在 Windows 系统上零基础搭建深度学习环境的初学者。我们将利用 Windows Subsystem for Linux (WSL) 在 Windows 中运行 Ubuntu 子系统。通过 WSL，可以在 Windows 上无缝使用 Linux 环境，方便安装 Python 与深度学习框架等工具。下面我们将一步步完成 WSL 的安装、Python (Miniconda) 环境配置、虚拟环境管理、PyTorch 安装（CPU/GPU）、Jupyter Notebook/Lab 配置、VS Code/PyCharm 整合，以及常用操作和注意事项。

## 1. 安装 WSL (Ubuntu) 并进行基础配置

Windows 10 (2004及以上) 或 Windows 11 用户可以直接使用**WSL 2**。WSL2 提供完整 Linux 内核、良好的性能和对GPU的支持，是推荐版本 。安装步骤如下：

- **启用 WSL 子系统**：在 **管理员模式** 下打开 PowerShell (开始菜单搜索“PowerShell”，右键选择“以管理员身份运行”)。然后输入以下命令启用 WSL 和虚拟机平台，并自动安装默认的 Ubuntu 发行版 ([安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install#:~:text=必须运行 Windows 10 版本 2004,及更高版本）或 Windows 11 才能使用以下命令。 如果使用的是更早的版本，请参阅手动安装页。)) ：

  ```powershell
  wsl --install
  ```

  运行上述命令后，系统会安装所需的组件和 Ubuntu 子系统 ([安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install#:~:text=现在，可以使用单个命令安装运行 WSL 所需的一切内容。 在管理员模式下打开 PowerShell,install 命令，然后重启计算机。))。第一次启动 Ubuntu 子系统时会弹出控制台进行初始化，你需要设置一个新的 Linux 用户名和密码（与 Windows 无关） 。请牢记这个用户名和密码。

  详细内容请查看[官方文档](https://learn.microsoft.com/zh-cn/windows/wsl/setup/environment#set-up-your-linux-username-and-password)

- **基础 Linux 配置**：Ubuntu 子系统安装后，每次打开都会进入你创建的普通用户主目录 (`/home/用户名`)。可以使用 Windows Terminal 来打开 WSL，这样能够在标签页中使用Ubuntu终端（需安装**Windows Terminal**应用，可从 Microsoft Store 获取） ([安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install#:~:text=建议遵循设置 WSL 开发环境的最佳做法这一指南，逐步了解以下操作：为已安装的 Linux 发行版设置用户名和密码、使用基本的,VS Code 远程服务器进行代码编辑和调试、文件存储的最佳做法、设置数据库、装载外部驱动器、设置 GPU 加速等。))。初次进入Ubuntu后，可以尝试运行 `uname -a` 或 `lsb_release -a` 来查看Linux版本，并运行基本命令如 `pwd`、`ls` 等体验Linux命令行。

> 💡 **提示**：WSL 中的 Linux 文件系统与 Windows 是隔离的，但可以互相访问文件。Windows 磁盘在 WSL 中挂载于 `/mnt/盘符` 路径，比如C盘对应 `/mnt/c `。你可以在 WSL 中通过 `cd /mnt/c/Users/<你的Windows用户名>`访问Windows用户目录。同样地，可以在 Windows 的资源管理器地址栏输入`\wsl$\Ubuntu` 来访问 Linux 文件系 ([跨文件系统工作 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/filesystems#:~:text=另外，还可以使用 ,命令。请确保在命令的末尾添加句点以打开当前目录。))】。建议在Linux环境下处理位于Linux文件系统中的项目文件，在Windows下处理Windows文件系统的文件，以获得最佳性 ([跨文件系统工作 | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/filesystems#:~:text=跨文件系统的文件存储和性能))】。

## 2. 安装 Miniconda (Python)

WSL 就绪后，我们需要安装 Python 环境。推荐使用 **Miniconda**（Anaconda 的轻量版）来方便地管理 Python 版本和包。Miniconda提供了 `conda` 包管理器，方便创建隔离的虚拟环境。安装步骤如下（[参考资料](https://www.cnblogs.com/distance66/p/17772974.html)）：

- **下载 Miniconda 安装脚本**：在 Ubuntu (WSL) 终端中执行下载命令。可以直接从官网获取最新版安装脚本：

  ```bash
  wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
  ```

  如果上述官方链接速度较慢，你也可以使用国内镜像，比如清华大学开源镜像站提供的 Miniconda 下载：

  ```bash
  wget https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/Miniconda3-latest-Linux-x86_64.sh
  ```

  下载完成后，运行 `ls` 查看当前目录下是否有 `Miniconda3-latest-Linux-x86_64.sh` 文件。

- **运行安装脚本**：执行以下命令启动 Miniconda安装脚本：

  ```bash
  bash Miniconda3-latest-Linux-x86_64.sh
  ```

  安装程序将提示一系列步骤：

  1. **查看许可**：按 **Enter** 浏览许可证文本（按空格键可以快速翻页）。
  2. **接受许可**：输入 `yes` 并回车表示接受许可证协。
  3. **选择安装路径**：默认会安装到你的 home 目录下的 `miniconda3` 文件夹，一般直接按 **Enter** 接受默认路径即可。
  4. **初始化**：安装完成后会询问是否运行 `conda init` 来初始化环境。

  安装过程结束后，Miniconda 会在你的 Linux 主目录下创建 `miniconda3` 文件夹。

- **激活 Conda 环境**：成功安装后，需要激活 conda 初始化配置。可以关闭当前终端重新打开，或者手动运行：

  ```bash
  source ~/.bashrc
  ```

  这一步会使终端 prompt 前出现 `(base)`，表示 base 环境已激活，并且可以使用 `conda` 命令。你可以运行 `conda --version` 检查 conda 是否安装成功。

> 💡 **Miniconda vs Anaconda**：Miniconda 只包含最小的 Python 和 conda，安装迅速且占用空间小；而 Anaconda 自带大量科学计算库。对于我们的目的，Miniconda足够且更轻量，后续可按需安装。

## 3. 创建和管理 Conda 虚拟环境

使用 conda 可以创建独立的虚拟环境来隔离不同项目的包依赖，避免冲突。这对深度学习环境非常重要。下面是常用的 conda 环境管理操作([参考资料](https://cloud.tencent.com/developer/article/2379096))：

- **创建新环境**：使用 `conda create` 命令新建环境。可以指定环境名称和要安装的 Python 版本，例如创建名为“dl_env”的环境，指定 Python 3.10：

  ```bash
  conda create -n dl_env python=3.10 -y
  ```

  执行该命令会下载并安装指定 Python 版本及基础包到一个独立目录。`-n dl_env` 指定环境名称，`-y` 自动确认安装。环境创建完成后，你可以通过以下命令查看已创建的环境：

  ```bash
  conda env list
  ```

  带 `*` 的行为当前激活的环境。

- **激活环境**：使用 `conda activate <环境名>` 切换到该环境。例如，激活刚刚创建的环境：

  ```bash
  conda activate dl_env
  ```

  终端前缀会从 `(base)` 变为 `(dl_env)`，表示环境切换 。此后安装的包、运行的程序都会在这个环境中进行。

- **退出环境**：使用 `conda deactivate` 返回上一层环境。

- **删除环境**：若不再需要某个环境，可以删除：

  ```bash
  conda env remove -n <环境名>
  ```

  请务必先退出该环境再删除该环境。

- **其他有用命令**：`conda list` 列出当前环境已安装的包，`conda install <pkg>` 安装新包，`conda update <pkg>` 更新包，`conda remove <pkg>` 卸载包等。

- conda 最为人诟病的点应该是包管理跟 pip 可能会产生一些冲突, conda 官方给出的最佳方案是：

  - 全程使用 `conda install` 来安装模块, 实在不行再用 `pip`
  - **使用 conda 创建完虚拟环境后, 一直用`pip`来管理模块** 
    - pip 应使用 `–upgrade-strategy only-if-needed` 参数运行, 以防止通过 conda 安装的软件包进行不必要的升级. 这是运行 pip 时的默认设置, 不应更改
    - 不要将 pip 与 `–user` 参数一起使用，避免所有用户安装

> 💡 **建议**：除非有特殊需求，否则**不要**在 `base` 基础环境中安装大量包。最好创建独立环境（如 `dl_env`）用于深度学习项目，保持 base 环境干净。例如，你可以为不同项目创建不同环境，如 `tf_env`（TensorFlow环境）和 `torch_env`（PyTorch环境）等。激活环境后再安装相应框架和依赖。

## 4. 安装 PyTorch（区分 CPU 和 GPU）

PyTorch 是深度学习常用框架。下面介绍在 WSL 的虚拟环境中安装 PyTorch 的步骤，包括纯 CPU 版本和支持 GPU 加速的版本。

**① 安装 CPU 版 PyTorch（请优先选择GPU版本）：**如果你的电脑没有独立显卡，或不需要GPU加速，可以安装 CPU 版本。确保激活了前面创建的环境（例如 `dl_env`），然后运行：

```bash
conda install pytorch torchvision torchaudio cpuonly -c pytorch
```

上述命令将通过 PyTorch 官方Anaconda通道安装适用于CPU的 PyTorch、TorchVision 和 Torchaudio。`cpuonly` 参数确保不包含CUDA支持。安装完成后，运行 `python -c "import torch; print(torch.__version__)"` 可验证 PyTorch 是否成功导入，以及输出版本号。

**② 安装 GPU 版 PyTorch：**

- **Windows端GPU驱动**：
  如果你在 **WSL 或 Linux 系统中**，可以在终端输入：

  ```bash
  nvidia-smi
  ```

  如果你已经安装驱动，会看到如下类似输出：

  ```bash
  +-----------------------------------------------------------------------------+
  | NVIDIA-SMI 552.12       Driver Version: 552.12       CUDA Version: 12.2    |
  +-----------------------------------------------------------------------------+
  | GPU Name        | Memory Usage | Temperature | ...
  | RTX 3060        | 0MiB / 6144MiB | ...
  ```

  如果你看到：

  ```bash
  Command 'nvidia-smi' not found
  ```

  说明你当前没有 GPU 驱动，或者没有 NVIDIA 显卡。

  **显卡驱动**（Graphics Driver）是一个让 **操作系统（Windows / Linux）能识别和控制你的显卡** 的程序。

  **安装显卡驱动**：进入[官网](https://www.nvidia.com/en-us/drivers/)，选择自己电脑的硬件，然后搜索，选择**NVIDIA Studio 驱动程序**下载，按照安装指导进行下载即可。

- 安装最新的 NVIDIA 显卡驱动（支持 WSL 的版本）。这一部分请参考官方文档[在 WSL 中启用 NVIDIA CUDA](https://learn.microsoft.com/zh-cn/windows/ai/directml/gpu-cuda-in-wsl)

  

- **WSL 中检测 GPU**：驱动安装并重启后，在 Ubuntu WSL 终端中运行 `nvidia-smi`，应当显示 NVIDIA GPU 列表以及驱动版本和CUDA版本。

- **安装支持CUDA的PyTorch**：

  从[pytorch的官方网站](https://pytorch.org/)上选择对应的版本，使用`pip`指令进行下载(下面这个示例是安装cuda版本为12.6的命令)

  注：最新的pytorch版本要求python版本>=3.9

  ```bash
  pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126
  ```

  安装完成后，进入 Python (`python`) 测试是否可以使用 GPU：

  ```python
  import torch
  print(torch.cuda.is_available())
  print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else "No CUDA")
  ```

  若输出 `True` 且设备名正确，说明 PyTorch GPU 版工作正常。

- 如果conda安装过程遇到网络问题，也可以

> ⚠️ **注意**：不要在 WSL 内自行安装官方的 CUDA Toolkit 或驱动（如通过`apt`安装），因为WSL2下CUDA的驱动部分由Windows主机提供，错误安装Linux驱动可能导致版本冲突。按照上述方法安装 PyTorch 时，会自动包含运行所需的CUDA库（如cuDNN等）。

**③ 验证 PyTorch**：安装完PyTorch后，可以在 Python 中执行以下代码测试一个简单的张量运算以确认一切正常：

```python
import torch
x = torch.rand(2, 3)
print("Tensor:", x)
if torch.cuda.is_available():
    print("CUDA is available. GPU:", torch.cuda.get_device_name(0))
```

这将输出一个随机张量，并在有GPU时打印GPU名称。如果没有GPU，`torch.cuda.is_available()` 会返回 False。

## 5. 安装和配置 Jupyter Notebook / JupyterLab

Jupyter Notebook/Lab 是数据科学常用的交互式开发环境。我们可以在 WSL 中运行 Jupyter 服务，然后从 Windows 浏览器访问。安装和配置步骤如下（也可以参考[这个文档](https://realpython.com/jupyter-notebook-introduction/)）：

- **安装 Jupyter**：在前面创建的 conda 环境中安装 Jupyter Notebook 或 JupyterLab。你可以选择安装传统的 Notebook：

  ```bash
  conda install jupyter notebook
  ```

- **启动 Jupyter 服务**：在 WSL 中激活你的环境，然后运行：

  ```bash
  jupyter notebook
  ```

  默认情况下，Jupyter会在后台启动一个本地服务器（通常监听 `localhost:8888` 端口），并尝试打开浏览器。由于 WSL 中没有图形界面，浏览器不会在 Linux 中打开，而是在终端中打印出一个包含访问令牌(token)的 URL，例如：

  ```
  http://localhost:8888/?token=abcdef123456...
  ```

  你需要**复制该 URL**，然后在 Windows 主机的浏览器中粘贴打开。通常即可看到 Jupyter 的界面。如果浏览器打不开，请确保复制了完整的URL（包括`token`参数），或参考下文取消token验证。

  - 通常在 Windows 11/10 最新版本中，`localhost:8888` 会正确映射到WSL中的Jupyter服务。如果无法访问，可以尝试使用 WSL 的 IP 地址替代（通过 `ifconfig` 或 `hostname -I` 获取 WSL的内部IP，然后浏览器访问 `http://<WSL_IP>:8888`）。不过新版WSL已改进了localhost转发，大多数情况下直接用localhost。

- **关闭 Jupyter 服务**：在终端中按 `Ctrl+C` 可停止 Jupyter 服务。若有未保存的Notebook编辑，会有提示确认。

- **取消 Token 验证 (免除每次复制token)**：默认安全机制下，每次启动Jupyter服务会生成随机访问令牌，需要在浏览器中输入或随URL附带。为了简化本地使用，我们可以设置**固定密码**或**免密登录**。由于本教程针对本机使用，下面介绍免密码的配置方法：

  1. **生成配置文件**：首次使用时运行命令：

     ```bash
     jupyter notebook --generate-config
     ```

     这将在 `~/.jupyter/` 目录下生成 `jupyter_notebook_config.py`（Jupyter Lab同样适用此配置）。

  2. **编辑配置文件**：使用Linux**文本编辑器**打开该文件，例如（这里也可以使用vim，或者直接用记事本打开文本文件进行修改）：

     ```bash
     nano ~/.jupyter/jupyter_notebook_config.py
     ```

     （nano是简易终端编辑器，WSL默认可能安装了，若无可先 `sudo apt install nano`）。找到以下两行，将前面的 `#` 去掉并做：

     ```python
     c.ServerApp.token = ''
     c.ServerApp.password = ''
     ```

     这两个设置将禁用启动 token 和密码验证，从而实现打开 Notebook 时不要求任何认证。为了让浏览器直接访问，我们还可以继续在文件中找到或添加如下设置：

     ```python
     c.ServerApp.allow_remote_access = True
     c.ServerApp.disable_check_xsrf = True
     c.ServerApp.ip = '0.0.0.0'
     c.ServerApp.port = 8888
     c.ServerApp.open_browser = False
     ```

     这些配置分别含义为：允许远程访问（非本机请求），禁用跨站验证请求（XSRF），监听所有IP（包括WSL外部），指定端口8888，以及不自动弹出。修改后保存文件并退出编辑器。

  3. **重新启动 Jupyter**：现在再次运行 `jupyter notebook`（或lab），终端将不再显示 token。此时直接在浏览器访问 `http://localhost:8888` 即可看到 Jupyter 主界面，无需输入密码token。

  > ⚠️ **注意安全**：禁用 token/password 意味着在本机无需验证即可访问 Notebook 服务。**切勿**在公共网络环境下启用此配置，否则可能有安全风险。本设置仅在离线或防火墙保护的本地使用时方便调试。生产环境下建议设置密码而非完全禁用认证。

- **使用 Jupyter Notebook/Lab**：打开浏览器访问 Jupyter 后，可以创建 Notebook、编写和运行 Python 代码。JupyterLab 提供了多标签页界面，左侧还有文件浏览器，更加直观。**在WSL中运行的Jupyter，与Windows上的浏览器交互良好**，你可以像普通本地Notebook一样使用它。存储的文件实际上位于WSL的Linux系统中（例如默认启动目录为 `~/`）。你可以在Jupyter里浏览 `/mnt/c` 挂载来打开Windows文件。不过**推荐在Linux侧进行项目文件管理**，避免不同文件系统来回切换。

## 6. 安装 VSCode 或 PyCharm 并配置与 WSL 的连接

良好的开发环境有助于提高效率。**Visual Studio Code (VS Code)** 是免费且对 WSL 支持极佳的编辑器，而 **PyCharm** 则是强大的Python IDE（其专业版支持WSL）。你可以根据偏好选择，它们都可以在Windows上运行但使用WSL内的Python环境。这部分主要参考了[官方文档](https://learn.microsoft.com/zh-cn/windows/wsl/tutorials/wsl-vscode)

### 使用 VS Code + Remote WSL（推荐）

1. **安装 VS Code**：从 VS Code 官网下载安装包并安装 Windows 版 VScode。安装时勾选“**添加到 PATH**”的选项，这样方便从命令行启动 VScode。
2. **安装 Remote WSL 扩展**：启动 VSCode，点击左侧扩展商店图标，在搜索框输入“WSL”，找到 **Remote - WSL** 扩展并安装。安装完扩展后，重新加载 VSCode。
3. **连接到 WSL**：现在有几种方式打开WSL环境中的项目：
   - **在WSL终端中**：进入Linux目录后，直接运行 `code .` 。这会触发 VSCode 打开当前目录，并通过Remote WSL扩展在WSL中启动VSCode。第一次使用时VSCode会自动在WSL内安装一些服务器组件，请耐心等待完成。
   - **在VSCode中**：按 `Ctrl+Shift+P` 调出命令面板，输入“WSL”并选择“WSL: Connect to WSL”或“WSL: 新建 WSL 窗口” 。这样会打开一个针对WSL的VSCode窗口，然后可以通过 `File -> Open Folder` 打开Linux里的文件夹。
   - 连接成功的标志是在 VSCode 窗口左下角的绿色远程指示器上会显示 “WSL: <你的发行版名称>” (例如 “WSL: Ubuntu”)。此时，VSCode的终端、文件浏览等都是在Linux环境下操作。
4. **安装 VSCode 扩展（Python等）**：在远程WSL模式下，建议安装适用于Linux的Python扩展。VSCode在WSL下打开时，扩展面板会区分本地和WSL。若某些扩展未在WSL中安装，会出现一个按钮提示“在WSL中安装”。点击为WSL环境单独安装Python扩展、Pylance等，以获得语法高亮、智能提示和调试功能。
5. **选择解释器**：当你在VSCode中打开WSL的项目并创建Python文件/Notebook时，VSCode可能提示选择Python解释器。你可以在VSCode窗口底部蓝色状态栏点击Python版本，或按 `Ctrl+Shift+P` 输入 “Python: Select Interpreter”。在列表中选择 `/home/<user>/miniconda3/envs/dl_env/bin/python` (如果你使用conda环境)，这样可以让VSCode在该环境下运行代码。之后VSCode运行、调试Python代码都会使用WSL内的这个环境，非常方便。

> 💡 **VSCode 优势**：使用VSCode连接WSL，等于在Linux下开发但享受Windows图形界面，既可以使用Linux的工具链，也能直接访问Windows。你可以在VSCode集成终端中直接运行Linux命令。VSCode还支持在Notebook界面直接运行Jupyter（安装官方Jupyter扩展后，在.py或.ipynb文件中可以选择使用内置Notebook界面）。

### 使用 PyCharm (Professional) 配合 WSL

PyCharm 专业版支持将 WSL 中的解释器当作远程解释器使用，从而在Windows上编写代码、调用WSL内的Python环境执行。若你拥有 PyCharm 专业版，可以按以下思路配置（这部分主要参考的[官方文档](https://www.jetbrains.com/help/pycharm/using-wsl-as-a-remote-interpreter.html)）：

1. **安装 PyCharm**：从JetBrains官网下载并安装 PyCharm。在WSL中确保你已经安装好所需的Python（我们已用Miniconda安装）。建议先在WSL中创建好一个conda环境（如 `dl_env`）并安装好了需要的包（如PyTorch）。
2. **配置WSL解释器**：打开 PyCharm 的 Settings（设置） -> Project: ... -> Python Interpreter -> 点右侧设置图标 -> Add. 在弹出的“添加解释器”对话框，选择 **On WSL**。PyCharm会检测你的WSL发行版，比如“Ubuntu”；点击 Next。
3. **选择环境**：接下来可以选择在WSL中的：解释器
   - 若要使用已有的conda环境，选择 *Conda Environment*，然后在下方填写 Python 可执行文件路径。例如路径类似 `/home/<user>/miniconda3/envs/dl_env/bin/python`（可以在WSL下用 `which python` 获取路径）。
   - 或选择 *System Interpreter* 使用系统默认Python，或 *Virtualenv Environment* 创建一个新的虚拟环境（不推荐新建，直接使用conda环境即可）。
4. **完成配置**：点击 Create 完成。PyCharm 将连接WSL，索引该环境的库。之后在PyCharm底部状态栏会显示所选的WSL解释器。如果一切正常，你就可以像在本地一样运行、调试，只是代码实际上在WSL的Linux环境中执行。
5. **同步工具**：PyCharm会使用 WSL 内的 `\\wsl$` 路径访问文件。如果你的项目在Windows文件系统，需要确保PyCharm的路径映射正确（PyCharm通常会自动处理）。建议把项目直接放在WSL的Linux目录下，这样路径最简单，如 `/home/<user>/projects/myproj`。

> ⚠️ **社区版 PyCharm**：很遗憾，PyCharm 社区版不直接支持WSL远程。如果你没有专业版，可以考虑使用VSCode方案或者使用PyCharm的SSH Remote Interpreter功能配合类似`ssh localhost`到WSL的方法（较繁琐，不在本教程展开）。

总的来说，对于零基础用户，**VS Code + WSL** 是非常顺畅的开发体验，而且完全免费。配置完成后，你可以在VSCode中使用我们在WSL安装的conda环境来编写和运行深度学习代码，无需离开VSCode界面。

## 7. 常用 Linux 指令和操作入门

在WSL的Ubuntu中，掌握一些基本的Linux命令会非常有帮助：

- **文件/目录操作**：
  - `pwd`：打印当前工作目录 (print working directory)。
  - `ls`：列出当前目录内容（加 `-l` 参数可显示详细信息，加 `-a` 可包括隐藏文件）。
  - `cd <目录>`：切换目录。例：`cd ~/projects` 切换到 home 下的 projects 目录；`cd ..` 返回上级目录。
  - `mkdir <目录名>`：创建新目录。
  - `cp <源> <目的>`：复制文件或目录（加 `-r` 复制目录及其内容）。
  - `mv <源> <目的>`：移动或重命名文件/目录。
  - `rm <文件>`：删除文件（加 `-r` 删除目录及内容 **小心使用**）。
  - `cat <文件>`：查看文本文件内容（`head` 查看前几行，`tail` 查看最后几行）。
- **包管理和系统**：
  - `sudo apt install <软件名>`：通过APT包管理器安装软件包（例如 `sudo apt install git` 安装 Git）。首次使用APT前通常先运行 `sudo apt update` 更新软件源索引。
  - `sudo apt remove <软件名>`：卸载软件。
  - `top` 或 `htop`（需自行安装）：实时查看进程和系统资源占用。
  - `free -h`：查看内存使用情况，`df -h` 查看磁盘使用情况。
- **Conda/Pip**：
  - `conda activate <环境>` / `conda deactivate`：激活/退出环境（如第3节所述）。
  - `conda install <包名>`：安装包到当前环境。
  - `pip install <包名>`：在当前环境下用pip安装Python包（通常在想安装conda仓库没有的包时使用）。
  - `pip list` 或 `conda list`：列出已安装包。
- **运行Python脚本**：
  - 在终端中进入脚本所在目录，执行 `python script.py` 即可运行（这里的`python`会使用当前激活环境的Python）。
  - 进入Python交互式解释器：直接输入 `python` 或 `ipython`（后者需要 `pip install ipython` 安装）。
- **WSL命令**：
  - 在Windows中可通过命令 `wsl` 或 `ubuntu` 打开WSL终端。`wsl -l -v` 列出WSL发行版版本（前面提到）。
  - `wsl --shutdown`：强制关闭所有WSL实例。有时WSL资源占用过高或GPU驱动更新需要重启WSL，可使用此命令，然后再次启动Ubuntu。
  - WSL中调用Windows可执行文件：直接输入Windows程序名称即可运行，例如在WSL中输入 `explorer.exe .` 可打开当前目录在Windows资源, 输入 `code .` 调用VSCode 等。
- **编辑器**：
  - Ubuntu预装了`vim`（高阶一些）和`nano`（简单易用）。`nano <文件>` 可进入一个简单编辑界面，编辑完按 `Ctrl+O` 保存，`Ctrl+X` 退出。
  - 如果安装了 VSCode 的 Remote WSL，可以直接在VSCode中编辑WSL的文件，这对新手来说更直观（参见第6节）。
- **退出WSL**：
  - 直接关闭终端窗口即可。如果需要从WSL内部退出到Windows终端，输入 `exit` 即可关闭当前Shell会话。

掌握以上基础命令足以完成多数基本操作。如果需要了解特定命令的用法，可以使用 `man <命令>` 查看手册，或在网上查找使用示例。

## 8. 其他建议配置与技巧

在搭建完基本环境后，这里介绍一些实用的优化和技巧，帮助初学者更顺畅地使用深度学习环境（[参考文档](https://cloud.tencent.com/developer/article/2379096)）：

- **配置 pip 国内镜像源**：pip 默认从 Python 官方的 PyPI 下载包，在国内可能较慢。可以配置 pip 使用国内镜像源以提升速度。方法：在主目录下创建文件 `~/.pip/pip.conf`，写入：

  ```ini
  [global]
  timeout = 6000
  index-url = https://pypi.tuna.tsinghua.edu.cn/simple
  trusted-host = pypi.tuna.tsinghua.edu.cn
  ```

  这样 pip 安装包时将从清华镜像加速。此外，Anaconda的默认源也可以通过配置 `.condarc` 切换为清华镜像源，参考第2节可选步骤或 Anaconda 镜像站说明。

- **环境变量配置**：如果你经常需要自定义 Linux 环境变量（如 `PATH`、`http_proxy` 等），可以编辑 `~/.bashrc` 或 `~/.bash_profile` 文件添加 `export VAR=value` 行。每次打开WSL时这些脚本都会执行。Miniconda 安装时已自动在 `.bashrc` 末尾添加了初始化代码（`conda initialize`），确保不要删除它，否则 conda 命令将不可用。你也可以在 `.bashrc` 中配置一些别名(alias)提升效率，例如：

  ```bash
  alias ll='ls -alF'   # 定义 ll 为 ls -alF 列表别名
  ```

  添加后保存文件，`source ~/.bashrc` 让修改立即生效。

- **GPU 使用建议**：在WSL下使用GPU进行训练时，显存资源由Linux这边管理，但实际上还是调用Windows驱动。如果需要同时在Windows上使用GPU（例如运行Windows程序如浏览器、IDE也在占用显卡），要注意显存是否够用。`nvidia-smi` 可以在Windows PowerShell和WSL下分别运行，观察两个系统的GP-L0】。通常训练时尽量不要让Windows前台占用过多GPU资源。另外，目前WSL不支持AMD显卡通过CUDA，但可通过DirectML使用（需框架特别版本），超出本文范围。

- **保持 WSL 和驱动更新**：WSL是actively维护的，建议不时运行 `wsl --update` 更新Linux内核，以获得新功能和修复(特别是GPU)。

- **备份环境**：当配置好环境后，最好做好备份。例如导出conda环境依赖列表：`conda env export > env.yaml` 保存环境规格。这样在别的机器或重装时，可以用 `conda env create -f env.yaml` 恢复相同环境（需要相同平台）。重要数据和代码也应定期备份(可以利用Windows的工具或Git进行版本管理)。