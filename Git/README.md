
# Pro Git，第二版，简体中文

* _作为Git官方推荐书籍，《Pro Git》值得Git初学者和爱好者认真阅读一遍。_

原文：https://github.com/progit/progit2

中文翻译-原文：https://github.com/bingohuang/progit2-gitbook

# 原书前言
欢迎阅读 Pro Git 第二版。

你可以在以下地址找到本书的在线版本：http://git-scm.com/book

与第一版类似，Pro Git 第二版以知识共享协议开源。

自第一版开源以来，许多事情都发生了变化。
其一，我们将本书的文本由 Markdown 迁移至 Asciidoc。
同时，我们还开始使用 O'Reilly 的 [Atlas平台](https://atlas.oreilly.com) 持续构建生成本书，因此，所有主要格式在每种语言都是可用的。

# 原书序言

## Scott Chacon 序

欢迎来到 Pro Git 第二版。
第一版出版到现在已经过去了四年。
到今天，Git 虽然出现了许多改变，但是还有很多重要的事情一如昨日。
因为 Git 核心团队对保持向后兼容性异常固执，所以直到今天大多数核心命令与概念依然有效，但是围绕 Git 的社区还是有一些重大的增加与改变。
本书的第二版就是为了更新书籍并讲解那些改动以使其对新用户更有帮助。

当我写第一版时，Git 对于超级黑客来说还是一个相对难用，只能勉强接受的工具。
它开始在特定的社区中快速发展，但是还没有达到像今天一样无处不在的地步。
自那时起，几乎每一个开源社区都采用了它。
Git 在 Windows 上取得了难以置信的进步，包括所有平台的图形用户界面对它的支持、IDE 的支持，以及商业使用的爆炸式发展。
四年前的 Pro Git 对此一无所知。
新版本的主要目标之一就是涉及 Git 社区中那些所有新的前沿领域。

使用 Git 的开源社区也呈现出爆炸式的发展。
大概在五年前吧，我坐下来写这本书时（写完第一个版本花了我不少时间），我开始在一个知名度极小的开发 Git 托管网站的公司工作，这家公司就是 GitHub。
本书出版时大概有几千人在使用 GitHub 网站，而为其工作的只有我们四个人。
在我写这篇介绍时，GitHub 宣布我们托管了 1000 万个项目、拥有大概 500 万注册开发者账户与大概 230 名员工。
爱它也好，恨它也罢，当我坐下来写第一版时，GitHub 以一种意想不到的方式猛烈地改变了一大批开源社区。

我在 Pro Git 的原始版本中写了一节我并不是很满意的内容，是作为和提供 Git 托管服务相关的例子的 GitHub。
我在书里写的东西本质上都是和社区有关的，但是又不得不讨论到我的公司，这点我不喜欢。
同时我还不喜欢那个兴趣的冲突，GitHub 在 Git 社区中的重要性是无法避免的。
我已经决定将本书的那部分转变为深度介绍 GitHub 是什么以及如何高效地使用它，而不再是作为一个 Git 托管的例子。
如果你正学习如何使用 Git，那么了解如何使用 GitHub 将会帮助你加入到一个巨大的社区中。不论你决定为自己的代码使用哪一个 Git 托管服务，这都很有价值。

自从上次出版以来另一个重大变革是 Git 网络传输 HTTP 协议的开发与崛起。书中的大多数例子都已经从 SSH 切换到 HTTP，因为它更简单。

在过去这几年看到 Git 从一个相对无名的版本管理系统成长为商业与开源版本管理的事实标准是令人吃惊的。我很高兴 Pro Git 做得很好并已经成为市场上几本既成功又完全开源的技术书籍之一。

我希望你能享受这个升级版的 Pro Git。

## Ben Straub 序

本书的第一版就是将我与 Git 结下不解之缘的原因。书中采用的是我引进的做软件的风格，这种风格比我之前看到的任何事情都要自然。那时我已经做了好几年开发者了，但是这本书将我指引到一条更加精彩的道路上。

几年之后的现在，我是 Git 的一个主要实现的贡献者，我在最大的 Git 托管公司工作，我已经环游世界教人们使用 Git。当 Scott 问我是否有兴趣在第二版上工作时，我甚至连想都没想就答应了。

能在这本书上工作是一份巨大的快乐与荣耀。我希望它能像帮助我一样帮助你。


## 献辞

_致我的妻子，Becky，没有她的话这段冒险不会开始。— Ben_

_谨以此书献给我的家人。
给这些年一直支持着我的妻子 Jessica 和女儿 Josephine，
还有那些在我风烛残年之时还能支持我的人。— Scott_

# 原书引言

你将花费你生命中的若干小时来阅读有关 Git 的相关内容。让我们用几分钟时间来介绍下我们将给你讲解的内容。
下面是本书正文十章和附录三章的快速总结。

[第一章](01-introduction/1-introduction.md)

 - 我们将介绍版本控制系统（VCSs）和 Git 的基本概念——不涉及技术内容，仅仅是什么是 Git，为什么它会成为 VCSs 大家庭中的一员，它与其它 VCSs 的区别，以及为什么那么多人都在使用 Git。然后，我们将介绍如何下载 Git 以及如果你的系统没有安装 Git，如何为第一次运行做准备。

[第二章]()

 - 我们将阐述 Git 的基本使用——包含你在使用 Git 时可能遇到的 80% 的情形。通过阅读本章，你应该能够克隆仓库、查看项目历史、修改文件和贡献更改。如果本书在此刻自燃，你应该已经能够使用已经学到的漂亮有用的 Git 知识获取到另外一份拷贝。

[第三章]()

 - 关注于 Git 的分支模型。分支模型通常被认为是 Git 的杀手级特性。这里，你将学习到究竟是什么让 Git与众不同。学习完本章，你可能需要一段时间来思考，在 Git 分支成为你的生活的一部分之前，你到底是如何生活的。

[第四章]()

 - 关注于服务器端的 Git。本章面向那些希望在你自己的组织或个人服务器搭建用于合作的 Git 的读者。如果你希望让别人处理这些事务，我们也会探讨一些托管选项。

[第五章]()

 - 将阐述多种分布式工作流的细节，以及如何使用 Git 实现它们。学习完本章，你应该能够在多个远程仓库之间游刃有余，通过电子邮件使用 Git，熟练地处理多个远程分支和合作者贡献的补丁。

[第六章]()

 - 介绍 GitHub 托管服务以及深层次的工具。我们将涵盖注册与账户管理，创建和使用 Git 仓库，贡献项目的普通工作流以及接受他人的贡献，GitHub 的可编程接口和那些能够让你的生活变得更简单的小技巧。

[第七章]()

 - 关于 Git 的高级命令。你将学习到一些高级主题，诸如掌握可怕的“reset”命令，使用二分搜索识别错误，编辑历史，细节版本选择等等。本章的介绍将丰富你的 Git 知识，让你成为一个真正的大师。

[第八章]()

 - 关于 Git 环境的自定义配置，包括设置用于增强或促进自定义策略的钩子脚本以及按照你所需要的方式进行工作的环境配置。我们还会介绍构建你自己的脚本集，以增强自定义提交策略。

[第九章]()

 - 对比 Git 和其它 VCSs，包括在 Subversion（SVN）的世界使用 Git 以及从其它 VCSs 迁移到 Git。很多组织仍在使用 SVN，并且也没有计划改变，此时，你将了解到 Git 不可思议的能力——本章将展示，在你不得不使用 SVN 服务器的时候如何协同合作。我们还将介绍如何从不同系统导入项目，以便你能够全身心投入 Git 的怀抱。

[第十章]()

 - 深入 Git 阴暗而漂亮的实现细节。现在，你已经知道所有有关 Git 的知识，能够熟练运用 Git 的强大优雅的功能。接下来，你可以继续学习 Git 如何存储对象、Git 的对象模型是怎样的、打包文件的细节、服务器协议等更多知识。本书自始至终都将引用本章的内容，以便你能够在当时就可以深入了解。但是，如果你像我们一样希望深入学习技术细节，你可能想先阅读第十章。我们将选择权交给你。

在 [附录 A]()，我们学习多个在特定环境中使用 Git 的实例。我们涵盖多个你可能会使用 Git 的多个 GUI 和 IDE 编程环境，这些都可以由你自己选择。如果你想在 shell、Visual Studio 或 Eclipse 中使用 Git，请阅读本章。

在 [附录 B]()，我们探讨通过类似 libgit2 和 JGit 的工具编写 Git 脚本、扩展 Git。如果你对编写复杂、快速的自定义工具感兴趣，需要了解 Git 的底层访问，本章就是你所需要了解的。

最后在 [附录 C]()，我们一次性浏览 Git 的所有主要命令，复习在本书中介绍的内容，回忆我们能够使用这些命令做什么。如果你需要知道本书中我们使用了哪些特定 Git 命令，你可以在这里查阅。

下面让我们开始学习吧！

# Summary

. [README.md][前言]
. link:01-introduction/1-introduction.adoc[起步]
.. link:01-introduction/sections/about-version-control.adoc[关于版本控制]
.. link:01-introduction/sections/history.adoc[Git 简史]
.. link:01-introduction/sections/basics.adoc[Git 基础]
.. link:01-introduction/sections/command-line.adoc[命令行]
.. link:01-introduction/sections/installing.adoc[Git]
.. link:01-introduction/sections/first-time-setup.adoc[初次运行 Git 前的配置]
.. link:01-introduction/sections/help.adoc[获取帮助]
.. link:01-introduction/sections/summary.adoc[总结]
. link:02-git-basics/1-git-basics.adoc[Git 基础]
.. link:02-git-basics/sections/getting-a-repository.adoc[获取 Git 仓库]
.. link:02-git-basics/sections/recording-changes.adoc[记录每次更新到仓库]
.. link:02-git-basics/sections/viewing-history.adoc[查看提交历史]
.. link:02-git-basics/sections/undoing.adoc[撤消操作]
.. link:02-git-basics/sections/remotes.adoc[远程仓库的使用]
.. link:02-git-basics/sections/tagging.adoc[打标签]
.. link:02-git-basics/sections/aliases.adoc[Git 别名]
.. link:02-git-basics/sections/summary.adoc[总结]
. link:03-git-branching/1-git-branching.adoc[Git 分支]
.. link:03-git-branching/sections/nutshell.adoc[分支简介]
.. link:03-git-branching/sections/basic-branching-and-merging.adoc[分支的新建与合并]
.. link:03-git-branching/sections/branch-management.adoc[分支管理]
.. link:03-git-branching/sections/workflows.adoc[分支开发工作流]
.. link:03-git-branching/sections/remote-branches.adoc[远程分支]
.. link:03-git-branching/sections/rebasing.adoc[变基]
.. link:03-git-branching/sections/summary.adoc[总结]
. link:04-git-server/1-git-server.adoc[服务器上的 Git]
.. link:04-git-server/sections/protocols.adoc[协议]
.. link:04-git-server/sections/git-on-a-server.adoc[在服务器上搭建 Git]
.. link:04-git-server/sections/generating-ssh-key.adoc[生成 SSH 公钥]
.. link:04-git-server/sections/setting-up-server.adoc[配置服务器]
.. link:04-git-server/sections/git-daemon.adoc[Git 守护进程]
.. link:04-git-server/sections/smart-http.adoc[Smart HTTP]
.. link:04-git-server/sections/gitweb.adoc[GitWeb]
.. link:04-git-server/sections/gitlab.adoc[GitLab]
.. link:04-git-server/sections/hosted.adoc[第三方托管的选择]
.. link:04-git-server/sections/summary.adoc[总结]
. link:05-distributed-git/1-distributed-git.adoc[分布式 Git]
.. link:05-distributed-git/sections/distributed-workflows.adoc[分布式工作流程]
.. link:05-distributed-git/sections/contributing.adoc[向一个项目贡献]
.. link:05-distributed-git/sections/maintaining.adoc[维护项目]
.. link:05-distributed-git/sections/summary.adoc[总结]
. link:06-github/1-github.adoc[Github]
.. link:06-github/sections/1-setting-up-account.adoc[账户的创建和配置]
.. link:06-github/sections/2-contributing.adoc[对项目做出贡献]
.. link:06-github/sections/3-maintaining.adoc[维护项目]
.. link:06-github/sections/4-managing-organization.adoc[管理组织]
.. link:06-github/sections/5-scripting.adoc[脚本 GitHub]
.. link:06-github/sections/6-summary.adoc[总结]
. link:07-git-tools/1-git-tools.adoc[Git 工具]
.. link:07-git-tools/sections/revision-selection.adoc[选择修订版本]
.. link:07-git-tools/sections/interactive-staging.adoc[交互式暂存]
.. link:07-git-tools/sections/stashing-cleaning.adoc[储藏与清理]
.. link:07-git-tools/sections/signing.adoc[签署工作]
.. link:07-git-tools/sections/searching.adoc[搜索]
.. link:07-git-tools/sections/rewriting-history.adoc[重写历史]
.. link:07-git-tools/sections/reset.adoc[重置揭密]
.. link:07-git-tools/sections/advanced-merging.adoc[高级合并]
.. link:07-git-tools/sections/rerere.adoc[Rerere]
.. link:07-git-tools/sections/debugging.adoc[使用 Git 调试]
.. link:07-git-tools/sections/submodules.adoc[子模块]
.. link:07-git-tools/sections/bundling.adoc[打包]
.. link:07-git-tools/sections/replace.adoc[替换]
.. link:07-git-tools/sections/credentials.adoc[凭证存储]
.. link:07-git-tools/sections/summary.adoc[总结]
. link:08-customizing-git/1-customizing-git.adoc[自定义 Git]
.. link:08-customizing-git/sections/config.adoc[配置 Gits]
.. link:08-customizing-git/sections/attributes.adoc[Git 属性]
.. link:08-customizing-git/sections/hooks.adoc[Git 钩子]
.. link:08-customizing-git/sections/policy.adoc[使用强制策略的一个例子]
.. link:08-customizing-git/sections/summary.adoc[总结]
. link:09-git-and-other-scms/1-git-and-other-scms.adoc[Git 与其他系统]
.. link:09-git-and-other-scms/sections/git-client.adoc[作为客户端的 Git]
.. link:09-git-and-other-scms/sections/git-migrate.adoc[迁移到 Git]
.. link:09-git-and-other-scms/sections/summary.adoc[总结]
. link:10-git-internals/1-git-internals.adoc[Git 内部原理]
.. link:10-git-internals/sections/plumbing-porcelain.adoc[底层命令和高层命令]
.. link:10-git-internals/sections/objects.adoc[Git 对象]
.. link:10-git-internals/sections/refs.adoc[Git 引用]
.. link:10-git-internals/sections/packfiles.adoc[包文件]
.. link:10-git-internals/sections/refspec.adoc[引用规格]
.. link:10-git-internals/sections/transfer-protocols.adoc[传输协议]
.. link:10-git-internals/sections/maintenance.adoc[维护与数据恢复]
.. link:10-git-internals/sections/environment.adoc[环境变量]
.. link:10-git-internals/sections/summary.adoc[总结]
. link:A-git-in-other-environments/1-git-other-environments.adoc[附录A：其它环境中的 Git]
.. link:A-git-in-other-environments/sections/guis.adoc[图形界面]
.. link:A-git-in-other-environments/sections/visualstudio.adoc[Visual Studio 中的 Git]
.. link:A-git-in-other-environments/sections/eclipse.adoc[Eclipse 中的 Git]
.. link:A-git-in-other-environments/sections/bash.adoc[Bash 中的 Git]
.. link:A-git-in-other-environments/sections/zsh.adoc[Zsh 中的 Git]
.. link:A-git-in-other-environments/sections/powershell.adoc[Powershell 中的 Git]
.. link:A-git-in-other-environments/sections/summary.adoc[总结]
. link:B-embedding-git/1-embedding-git.adoc[附录B：将 Git 嵌入你的应用]
.. link:B-embedding-git/sections/command-line.adoc[命令行 Git 方式]
.. link:B-embedding-git/sections/libgit2.adoc[Libgit2]
.. link:B-embedding-git/sections/jgit.adoc[JGit]
. link:C-git-commands/1-git-commands.adoc[附录C：Git 命令]
. link:contributors.adoc[原书贡献者]
