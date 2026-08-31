# 弥光 · 塔罗抽牌原型

一个无需构建工具、直接在浏览器运行的交互原型。

## 运行

```bash
cd tarot-studio
python3 server.py
```

打开 `http://localhost:4173`。

## 已实现

- 问题输入、手动分类及关键词自动分类
- 按问题匹配 3/4/5 张牌阵
- 三轮定时洗牌动画和默念提示
- 完整 78 张标准塔罗牌（22 张大阿尔卡那＋56 张小阿尔卡那）扇形铺牌、限量选牌与顺序标记
- 正逆位、按牌阵摆放和牌位展示
- 塔罗、梅花易数、心理支持三段式解析
- 韦特塔罗深度解析：牌义纪律、正逆位机制、元素分布、牌间关系、叙事弧和具体行动
- 梅花易数仅以抽牌序号和抽牌时间起卦，完整计算本卦、互卦、变卦、动爻和体用五行，不读取塔罗牌义
- 抽牌结果和解析状态保存到浏览器 `localStorage`
- 结果页按需生成局域网分享短链接，同一 Wi-Fi 内可查看相同牌面与解析，7 天后自动失效
- 桌面端记录侧栏和移动端抽屉式记录

解析正文现在由本机已登录的 Codex 直接生成：逐牌回扣用户问题、分析牌间关系并给出具体行动。梅花易数在同一结构化响应中保持独立，只读取抽牌序号与时间；心理支持再综合两份结果。历史记录会保存 Codex 原文，不会重新套用本地模板。

## 局域网分享

在结果页点击“分享抽牌结果”，确认后会生成 7 天有效的短链接。配置公网分享服务后，朋友无需连接同一 Wi-Fi，也不需要分享者持续运行预览服务；未配置公网服务时会自动降级为原来的局域网链接。

分享内容包含当前问题、牌面、正逆位、牌阵和已经生成的解析。公网写入仅由本机服务使用私密写入密钥发起，查看页不会暴露本机 Codex、图片识别或解析接口。短码随机生成，7 天后自动失效；分享不会自动开启，只有用户确认后才会写入。

## 牌阵图片视觉接口

提问可以留空。用户上传 JPG、PNG 或 WebP 后，主按钮仍显示“进入仪式”；点击后会跳过洗牌、选牌和识别确认页，由默认 Codex 读取图片并直接展示完整解析。

本地预览默认使用同源接口 `/api/vision-reading`。 `server.py` 会调用当前电脑已登录的 Codex CLI，以图片输入和 JSON Schema 结构化输出识别牌面，不需要在浏览器填写或暴露 API Key。

如需切换为自己的生产服务，可在页面加载前设置：

```js
window.TAROT_VISION_ENDPOINT = "/api/vision-reading";
```

产品固定使用 Codex 作为默认解析 Agent，不向用户展示模型或 API 选择。前端会向内部识别服务发送可选 `question`、Base64 图片及 Rider–Waite–Smith 识别要求。接口返回示例：

```json
{
  "spreadKey": "general",
  "confidence": 0.92,
  "cards": [
    { "id": 0, "name": "愚者", "reversed": false, "position": "当前局面" }
  ]
}
```

自定义服务的 API Key 必须保存在服务端。若仍用 `python3 -m http.server` 启动纯静态预览，图片接口不会存在；请使用 `python3 server.py`。

## 方法参考

- [daman-ovo-0404/tarot-skill](https://github.com/daman-ovo-0404/tarot-skill)：韦特塔罗牌义纪律、牌间关系与反巴纳姆方法。
- [dreamhunter2333/chatgpt-tarot-divination](https://github.com/dreamhunter2333/chatgpt-tarot-divination)：两数起卦交互方式；本项目补充实现了本卦、互卦、变卦及体用五行计算。

## 牌面图像

- 用户提供的 [塔罗网小阿尔卡纳页面](https://w.taluo.com/xa.html) 作为牌面视觉参考。该页面图片带网站水印，未直接复制到项目。
- 产品实际显示 [Wikimedia Commons 的78张 Pam-A Rider–Waite–Smith 扫描图](https://commons.wikimedia.org/wiki/Category:Rider-Waite-Smith_tarot_deck_(TaionWC))。原始 Rider–Waite–Smith 牌面为公共领域；具体文件信息以各图片描述页为准。
