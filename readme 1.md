### 🔗 **Google AI 工具箱 (收藏夹)**

- **Google 账号管理**: https://accounts.google.com
- **Gemini (对话/写作/分析)**: https://gemini.google.com
- **NotebookLM (文档分析/PPT 策划)**: https://notebooklm.google.com
- **AI Studio (模型调试)**: https://aistudio.google.com
- **ImageFX (专业绘画实验室)**: https://aitestkitchen.withgoogle.com/tools/image-fx

---

# 📘 Google AI 深度实操指南 (完整版)

## 准备工作

翻墙软件：`Clash X` or `sakura cat`

sakuraCat 安装包地址：https://sakura-cat1.com/knowledge

## 🏁 第一章：拥有通行证 (Google 账号)

这是所有 AI 服务的起点。如果你已经有 Google 账号，可以跳过这一步。

**操作步骤：**

1.  **访问入口**：在浏览器中输入 `accounts.google.com/signin`。
2.  **创建/登录**：
    - 如果没有账号，点击左下角的“创建账号 (Create account)”，选择“个人用途 (For my personal use)”。
    - 如果有账号，直接输入邮箱和密码登录。
3.  **关键设置**：
    - **语言设置**：建议在 `Personal info` -> `General preferences for the web` 中将语言设置为 `English (United States)`。虽然中文支持很好，但英文界面通常能最早解锁最新功能（如 Imagen 3 的绘图功能）。

---

## 🤖 第二章：Gemini —— 你的全能 AI 副驾驶

这是 Google 的核心 AI 产品，适合日常问答、写作和多模态分析。

**如何进入：** `gemini.google.com`

**界面详解与实操：**

1.  **主对话框 (Prompt Box)**：
    - 这是你和 AI 交流的核心区域。
    - **技巧**：不要只说“写个文章”。试试“_作为一名资深旅游博主，请帮我写一篇关于京都秋季旅游的 500 字攻略，语气要轻松活泼。_”
2.  **多模态功能 (传图/传录音)**：
    - 点击输入框旁边的 `+` 号或图片图标。
    - **实操任务**：上传一张冰箱里剩余食材的照片，问 Gemini：“_根据这张照片里的食材，给我推荐三个简单的食谱。_”
3.  **检查回答 (Check Response)**：
    - Gemini 生成回答后，底部通常会有三个图标：`点赞`、`点踩`、`修改回答 (Modify response)`。
    - 你可以点击 `Modify response` 让它把回答变得“更短 (Shorter)”、“更长 (Longer)”或“更专业 (Professional)”。

---

## 📚 第三章：NotebookLM —— 你的智能知识库与 PPT 策划师

NotebookLM 是处理长文档的神器。它不仅能读懂书，还能帮你把资料转化为 PPT 的骨架。

**🎯 目标任务：利用一份 PDF 资料，快速生成一份 10 页的 PPT 策划案。**

**操作步骤：**

1.  **进入基地**：

    - 访问 `notebooklm.google.com`。
    - 点击大大的方块 `+ New Notebook` 创建一个新笔记本。

2.  **投喂资料 (Source)**：

    - 进入笔记本后，左侧栏是“Sources”。
    - 点击 `+` 号，选择 `Upload source`。
    - **实操**：上传一份你手边的 PDF（比如行业报告、论文或电子书）。
    -

3.  **调教 AI 生成 PPT 大纲**：

    - 资料上传成功后，NotebookLM 已经读完了整本书。
    - 在底部的对话框中输入以下**超级指令**（可以直接复制）：
      > “请基于我上传的这份文档，帮我规划一份 10 页的 PowerPoint 演示文稿大纲。要求：
      >
      > 1. 每一页都要有清晰的‘标题’。
      > 2. 每一页列出 3-4 个核心‘子弹点’（Bullet points）。
      > 3. 为每一页写一段 100 字左右的‘演讲备注’。
      > 4. 重点突出文档中的核心数据和结论。”

4.  **生成与优化**：

    - 几秒钟后，你会得到一份结构极其严密的 PPT 内容。
    - **技巧**：如果你觉得某页太简单，可以继续追问：“_第 3 页的内容太单薄了，请根据文档里的[某章节]再补充一些细节。_”

5.  **保存成果**：
    - 鼠标悬停在回答上，点击“图钉”图标 📌，将其保存为笔记 (Note)。
    - 现在，你只需要打开 PowerPoint，把这些生成好的标题和文字复制进去，配上图，一份专业级 PPT 就完成了 80% 的工作。

---

## 🎨 第四章：ImageFX / Imagen 3 —— 你的灵魂画师

这里就是你提到的“Nano Banana”应用场景（实为 **Imagen 3** 模型）。我们来生成一张高质量图片。

**🎯 目标任务：生成一张电影质感的高清图片。**

### **方法一：在 Gemini 中快速生成 (适合新手)**

1.  **回到 Gemini** (`gemini.google.com`)。
2.  **输入绘画指令**：
    - Gemini 目前对**英文提示词** (Prompt) 的支持最好。
    - **公式**：**主体 + 动作/环境 + 艺术风格 + 灯光/氛围**
    - **实操案例**：
      > "Generate a photorealistic image of a futuristic city with vertical gardens on skyscrapers, sunset lighting, cinematic shot, highly detailed, 8k resolution."
      > _(生成一张未来城市的逼真照片，摩天大楼上有垂直花园，日落光线，电影镜头感，高细节，8k 分辨率)_
3.  **查看结果**：Gemini 会直接在对话框里吐出 4 张图。点击图片可以下载全尺寸版本。

### **方法二：使用 ImageFX (进阶玩法，效果更炸裂)**

如果你想要更极致的控制力，我们要去 Google 的“AI 厨房”。

1.  **进入实验室**：
    - 访问 `aitestkitchen.withgoogle.com/tools/image-fx` 并登录。
2.  **界面操作**：
    - ImageFX 的独特之处在于**“可变参数芯片” (Chips)**。当你输入提示词后，它会把关键词高亮显示。你可以直接点击这些词来切换（比如把 "Sunset" 点击切换成 "Midnight"），无需重写整个句子。
3.  **实操练习**：
    - 在输入框输入：
      > "A cute robot barista making coffee in a cozy wooden cafe, warm lighting, digital art style, soft focus background."
      > _(一个可爱的机器人咖啡师在温馨的木质咖啡馆里做咖啡，暖光，数字艺术风格，背景柔焦)_
    - 点击 `Generate`。
    -
4.  **微调**：
    - 如果不满意，尝试点击关键词下方的下拉菜单，快速更换风格（比如从 `Digital Art` 换成 `Oil Painting` 油画）。

---

这就完成了！从账号设置，到文档分析，再到创意绘图，你现在已经拥有了一套完整的 AI 工作流。

**接下来，你打算先用 NotebookLM 处理哪个文档，还是先去 ImageFX 画一张图练练手？**
