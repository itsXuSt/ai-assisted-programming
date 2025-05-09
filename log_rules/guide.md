# Cursor 中 Qt/DTK 日志规范应用指南

本指南旨在介绍如何在 Cursor IDE 中利用规则系统和提示词来辅助 Qt/DTK 日志的规范化打印，提高开发效率。

## Cursor 规则系统简介

Cursor 支持通过自定义指令（Custom Instructions）来创建开发规则，这些规则可以帮助 AI 生成符合项目标准的代码。对于 Qt/DTK 日志规范，我们可以:

1. 设置全局规则
2. 设置项目特定规则
3. 通过提示词临时激活规则

## 配置 Cursor 中的 Qt/DTK 日志规则

### 创建全局日志规则

1. 打开 Cursor 设置
2. 找到 "Rule"
3. 添加以下规则内容:

```
# 角色

你是一名优秀的 Dtk/Qt 开发者，与你交流的用户有基础的编码能力，能看懂代码逻辑。

# 约束

1. 你常写注释，代码风格很好；
2. 你日常使用中文交流，但生成提交信息时使用英文；
3. 涉及到样式上的改动，不要使用样式表；
4. 在新建文件时，你必须遵守以下日志风格；在维护旧文件时，也必须按照以下风格重新整理日志，并且在做日志整理时，你被禁止修改任何业务逻辑代码；

## Qt/DTK 日志规范

[这里可以添加之前日志规范的内容]
```

### 创建项目特定规则

对于特定项目，可以在项目根目录中创建 `.cursor` 文件夹，并添加 `instructions.md` 文件：

```bash
mkdir -p .cursor
touch .cursor/instructions.md
```

然后在该文件中添加项目特定的日志规则：

```markdown
# 项目日志规范

本项目使用以下日志分类:
- app.ui - 用于UI相关日志
- app.network - 用于网络相关日志
- app.data - 用于数据处理相关日志

所有日志必须使用分类方式 (qCDebug/qCInfo/qCWarning/qCCritical)，并且使用英文记录。
```

## 使用提示词生成符合规范的日志代码

### 激活日志规则的提示词

在与 Cursor AI 交互时，可以使用以下提示词激活日志规则：

```
请在接下来的代码生成中遵循 Qt/DTK 日志规范，确保使用分类日志，并根据情况选择适当的日志级别。
```

### 常用日志相关提示词模板

#### 1. 生成带日志的新函数

```
请根据 Qt/DTK 日志规范生成一个函数，功能是 [功能描述]。
函数名：[函数名]
参数：[参数列表]
返回值：[返回值类型]
使用日志分类：[分类名]
```

#### 2. 为现有函数添加日志

```
请为以下函数添加符合 Qt/DTK 规范的日志记录：

[粘贴需要添加日志的函数代码]

日志分类名请使用：[分类名]
```

#### 3. 重构现有日志

```
请将以下代码中的日志调用重构为符合 Qt/DTK 规范的分类日志：

[粘贴需要重构的代码]

使用的日志分类名为：[分类名]
```

#### 4. 批量创建日志分类定义

```
请为我的项目创建以下模块的日志分类定义：
- [模块1名称]
- [模块2名称]
- [模块3名称]

每个模块需要包含头文件声明和源文件定义。
```

## 实际提示词示例

### 示例1: 生成带日志的文件操作函数

```
请根据 Qt/DTK 日志规范生成一个函数，功能是保存文件内容到指定路径。
函数名：saveFile
参数：const QString &filePath, const QByteArray &content
返回值：bool (表示操作是否成功)
使用日志分类：fileOpsLog
```

### 示例2: 重构日志代码

```
请将以下代码中的日志调用重构为符合 Qt/DTK 规范的分类日志：

bool connectDatabase()
{
    qDebug() << "Connecting to database...";
    
    if (!db.isValid()) {
        qDebug() << "Database configuration is invalid!";
        return false;
    }
    
    bool success = db.open();
    if (success) {
        qDebug() << "Successfully connected to database";
    } else {
        qDebug() << "Failed to connect to database: " + db.lastError();
    }
    
    return success;
}

使用的日志分类名为：dbLog
```

### 示例3: 创建日志框架

```
请为我的应用程序创建完整的日志框架，包括：

1. 在main.cpp中初始化日志系统
2. 为以下模块创建日志分类：
   - 网络模块 (NetworkManager)
   - 文件操作模块 (FileManager)
   - 用户界面模块 (UIController)

提供每个模块的头文件和源文件中的日志类别声明和定义代码。
```

## Cursor 中的日志编辑快捷方式

### 日志重构命令

通过以下提示词可以快速重构整个文件的日志:

```
请将当前文件中所有的 qDebug、qInfo、qWarning 和 qCritical 调用重构为对应的 qCDebug、qCInfo、qCWarning 和 qCCritical 形式，使用日志分类名: [分类名]
```

## 如何让 Cursor 生成更好的日志代码

### 更有效的提示词策略

1. **明确上下文**: 
```
这是一个网络管理模块，负责处理HTTP请求。请在生成代码时，确保添加合适的日志记录关键操作和错误情况。
```

2. **指定日志级别使用原则**:
```
请在生成代码时遵循以下原则：
- 使用 qCDebug 记录内部步骤和状态
- 使用 qCInfo 记录重要操作完成
- 使用 qCWarning 记录可恢复的异常
- 使用 qCCritical 记录严重错误
```

3. **引导生成特定日志点**:
```
在网络请求开始、结束以及发生错误时添加适当的日志记录，确保日志包含请求URL和响应状态码等关键信息。
```

## 自动化日志流程

### 使用 Cursor 批量操作

Cursor 允许通过批量编辑功能快速修改和添加日志。使用以下提示词:

```
请在此类的每个公共方法开始处添加 qCDebug 日志记录，记录方法名和关键参数。并在方法结束处添加表示完成的日志。
```

### 结合代码分析和日志添加

```
请分析这个函数的执行流程，在关键决策点和潜在错误点添加适当级别的日志记录。特别关注文件操作、网络请求和数据转换等容易出错的环节。
```

## 团队协作中的日志规范应用

### 创建项目日志模板

在项目初始化时，使用以下提示词生成项目日志模板:

```
请为我的Qt/DTK项目创建一套完整的日志框架模板，包括:
1. main.cpp中的日志初始化
2. 模块日志类别的定义方式
3. 示例日志使用代码
```

### 代码审查提示词

在代码审查中使用以下提示词检查日志规范:

```
请检查以下代码中的日志使用是否符合我们的Qt/DTK日志规范，特别注意:
1. 是否使用了分类日志
2. 日志级别是否选择适当
3. 日志消息是否清晰有用
4. 是否存在多余或缺失的日志点
```

## 常见问题解决

### 问题1: 如何在Cursor中快速将所有日志更新为分类日志?

使用以下提示词:
```
请帮我将当前文件中所有非分类日志(qDebug, qInfo等)替换为对应的分类日志(qCDebug, qCInfo等)，使用分类名"[分类名]"。
```

### 问题2: 如何让Cursor识别项目的日志风格?

在项目根目录创建示例日志文件，然后在提示中引用:
```
请参考项目中的 Logger.cpp 文件，以相同的风格为我的新类添加日志记录。
```

### 问题3: 如何让Cursor帮助进行日志级别评估?

```
请分析这段代码的执行流程和可能的失败点，评估应该在哪些位置添加什么级别的日志记录，然后实施这些改动。
``` 