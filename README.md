# ZoomTranslator

Zoom 会议转录的实时翻译和 AI 总结工具。

## 功能特性

- **实时监控翻译** (`monitoring.py`) - 监控文件夹中的 .txt 文件变化，使用 DeepL API 实时翻译
- **智能总结** (`summarize.py`) - 对最新的转录文件进行全文翻译和 AI 总结

## 安装依赖

```bash
pip install -r requirements.txt
```

## 使用方法

### 1. 实时监控翻译 (monitoring.py)

监控指定文件夹，当检测到 .txt 文件变化时自动翻译最新的内容。

#### 配置文件方式

编辑 `monitoring.json`：

```json
{
  "deepl_api_key": "你的DeepL API密钥",
  "folder_to_watch": "要监控的文件夹路径",
  "target_lang": "ZH",
  "lines_to_process": 50
}
```

运行：

```bash
python monitoring.py
```

#### 命令行参数方式

```bash
# 基本用法
python monitoring.py --folder /path/to/watch --api-key YOUR_DEEPL_KEY

# 指定目标语言
python monitoring.py --folder /path/to/watch --target-lang JA

# 使用自定义配置文件
python monitoring.py --config /path/to/monitoring.json
```

#### 参数说明

| 参数 | 短参数 | 说明 |
|------|--------|------|
| `--folder` | `-f` | 要监控的文件夹路径 |
| `--api-key` | `-k` | DeepL API 密钥 |
| `--target-lang` | `-t` | 目标语言（如 ZH, EN-US, JA, ES） |
| `--config` | `-c` | 自定义配置文件路径 |

#### 支持的语言代码

- `ZH` - 中文（简体）
- `EN-US` / `EN-GB` - 英语（美式/英式）
- `JA` - 日语
- `ES` - 西班牙语
- `FR` - 法语
- `DE` - 德语
- `IT` - 意大利语
- `KO` - 韩语

### 2. AI 智能总结 (summarize.py)

找到最新的转录文件，先用 DeepL 翻译全文，再用 ChatGPT API 生成智能总结。

#### 配置文件方式

编辑 `summarize.json`：

```json
{
  "input_folder": "输入文件夹路径",
  "output_folder": "输出文件夹路径",
  "api_url": "http://your-chatgpt-api-url",
  "model": "gpt-5-thinking",
  "prompt_template": "{content} \n\n请总结这段内容",
  "deepl_api_key": "你的DeepL API密钥",
  "deepl_target_lang": "ZH"
}
```

运行：

```bash
python summarize.py
```

#### 命令行参数方式

```bash
# 基本用法
python summarize.py --input /path/to/transcripts --output /path/to/output

# 指定模型
python summarize.py --model gpt-4

# 自定义 prompt
python summarize.py --prompt "{content}\n\n请用英文总结"

# 配置 DeepL
python summarize.py --deepl-api-key YOUR_KEY --deepl-target-lang JA

# 使用自定义配置文件
python summarize.py --config /path/to/summarize.json
```

#### 参数说明

| 参数 | 短参数 | 说明 |
|------|--------|------|
| `--input` | `-i` | 输入文件夹路径 |
| `--output` | `-o` | 输出文件夹路径 |
| `--model` | `-m` | AI 模型名称 |
| `--prompt` | `-p` | 自定义 prompt 模板（使用 `{content}` 作为占位符） |
| `--deepl-api-key` | `-d` | DeepL API 密钥 |
| `--deepl-target-lang` | `-l` | DeepL 目标语言 |
| `--config` | `-c` | 自定义配置文件路径 |

#### 自定义 Prompt 模板

在 `prompt_template` 中使用 `{content}` 作为占位符，程序会自动替换为文件内容。

示例：

```json
{
  "prompt_template": "{content}\n\n请按以下格式总结:\n1. 会议主题\n2. 关键决策\n3. 行动项"
}
```

#### 输出文件格式

生成的文件包含两部分：

```
================================================================================
全文翻译 (Full Translation)
================================================================================

[DeepL 翻译的全文内容]

================================================================================
AI 总结 (AI Summary)
================================================================================

[ChatGPT 生成的总结内容]
```

**注意**：控制台只显示 AI 总结部分，完整的翻译和总结都保存在输出文件中。

#### 禁用 DeepL 翻译

如果不需要全文翻译，只需要 AI 总结，可以：

1. 从 `summarize.json` 中移除 `deepl_api_key` 配置
2. 或运行时不提供 `--deepl-api-key` 参数

程序会自动跳过翻译步骤，只进行 AI 总结。

## 配置优先级

命令行参数 > 配置文件 > 默认值

## 常见问题

### DeepL API 配额查看

两个工具启动时都会显示当前的 DeepL API 使用情况：

```
DeepL Usage: 123,456/500,000 characters (24.7%)
```

### 监控模式 vs 单次处理

- **monitoring.py** - 持续监控，适合实时翻译场景（如正在进行的会议）
- **summarize.py** - 单次处理，适合会议结束后的完整总结

## 许可证

本项目仅供学习和研究使用。
