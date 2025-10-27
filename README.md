# HPL
# 🧮 HPL CPU Memory Benchmark (Ubuntu 22.04)

這個專案提供一個簡潔的 Docker 環境，用於在 **CPU 上執行 HPL（High Performance Linpack）** 測試，  
主要測量 **CPU 浮點效能 (GFLOPS)** 與 **記憶體運算表現**。  
不包含 GPU，適合做伺服器或 HPC 節點的 CPU 性能基準測試。

---

## 📂 專案結構

hpl-cpu-test/
├── Dockerfile # 建置 CPU 版 HPL 映像
├── Make.Linux_CPU # HPL 編譯設定
├── HPL.dat # 測試參數（矩陣大小、區塊大小、處理網格）
├── entrypoint.sh # 自動執行腳本
└── README.md # 專案說明

yaml
コードをコピーする

---

## 🧱 1. 建置 Docker 映像

在專案根目錄執行：

```bash
docker build -t hpl-cpu-test .
這會：

安裝 OpenBLAS + MPI

下載並編譯 HPL (v2.3)

複製設定檔與啟動腳本

▶️ 2. 執行測試
bash
コードをコピーする
docker run --rm -v $(pwd)/logs:/workspace/logs hpl-cpu-test
執行後：

會在 /workspace 內運行 HPL

自動產生測試 log 檔案，例如：

bash
コードをコピーする
/workspace/hpl_20251024_1530.log
結果會包含：

問題尺寸 (N)

區塊大小 (NB)

使用核心數

實際執行時間

GFLOPS（每秒浮點運算量）

⚙️ 3. 調整測試參數
你可以編輯 HPL.dat 來控制記憶體與負載：

參數	說明	建議值
Ns	矩陣大小（N）	越大越吃記憶體，例如 20000～80000
NBs	區塊大小	一般用 128 或 256
Ps / Qs	處理節點佈局	可依核心數調整，例如 2x2 或 4x4

🧩 4. Log 分析
打開 log 可看到類似：

css
コードをコピーする
T/V                N    NB     P     Q               Time                 Gflops
WR11R2R4      20000   256     2     2             550.00              9.20
表示：

測試矩陣大小為 20000

使用 4 核 (2x2)

執行時間 550 秒

性能為 9.20 GFLOPS

💡 5. 常見調整建議
需求	方法
想測更大記憶體	增加 Ns
想跑更快	調整 Ps、Qs 讓佈局更均衡
想輸出成結果檔	在 entrypoint.sh 內加上 tee logs/output.log
想跑多次取平均	用 bash 迴圈執行多次 docker run

🧰 6. 系統需求
Ubuntu 22.04（或任意支援 Docker 的 Linux）

CPU：x86_64 架構，建議 4 核以上

記憶體：至少 4GB（建議 16GB 以上）

約 1GB 磁碟空間

