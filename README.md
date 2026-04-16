# Reel Scout

Short-form video analysis CLI tool.  
Crawl YouTube Shorts / Instagram Reels / TikTok, transcribe audio, analyze visuals, and output structured JSON.

---

## Requirements

| 工具 | 版本 | 說明 |
|------|------|------|
| Python | 3.9+ | [python.org](https://www.python.org/downloads/) |
| ffmpeg | 任意 | yt-dlp 下載影片後處理音訊需要 |
| yt-dlp | 自動安裝 | pip 會自動拉取 |

> **ffmpeg 安裝（Windows）**  
> `winget install Gyan.FFmpeg` 或到 [ffmpeg.org](https://ffmpeg.org/download.html) 下載，解壓後把 `bin/` 加進 PATH。

---

## 安裝

### Step 1：建立虛擬環境（強烈建議）

```powershell
# 在專案根目錄執行
python -m venv .venv
```

### Step 2：啟動虛擬環境

```powershell
# Windows PowerShell（路徑有空格時要加引號）
& ".venv\Scripts\Activate.ps1"

# Windows CMD
.venv\Scripts\activate.bat

# macOS / Linux
source .venv/bin/activate
```

啟動成功後，命令列前面會出現 `(.venv)`。

### Step 3：安裝套件

```powershell
# 基本安裝
pip install -e .

# 含 Whisper 語音轉文字（建議安裝）
pip install -e ".[whisper]"
```

> ⚠️ **Windows 注意**：不要跳過虛擬環境直接 `pip install -e .`，否則腳本會裝在系統 PATH 以外的地方，導致 `reel-scout` 指令找不到。

---

## 使用方式

> 每次開新的終端機視窗，都要先重新啟動虛擬環境（Step 2）。

### 爬取單一影片

```powershell
reel-scout crawl "https://www.youtube.com/watch?v=xxxxx"
reel-scout crawl "https://youtube.com/shorts/xxxxx"
reel-scout crawl "https://www.tiktok.com/@user/video/xxxxx"
```

### 完整分析（爬取 + 轉錄 + 視覺分析）

```powershell
reel-scout analyze "https://youtube.com/shorts/xxxxx"

# 批次處理（從文字檔讀取 URL 清單，一行一個）
reel-scout analyze --file urls.txt

# 跳過視覺分析（沒有 VLM 時用）
reel-scout analyze --file urls.txt --skip-vision
```

### 查看已存影片

```powershell
# 列出所有已分析的影片
reel-scout list

# 顯示單一影片的完整分析結果
reel-scout show <video_id>
```

### 匯出資料

```powershell
# 匯出為 JSON，存到 ./export 資料夾
reel-scout export --format json -o ./export
```

### 設定管理

```powershell
# 檢查目前設定（API key、路徑等）
reel-scout config check
```

---

## 不用虛擬環境的替代方式

如果不想每次啟動 venv，可以用 `python -m` 直接執行：

```powershell
python -m reel_scout.cli crawl "https://www.youtube.com/watch?v=xxxxx"
```

---

## MCP Server（Claude Code 整合）

```powershell
reel-scout-mcp   # stdio transport，供 Claude Code 呼叫
```

設定方式請參考 [`docs/task-a1-mcp-handover.md`](docs/task-a1-mcp-handover.md)。

---

## 環境變數設定

複製範例檔後填入你的 API Key：

```powershell
copy .env.example .env
```

編輯 `.env`，至少填：

```
OPENAI_API_KEY=sk-...       # 視覺分析 / LLM
# 或
OLLAMA_BASE_URL=http://localhost:11434  # 本地 LLM
```

完整設定說明見 `.env.example`。

---

## 常見問題

**Q：`reel-scout` 指令找不到**  
A：虛擬環境沒有啟動。執行 `& ".venv\Scripts\Activate.ps1"` 後再試。

**Q：`Activate.ps1` 執行被封鎖**  
A：PowerShell 執行政策限制。以系統管理員執行：
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**Q：ffmpeg not found**  
A：ffmpeg 沒裝或不在 PATH。用 `winget install Gyan.FFmpeg` 安裝後重開終端機。

**Q：影片下載失敗**  
A：yt-dlp 版本可能過舊。執行 `pip install -U yt-dlp` 更新。

---

## 開發 / 測試

```powershell
# 安裝開發依賴
pip install -e ".[dev]"

# 跑測試
pytest -v
```

架構說明與任務文件見 [`docs/`](docs/) 資料夾。
