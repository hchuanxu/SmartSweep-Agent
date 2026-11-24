# 智扫通 SmartSweep Agent

面向扫地机器人的智能客服，支持选购咨询、故障排查、维护保养和月度使用报告。项目提供 FastAPI 网页与 Streamlit 两种交互入口。

## 核心实现

- **意图识别与工具调用**：关键词和正则加权识别五类意图，LangChain 中间件切换场景提示词，ReAct Agent 按需调用知识检索和使用记录工具。
- **混合检索**：jieba 与 BM25 匹配故障码等关键词，DashScope Embedding 与 ChromaDB 召回语义相近的文档，再通过 RRF 融合排序。
- **会话记忆**：保留最近 10 轮对话，长期记忆通过 ChromaDB 存储事实向量，通过 JSON 持久化摘要。事实支持去重和时间衰减。
- **流式问答**：FastAPI 提供同步问答与 SSE 流式接口。

## 本地运行

需要 Python 3.10+ 和 DashScope API Key。在项目根目录执行以下 PowerShell 命令。

```powershell
python -m pip install -r requirements.txt
$env:DASHSCOPE_API_KEY = "your-dashscope-api-key"
python -m rag.vector_store
python run_server.py
```

首次运行先导入知识文档。启动后访问 [问答页面](http://127.0.0.1:8001)。也可运行 `python -m streamlit run app.py`。

可以试问「扫地机器人报 E4，应该怎么排查？」或「小户型有宠物，选购时该关注哪些功能？」。月报功能从 `data/external/records.csv` 读取虚构演示记录，可根据界面使用的用户 ID 和查询月份调整样例。

## 评测

初始化知识库后运行 `python -m eval.rag_eval`。评测集包含 50 条带预期关键词的查询，脚本输出 Recall@K、HitRate@K、MRR、NDCG@K，以及基于关键词的生成评测指标。

## 项目结构

| 目录 | 用途 |
| --- | --- |
| `agent/` | 意图分类、工具、中间件和会话记忆 |
| `rag/` | 知识库导入、混合检索和回答生成 |
| `config/`、`prompts/` | 模型配置、分块参数和场景提示词 |
| `data/`、`eval/` | 知识文档、演示数据与评测 |
