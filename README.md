# 宣传站（site/）

ESP32-C3 信息显示器的产品宣传站点。纯静态、零构建，**GitHub Pages 直接托管**，默认 HTTPS。

> 主视觉与 5 套主题截图均使用真实收盘数据渲染（来自 `document/xianyu/`，日期 2026-09-02），不虚构、不虚假宣传。

## ⚠️ 价格铁律（改站前必读）

**站点上不允许出现任何价格数字。**

产品售出后每台都会附激活码（8 只全功能），不存在"未激活版 / 基础版 / 旗舰版"这类分档。
99 / 199 只是**私域一对一谈判时的报价区间**——公开标价会同时造成两个问题：

1. **锁死自己的议价空间**：明面挂了价，私聊就很难再往上顶；
2. **合规风险**：公域平台同 SKU 对不同人报不同价，涉价格欺诈。

因此站点的做法是：**只引导"私聊询价"**，并在询价区让买家自曝三件事——自己玩还是公司用 / 要几台 / 要不要壳——你据此定档报价。

相关文案在 `index.html` 的 `#buy`（怎么买）区块与页脚。改动时请确保不引入 `99`、`199`、`¥`、基础版、旗舰版、未激活、试用徽章等字样。

## 目录结构

```
site/
├── index.html              # 宣传主页（单页，自包含）
├── flash/
│   ├── index.html          # 在线烧录（Chrome 一键刷固件）
│   ├── manifest.json       # 烧录清单（version 已与固件同步为 1.0.2）
│   ├── merged-firmware.bin # 整机固件（3.4 MB，bootloader+app+font+otadata）
│   └── esp-web-tools/      # ESP Web Tools 资源
└── assets/                 # 主题实拍图（来自 document/xianyu/）
    ├── cover.png
    ├── theme1_list.png
    ├── theme2_big.png
    ├── theme3_zoom.png
    ├── theme4_hot.png
    └── theme5_detail.png
```

## 本地预览

任一目录起静态服务器即可：

```bash
cd site
python -m http.server 8000
# 浏览器打开 http://localhost:8000
```

> ⚠️ `flash/index.html` 必须通过 `https://` 或 `http://localhost` 访问才可烧录。
> 直接双击用 `file://` 打开会被 Web Serial API 拒绝。

## GitHub Pages 部署

### 方案 A：作为独立仓库（推荐）

把整个 `site/` 目录里的内容 push 到一个新仓库的**根目录**，再：

1. GitHub → 该仓库 → Settings → Pages
2. Source 选 `Deploy from a branch`
3. Branch 选 `main`（或 master），目录选 `/`（root）
4. 保存，几分钟后得到 `https://<用户名>.github.io/<仓库名>/`

### 方案 B：作为主仓库的 `/docs` 目录

把 `site/` 里的内容（不含 `site/` 外壳）复制到主仓库 `docs/`，然后：

1. Settings → Pages → Branch 选 `main`，目录选 `/docs`
2. 访问 `https://<用户名>.github.io/<主仓库名>/`

## 发布前需要替换的占位

| 占位 | 在哪 | 改成什么 |
|---|---|---|
| 「仓库地址占位」 | 页脚 footer | 你的 GitHub 仓库 URL |
| 「微信 / 闲鱼（请替换为你的联系方式）」 | `#buy` 区块的联系方式行 + 页脚 | 你的微信号 / 闲鱼商品页 |

> 联系方式建议放**微信二维码图片**或闲鱼链接，比文字微信号转化更高；纯文字微信号容易被平台判定导流而限流。

## 同步烧录页与固件

当主仓库 `flasher/` 有更新（重新打包了 merged-firmware.bin）时，同步到站点：

```bash
# 在项目根目录
cp flasher/index.html flasher/manifest.json flasher/merged-firmware.bin site/flash/
cp -r flasher/esp-web-tools/. site/flash/esp-web-tools/
```

> 版本号提示：`flasher/manifest.json` 的 `version` 字段需与 `components/common/include/common/app_types.h` 的 `FIRMWARE_VERSION` 宏保持一致；当前站点已修正为 1.0.2，**主目录 `flasher/manifest.json` 也建议同步改**，否则两份 manifest 会漂移。

## 重新生成主题图

主题图来源于 `document/xianyu/render_themes.py`（真实数据抓取 + 渲染）。需要换日期时：

```bash
# 修改 render_themes.py 顶部的 DATE / ITEMS / HOT / MAOTAI_KL 等数据源
python document/xianyu/render_themes.py
# 把新生成的图复制过来
cp document/xianyu/theme*.png document/xianyu/cover.png site/assets/
```

⚠️ **铁律**：主题图必须使用**当日真实收盘数据**渲染，禁止虚构示例数据。改了日期却没重拉真实数据，会被买家按日期核对抓包（虚假宣传）。改图后记得同步 `index.html` 里两处日期标注（Hero 图注 + 主题区块末尾）。

## 技术备注

- **零依赖**：纯 HTML + 内联 CSS（无 framework、无构建）。任意编辑器可直接改。
- **GitHub Pages 限制**：单仓库推荐 ≤1 GB；当前站点 ≈ 4.3 MB（主要是 merged-firmware.bin），远低于限额。
- **烧录依赖**：浏览器需支持 Web Serial API（Chrome / Edge / Opera 全平台）；Safari / Firefox / iOS 不支持。
- **免责声明**：已在页脚注明"数据仅供参考，不构成投资建议"，请勿删除。
