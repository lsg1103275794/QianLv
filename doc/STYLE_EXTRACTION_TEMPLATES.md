# 创作风格提取模板使用指南

## 概述

本文档介绍千虑项目中用于风格迁移的创作风格提取模板系统。这些模板从文学/文案创作者的角度深度提取文本的写作风格特征，为AI风格迁移提供精准的创作指导。

## 模板列表

### 1. 深度创作风格提取模板 (`creative_style_extraction.yaml`)

**适用场景**：
- 长文本风格分析（>500字）
- 需要详细风格指导的场景
- 专业文学作品分析
- 复杂风格的精准复制

**特点**：
- 8大分析维度，50+细分特征
- 量化分析（百分比、比例等）
- 详细的模仿要点和禁止事项
- 适合专业创作者和高质量风格迁移

**分析维度**：
1. 语言质感与用词特征
   - 词汇选择规律
   - 修辞手法特征
   - 语言节奏与音韵

2. 句式结构与表达模式
   - 句子长度规律
   - 句式类型分布
   - 句式复杂度
   - 特殊句式

3. 段落结构与篇章布局
   - 段落长度特征
   - 段落内部结构
   - 段落间衔接

4. 叙述视角与表达方式
   - 人称使用
   - 叙述角度
   - 时态运用

5. 描写技巧与细节处理
   - 描写类型偏好
   - 细节密度
   - 意象运用

6. 对话与引用特征
   - 对话风格
   - 引用手法

7. 情绪表达与语气控制
   - 情绪基调
   - 语气特征
   - 态度倾向

8. 创作技巧与风格标识
   - 独特技巧
   - 文体特征
   - 风格标签

**输出格式**：
```
【写作风格指南】

一、语言质感与用词
1. 词汇选择：[具体描述，包含量化数据]
2. 修辞手法：[列举主要修辞及使用规律]
3. 语言节奏：[描述节奏和音韵特点]

二、句式结构
...

【模仿要点】
- 必须遵循的核心特征：[3-5个]
- 可灵活调整的次要特征：[2-3个]
- 禁止事项：[2-3个]
```

### 2. 快速风格提取模板 (`quick_style_extraction.yaml`)

**适用场景**：
- 短文本风格分析（<500字）
- 快速风格迁移需求
- 实时风格模仿
- 简单风格的快速复制

**特点**：
- 5大核心维度
- 简洁输出（每个特征1-2句话）
- 快速生成风格指南
- 适合日常写作和快速应用

**分析维度**：
1. 语言风格（30%权重）
2. 句式特征（25%权重）
3. 段落结构（15%权重）
4. 叙述方式（15%权重）
5. 情绪语气（15%权重）

**输出格式**：
```
【快速风格指南】

核心特征：
1. 语言：[1-2句话]
2. 句式：[1句话]
3. 段落：[1句话]
4. 视角：[1句话]
5. 语气：[1句话]

风格标签：[3-5个关键词]

模仿要点：
- 必须保持：[2-3个]
- 可以调整：[1-2个]
- 避免使用：[1-2个]
```

## 使用方法

### 后端API调用

模板已集成到风格迁移API中，会自动使用：

```python
# src/api/routes/transfer.py

# 风格提取时自动使用模板
style_guidance = await _extract_style_guidance(
    text=source_text,
    provider=provider,
    model=model,
    use_template=True,  # 启用模板
    template_name="creative_style_extraction"  # 指定模板
)
```

### 前端调用

前端无需修改，风格迁移功能会自动使用新模板：

```javascript
// frontend/src/components/style/StyleTransfer.vue

const response = await api.performStyleTransfer(
  payload.input_type,
  payload.new_theme,
  payload.source_text,
  payload.analysis_report_id,
  payload.provider,
  payload.model
);
```

### 直接使用模板

如果需要单独使用模板进行风格分析：

```python
from pathlib import Path
import yaml

# 加载模板
template_path = Path("config/prompt_templates/creative_style_extraction.yaml")
with open(template_path, 'r', encoding='utf-8') as f:
    template = yaml.safe_load(f)

# 构建提示词
prompt = template['full_prompt_template'].replace('{input_text}', your_text)

# 替换其他占位符
for key in ['instructions', 'analysis_dimensions', 'output_format']:
    if key in template:
        placeholder = '{' + key + '}'
        prompt = prompt.replace(placeholder, template[key])

# 调用LLM
result = await handler.generate_text(prompt=prompt, model=model)
```

## 模板选择建议

| 场景 | 推荐模板 | 理由 |
|------|---------|------|
| 文学作品风格迁移 | `creative_style_extraction` | 需要精准捕捉复杂的文学风格 |
| 商业文案风格迁移 | `quick_style_extraction` | 快速提取核心风格特征 |
| 长篇小说风格分析 | `creative_style_extraction` | 需要详细的风格指导 |
| 社交媒体文本 | `quick_style_extraction` | 文本短小，快速分析即可 |
| 专业论文风格 | `creative_style_extraction` | 需要精确的学术风格特征 |
| 日常对话风格 | `quick_style_extraction` | 风格简单，快速提取 |

## 模板优化建议

### 提高风格提取质量

1. **文本长度**：
   - 深度模板：建议源文本 >500字
   - 快速模板：建议源文本 100-500字
   - 过短文本可能导致风格特征不明显

2. **文本质量**：
   - 选择风格鲜明、特征明显的文本
   - 避免混合多种风格的文本
   - 确保文本完整，不要截断

3. **模型选择**：
   - 推荐使用大参数量模型（>30B）
   - 文学理解能力强的模型效果更好
   - 如：GPT-4、Claude、Deepseek等

### 自定义模板

如需创建自定义模板，参考现有模板结构：

```yaml
meta:
  name: "your_template_name"
  description: "模板描述"
  version: "1.0.0"
  tags: ["标签1", "标签2"]
  variables: ["input_text"]

instructions: |
  分析指导说明...

analysis_dimensions: |
  分析维度说明...

output_format: |
  输出格式说明...

full_prompt_template: |
  {instructions}
  {analysis_dimensions}
  --- 待分析文本 ---
  {input_text}
  --- 文本结束 ---
  {output_format}
```

## 常见问题

### Q1: 为什么风格提取结果不准确？

**可能原因**：
- 源文本太短，风格特征不明显
- 源文本风格混杂，没有统一风格
- 模型能力不足，无法理解复杂风格
- 模板选择不当（短文本用了深度模板）

**解决方案**：
- 增加源文本长度（至少200字）
- 选择风格统一的文本片段
- 更换更强大的模型
- 根据文本长度选择合适模板

### Q2: 风格迁移后的文本不像原文风格？

**可能原因**：
- 风格指南提取不完整
- 新主题与原文风格冲突
- 模型理解风格指南有偏差

**解决方案**：
- 使用深度模板重新提取风格
- 调整新主题，使其与原风格兼容
- 在风格指南中强调核心特征
- 多次尝试，选择最佳结果

### Q3: 如何提高风格迁移的一致性？

**建议**：
1. 使用同一模型进行风格提取和迁移
2. 在风格指南中明确"必须遵循"的特征
3. 对长文本进行分段迁移，保持风格一致
4. 使用较低的temperature参数（0.3-0.5）

## 技术细节

### 模板变量替换

模板中的变量会在运行时被替换：

- `{input_text}`: 待分析的源文本
- `{instructions}`: 分析指导说明
- `{analysis_dimensions}`: 分析维度详情
- `{output_format}`: 输出格式要求

### 模板加载流程

```
1. API接收风格迁移请求
   ↓
2. 加载指定模板（默认：creative_style_extraction）
   ↓
3. 替换模板变量，生成完整提示词
   ↓
4. 调用LLM提取风格指南
   ↓
5. 清理和格式化风格指南
   ↓
6. 使用风格指南进行内容生成
```

### 性能优化

- 模板文件在首次加载后会被缓存
- 风格指南可以被重复使用，无需每次重新提取
- 对于相同源文本，可以缓存风格指南

## 更新日志

### v2.0.0 (2024-12-30)
- ✨ 新增深度创作风格提取模板
- ✨ 新增快速风格提取模板
- 🔧 优化风格提取API，支持模板选择
- 📝 完善文档和使用示例

### v1.0.0 (2024-12-01)
- 🎉 初始版本，基础风格提取功能

## 贡献指南

欢迎贡献新的风格提取模板！

**提交要求**：
1. 遵循现有模板的YAML格式
2. 提供详细的使用说明和示例
3. 测试模板在不同文本类型上的效果
4. 更新本文档，添加新模板说明

**提交流程**：
```bash
# 1. 创建新分支
git checkout -b feature/new-style-template

# 2. 添加模板文件
# config/prompt_templates/your_template.yaml

# 3. 更新文档
# doc/STYLE_EXTRACTION_TEMPLATES.md

# 4. 提交更改
git add .
git commit -m "feat(templates): add new style extraction template"

# 5. 推送并创建PR
git push origin feature/new-style-template
```

## 相关文档

- [风格迁移API文档](./api_streaming_guide.md)
- [项目结构说明](./PROJECT_STRUCTURE.md)
- [开发日志](./development_log.md)

## 联系方式

如有问题或建议，请通过以下方式联系：

- 提交Issue：[GitHub Issues](https://github.com/your-repo/issues)
- 邮件：support@qianlv.ai
- 文档反馈：在本文档中添加评论

---

**最后更新**：2024-12-30  
**维护者**：千虑AI文本分析团队
