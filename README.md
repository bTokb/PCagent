1. 克隆项目
git clone <repository_url>
cd super_biz_agent_py

使用 pip
python -m venv .venv
.venv\Scripts\activate
pip install -e .

 3. 编辑配置文件
 使用记事本或其他编辑器打开 .env 文件，填入你的 DASHSCOPE_API_KEY
notepad .env

 4. 启动 Docker Desktop
确保 Docker Desktop 已安装并正在运行

 5. 启动 Milvus 向量数据库（Docker Compose）
docker compose -f vector-database.yml up -d

 6. 等待 Milvus 启动完成（约 5-10 秒）
timeout /t 10

7. 启动 MCP 服务
 启动 CLS 日志查询服务（新开一个 PowerShell 窗口）
python mcp_servers/cls_server.py

启动 Monitor 监控服务（新开一个 PowerShell 窗口）
python mcp_servers/monitor_server.py

8. 启动 FastAPI 主服务（新开一个 PowerShell 窗口）
注意：日志会自动输出到 logs\app_YYYY-MM-DD.log
python -m uvicorn app.main:app --host 0.0.0.0 --port 9900

 9. 上传文档到向量库（新开一个 PowerShell 窗口）
 等待服务启动完成后执行
timeout /t 5
python -c "import requests, os, time; [requests.post('http://localhost:9900/api/upload', files={'file': open(f'aiops-docs/{f}', 'rb')}) or time.sleep(1) for f in os.listdir('aiops-docs') if f.endswith('.md')]"
```



⚙️ 配置说明

通过 `.env` 文件配置：

```bash
 阿里云LLM DashScope 配置（必填）
 秘钥管理： https://bailian.console.aliyun.com/cn-beijing/?spm=5176.29597918.J_SEsSjsNv72yRuRFS2VknO.2.61ac133ccTVQLw&tab=demohouse#/api-key
DASHSCOPE_API_KEY=your-api-key （配置你自己的秘钥）
DASHSCOPE_API_BASE=https://dashscope.aliyuncs.com/compatible-mode/v1  # 不配置则默认会使用新加坡站点
DASHSCOPE_MODEL=qwen-max

 Milvus 配置
MILVUS_HOST=localhost
MILVUS_PORT=19530

 RAG 配置
RAG_TOP_K=3
CHUNK_MAX_SIZE=800
CHUNK_OVERLAP=100
```

 访问服务
- **Web 界面**: http://localhost:9900