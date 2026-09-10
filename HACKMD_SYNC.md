# HackMD 與 GitHub 同步方式

建議把 **GitHub 當成主要來源**。在本機修改並 push 後，再由 HackMD 從 GitHub pull 最新版本，避免兩邊同時修改造成衝突。

## 建議的 HackMD 結構

在 HackMD 建立 `Azure Certification Notes` 資料夾，再建立 `AI-901` 子資料夾。每篇 HackMD note 對應 repository 中的一個 Markdown 檔案：

```text
Azure Certification Notes/
└── AI-901/
    ├── 00 Index
    ├── 01 Exam Guide
    ├── 02 Responsible AI
    ├── 03 AI Models and Workloads
    ├── 04 Microsoft Foundry
    ├── 05 Text and Language
    ├── 06 Speech
    ├── 07 Computer Vision
    ├── 08 Content Understanding
    └── 09 Legacy and Adjacent Topics
```

## 第一次連結

1. 在 GitHub 安裝並授權 [HackMD GitHub App](https://github.com/apps/hackmd-hub)，選擇這個 repository。
2. 在 HackMD 建立一篇空白 note。
3. 開啟 **Versions and GitHub Sync**。
4. 選擇 **Pull from GitHub**。
5. 選擇 repository、`main` branch，以及對應檔案，例如 `ai-901/knowledge/00_Index.md`。
6. 對需要公開的每篇知識筆記重複一次，再放進 HackMD 的 `AI-901` 資料夾。
7. 將 note 的閱讀權限設為公開；若希望出現在 HackMD 公開頁面，再使用 Publish。

## 日常更新流程

```text
本機修改 Markdown
→ git commit
→ git push origin main
→ HackMD 開啟對應 note
→ Versions and GitHub Sync
→ Pull from GitHub
```

HackMD 的官方 GitHub Sync 是逐篇 note 的 push／pull，不會在每次 GitHub push 後自動同步整個資料夾。若也在 HackMD 編輯，請先 **Push to GitHub**，回到本機後再 `git pull`，避免兩邊產生不同版本。

## 建議公開範圍

公開 `ai-901/knowledge/` 即可作為閱讀筆記；`mistakes/` 可視需要公開。`source/` 與 `raw notes/` 是保存用原始材料，不需要建立 HackMD note。

官方說明：[Sync a Note with GitHub](https://hackmd.io/s/link-with-github)
