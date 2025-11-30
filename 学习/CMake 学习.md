#cmake

# Overview

现代化的 CMake 是围绕 **Target** 和 **Property** 来定义的
现代版的 CMake 更像是在遵循 OOP 的规则，通过 target 来约束 link、compile 等相关属性的作用域。

# Cheatsheets

列出所有构建选项（`option`）及其默认值：`cmake -LAH` 或者 `cmake -LH`

# Requirements

在 Target 中有两个概念非常重要：Build-Requirements 和 Usage-Requirements

- Build-Requirements： 包含了所有**构建 Target**必须的材料。如源代码，include 路径，预编译命令，链接依赖，编译/链接选项，编译/链接特性等。
- Usage-Requirements：包含了所有**使用 Target**必须的材料。如源代码，include 路径，预编译命令，链接依赖，编译/链接选项，编译/链接特性等。
  - 这些往往是当另一个 Target 需要使用当前 target 时，必须包含的依赖。

在 Moden CMake 中新增了不少关键字，其中最常见的是 PUBLIC、PRIVATE、INTERFACE。

- PRIVATE/INTERFACE/PUBLIC：定义了Target属性的传递范围。
  - PRIVATE: 表示 Target 的属性只定义在当前 Target 中，任何依赖当前 Target 的 Target 不共享 PRIVATE 关键字下定义的属性。
  - INTERFACE：表示Target的属性不适用于其自身，而只适用于依赖其的Target。
  - PUBLIC：表示 Target 的属性既是 build-requirements 也是 usage-requirements。凡是依赖。凡是依赖于当前 Target 的 Target 都会共享本属性。

# `find_package` vs `find_library`
