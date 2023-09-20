# 第一章 

## **Getting Started**

编程语言不仅仅是语言本身，工具是使用语言的体验的关键元素之一。同样的情况也适用于许多其他技术（例如，像gRPC或Apache Avro这样的RPC框架），它通常对该技术本身的采用（或失败）产生了不成比例的影响。因此，在设计和教授语言本身时，工具应被视为一项重要关注的内容。

Rust社区从早期就将工具置于前沿地位，这一点表现得非常明显。现在，让我们简要了解一些在我们的旅程中将会有用的工具和实用程序。其中一些工具得到了Rust组织的官方支持，而其他一些则是由社区构建和维护的。

### **1.1 Installing The Rust Toolchain**

有多种安装Rust到您的系统的方法，但我们将关注推荐的方法：通过rustup。

关于如何安装rustup本身的说明可以在https://rustup.rs找到。

rustup不仅仅是一个Rust安装程序 - 它的主要价值在于工具链管理。

工具链是编译目标和发布通道的组合。

#### **1.1.1 Compilation Targets**

Rust编译器的主要目的是将Rust代码转换为机器代码 - 一组您的CPU和操作系统可以理解和执行的指令。因此，您需要为每个编译目标（即每个平台，例如64位Linux或64位OSX），为了生成可运行的可执行文件，都需要不同的Rust编译器后端。

Rust项目致力于支持广泛的编译目标，具有不同级别的保证。这些目标被分为不同的层次，从“保证工作”的Tier 1到“尽力而为”的Tier 3。您可以在[这里](https://doc.rust-lang.org/nightly/rustc/platform-support.html)找到详尽且最新的列表。



#### **1.1.2 Release Channels**

Rust编译器本身是一款不断发展和改进的软件：它每天都在得到数百名志愿者的贡献。Rust项目追求稳定性而不是停滞不前。引用Rust的文档：

> [...] 您不应该害怕升级到新版本的稳定Rust。每次升级应该是无痛的，但也应该为您带来新功能、更少的错误和更快的编译时间。

因此，对于应用程序开发，通常应依赖于最新发布版本的编译器来运行、构建和测试您的软件，这被称为稳定通道（stable channel）。每六周在稳定通道上发布一个新版本的编译器[1] - 在撰写本文时，最新版本是v1.59.0[2]。

还有两个其他发布通道：
- beta，用于下一个版本的候选版；
- nightly，每天从rust-lang/rust的主分支构建，因此得名。

使用beta编译器测试您的软件是支持Rust项目的众多方法之一 - 这有助于在发布日期之前捕获错误[3]。

nightly有不同的用途：它为早期采用者提供了未完成功能[4]的访问权限，这些功能在发布之前（甚至在稳定化的轨道上）都可以使用。

如果您计划在nightly编译器上运行生产软件，我建议您三思：它之所以被称为不稳定，是有原因的。

---

1. 更多关于发布计划的详细信息可以在[这里](https://doc.rust-lang.org/book/appendix-07-nightly-rust.html)找到。
2. 您可以在[Rust Forge](https://forge.rust-lang.org/)上查看下一个版本及其发布日期。
3. 由于Rust项目的CI/CD设置，beta版本包含问题的情况相当罕见。其中最有趣的组件之一是[crater](https://github.com/rust-lang/crater)，这是一个用于从[crates.io](https://crates.io/)和GitHub上爬取Rust项目的工具，构建它们并运行它们的测试套件以识别潜在的回归问题。[Pietro Albini](https://www.pietroalbini.org/)在RustFest 2019的演讲中以《[每六周发布一个编译器](https://www.pietroalbini.org/blog/shipping-a-compiler-every-six-weeks/)》的主题详细介绍了Rust的发布流程。
4. 您可以在《[The Unstable Book](https://doc.rust-lang.org/nightly/unstable-book/the-unstable-book.html)》中查看nightly版本中可用的功能标志列表。剧透：有很多功能标志可用。

----



#### **1.1.3 What Toolchains Do We Need?**

安装 Rust 的工具 rustup 将为您提供最新的稳定版本编译器，适用于您的主机平台。在本书中，我们将使用稳定版本的发布通道来构建、测试和运行我们的代码。

您可以使用 rustup update 命令来更新您的工具链，而 rustup toolchain list 命令将为您提供已安装在您系统上的概览信息。

我们不需要（也不会执行）任何交叉编译操作 - 我们的生产工作负载将在容器中运行，因此我们不需要从开发机器交叉编译到用于生产环境的目标主机。

### **1.2 Project Setup**

通过rustup安装工具链会捆绑多个组件。其中之一是Rust编译器本身，即rustc。您可以使用以下命令检查它的版本：

```
rustc --version
```

您不会花太多时间直接使用rustc - 您构建和测试Rust应用程序的主要接口将是cargo，Rust的构建工具。您可以使用以下命令验证一切是否正常运行：

```
cargo --version
```

让我们使用cargo创建我们将在整本书中使用的项目的框架：

```
cargo new zero2prod
```

这将创建一个新的zero2prod文件夹，具有以下文件结构：

```
zero2prod/
  Cargo.toml
  .gitignore
  .git/
  src/
    main.rs
```

该项目已经是一个git仓库。

如果您计划在GitHub上托管项目，您只需创建一个新的空仓库并运行以下命令：

```
cd zero2prod
git add .
git commit -am "Project skeleton"
git remote add origin git@github.com:YourGitHubNickName/zero2prod.git
git push -u origin main
```

我们将使用GitHub作为参考，因为它很受欢迎，而且最近发布了GitHub Actions功能用于CI流水线，但当然您可以自由选择任何其他git托管解决方案（或不使用git托管）。

### **1.3 IDEs**

项目的骨架已经准备好了，现在是时候启动您喜爱的编辑器，这样我们就可以开始进行操作了。

不同的人有不同的偏好，但我认为，特别是如果您刚刚开始学习一门新的编程语言，最起码要拥有支持语法高亮、代码导航和代码自动完成的设置。

语法高亮可以立即反馈明显的语法错误，而代码导航和代码自动完成则可以实现“探索性”编程：在源代码和依赖项之间快速切换，快速访问从一个 crate 导入的结构体或枚举上可用的方法，而无需不断在编辑器和 [docs.rs](https://docs.rs/) 之间切换。

您的 IDE 设置有两个主要选项：rust-analyzer 和 IntelliJ Rust。

#### **1.3.1 Rust-analyzer**

rust-analyzer 是针对 Rust 的 Language Server Protocol 的实现。

Language Server Protocol 使得可以在许多不同的编辑器中轻松使用 rust-analyzer，包括但不限于 VS Code、Emacs、Vim/NeoVim 和 Sublime Text 3。

您可以在这里找到特定编辑器的设置说明。

#### **1.3.2 IntelliJ Rust**

IntelliJ Rust为JetBrains开发的一系列编辑器提供了Rust支持。

如果您没有JetBrains许可证，IntelliJ IDEA可免费使用并支持IntelliJ Rust。

如果您拥有JetBrains许可证，那么CLion是JetBrains IDE套件中用于Rust的首选编辑器。

#### **1.3.3 What Should I Use?**

截至2022年3月，首选应该是使用IntelliJ Rust。

尽管rust-analyzer很有潜力，并在过去一年取得了令人难以置信的进展，但与IntelliJ Rust提供的IDE体验相比，它仍然有相当大的差距。

另一方面，IntelliJ Rust强制您使用JetBrains的IDE，这可能是您愿意或不愿意的。如果您想坚持使用您选择的编辑器，请查找其rust-analyzer的集成/插件。

值得一提的是，rust-analyzer是Rust编译器内部正在进行的更大的库化工作的一部分：rust-analyzer与rustc之间存在重叠，有很多重复的工作。

将编译器的代码库演变为一组可重用的模块将使rust-analyzer能够利用编译器代码库的日益庞大的子集，从而实现所需的按需分析功能，从而提供一流的IDE体验。

这是一个未来值得关注的有趣领域。

### **1.4 Inner Development Loop**

在我们的项目中工作时，我们将一遍又一遍地执行相同的步骤：

​	• 进行更改；
​		• 编译应用程序；
​		• 运行测试；
​		• 运行应用程序。
​	这也被称为内部开发循环。

内部开发循环的速度是您在单位时间内完成的迭代次数的上限。

如果编译和运行应用程序需要5分钟，那么您在一个小时内最多可以完成12次迭代。将其减少到2分钟，您现在可以在同一个小时内完成30次迭代！

在这里，Rust并不能帮助我们 - 编译速度在大型项目中可能会成为一个痛点。在继续之前，让我们看看在解决这个问题之前我们能做些什么。

#### **1.4.1 Faster Linking**

在关注内部开发循环时，我们主要关注的是增量编译的性能 - 即在对源代码进行小的更改后，Cargo 重新构建我们的二进制文件需要多长时间。
相当大一部分时间花在了链接阶段 - 根据先前编译阶段的输出来组装实际的二进制文件。
默认的链接器表现不错，但根据您使用的操作系统，还有更快的替代方案：
	• 在Windows和Linux上使用LLVM项目开发的链接器lld；
	• 在MacOS上使用zld。
要加快链接阶段的速度，您需要在计算机上安装替代链接器，并将以下配置文件添加到项目中：

```toml
# .cargo/config.toml
# On Windows
# ```
# cargo install -f cargo-binutils
# rustup component add llvm-tools-preview
# ```
[target.x86_64-pc-windows-msvc]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]
[target.x86_64-pc-windows-gnu]
rustflags = ["-C", "link-arg=-fuse-ld=lld"]
# On Linux:
# - Ubuntu, `sudo apt-get install lld clang`
# - Arch, `sudo pacman -S lld clang`
[target.x86_64-unknown-linux-gnu]
rustflags = ["-C", "linker=clang", "-C", "link-arg=-fuse-ld=lld"]
# On MacOS, `brew install michaeleisel/zld/zld`
[target.x86_64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/usr/local/bin/zld"]
[target.aarch64-apple-darwin]
rustflags = ["-C", "link-arg=-fuse-ld=/usr/local/bin/zld"]
```

有关Rust编译器的工作正在进行中，以尽可能使用lld作为默认链接器 - 不久之后，将不需要此自定义配置来实现更高的编译性能！

####  **1.4.2** **cargo-watch**

### **1.5 Continuous Integration**

#### **1.5.1 CI Steps**

##### **1.5.1.1 Tests**

##### **1.5.1.2 Code Coverage**

##### **1.5.1.3 Linting**

##### **1.5.1.4 Formatting**

##### **1.5.1.5 Security Vulnerabilities**

#### **1.5.2 Ready-to-go CI Pipelines**







