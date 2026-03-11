# 实施计划：移除第三方联系方式和广告模块

## 项目信息
- **需求**：删除所有第三方联系方式（微信、Twitter/X、B站、QQ群），移除广告模块，保留更新通知但移除下载功能
- **方案**：完整清理（前后端兼容式）
- **预计时间**：3-4 小时

## 一、后端修改

### 1.1 停止返回 sponsor 字段
**文件**：`backend/services/preferences_service.go`

**修改位置**：L256-262 (CheckForUpdate 函数返回值)

**变更**：
- 移除返回数据中的 `"sponsor": respObj.Sponsor`
- 保留 `download_page` 以兼容旧客户端

**原因**：后端不再向前端提供广告数据

---

## 二、前端修改

### 2.1 移除社交媒体按钮（Ribbon.vue）

**文件**：`frontend/src/components/sidebar/Ribbon.vue`

**删除内容**：
1. **导入语句** (L16-19)：
   - `wechatUrl` 图片导入
   - `bilibiliUrl` 图片导入
   - `QRCode` 图标组件
   - `Twitter` 图标组件

2. **状态变量** (L44)：
   - `showWechat` ref

3. **事件处理函数** (L127-135)：
   - `openWechatOfficial()`
   - `openX()`

4. **模板代码** (L188-202)：
   - 微信公众号按钮（中文用户显示）
   - Twitter/X 按钮（非中文用户显示）

5. **微信弹窗** (L220-225)：
   - 整个 `n-modal` 组件

**保留**：
- GitHub 按钮
- 配置菜单
- 退出登录按钮（Web 版）

---

### 2.2 移除广告组件（ContentServerPane.vue）

**文件**：`frontend/src/components/content/ContentServerPane.vue`

**删除内容**：
1. **导入语句** (L2-8)：
   - `computed` from vue
   - `useThemeVars` from naive-ui
   - `BrowserOpenURL`
   - `find, includes, isEmpty` from lodash
   - `usePreferencesStore`

2. **状态和函数** (L10-68)：
   - `themeVars`
   - `prefStore`
   - `onOpenSponsor()`
   - `openBanner()`
   - `skipBanner()`
   - `sponsorAd` computed
   - `banner` computed

3. **模板代码**：
   - Banner 横幅警告 (L73-85)
   - Sponsor 广告链接 (L99-101)

4. **样式代码**：
   - `.banner` 样式
   - `.sponsor-ad` 样式
   - `.color-preset-item` 样式（未使用）
   - `position: relative` 容器定位

**保留**：
- 空状态展示
- "新建连接"按钮

---

### 2.3 调整更新通知逻辑（preferences.js）

**文件**：`frontend/src/stores/preferences.js`

**修改位置**：L490-585 (checkForUpdate 方法)

**变更**：

1. **移除导入** (L13)：
   - `BrowserOpenURL`

2. **调整数据解构** (L499-505)：
   - 移除 `download_page`
   - 移除 `sponsor`
   - 移除 `banner`
   - 保留 `version`, `latest`, `description`

3. **清理 localStorage** (L506-508)：
   ```javascript
   localStorage.removeItem('sponsor_ad')
   localStorage.removeItem('banner')
   localStorage.removeItem('banner_next_time')
   ```

4. **修改弹窗条件** (L509-512)：
   - 原条件：`compareVersion(latest, version) > 0 && !isEmpty(downUrl)`
   - 新条件：`!isEmpty(latest) && compareVersion(latest, version) > 0`
   - 不再依赖 `downUrl`

5. **移除下载按钮** (L543-558)：
   - 删除 "立即下载" NButton
   - 保留 "跳过此版本" 和 "稍后提醒" 按钮

6. **移除下载回调** (L563)：
   - 删除 `onPositiveClick: () => BrowserOpenURL(downUrl)`

---

### 2.4 更新多语言文案

**修改文件**（共 10 个语言文件）：
- `frontend/src/langs/en-us.json`
- `frontend/src/langs/zh-cn.json`
- `frontend/src/langs/zh-tw.json`
- `frontend/src/langs/ja-jp.json`
- `frontend/src/langs/ko-kr.json`
- `frontend/src/langs/fr-fr.json`
- `frontend/src/langs/es-es.json`
- `frontend/src/langs/pt-br.json`
- `frontend/src/langs/ru-ru.json`
- `frontend/src/langs/tr-tr.json`

**删除的键**：
- `ribbon.wechat_official`
- `ribbon.follow_x`
- `dialogue.upgrade.download_now`

**修改的键**：
- `dialogue.upgrade.new_version_tip`
  - 英文：`"New version {ver} available, download now?"` → `"New version {ver} available."`
  - 中文：`"新版本（{ver}），是否立即下载"` → `"发现新版本（{ver}）"`
  - 其他语言类似调整，移除下载提示

---

### 2.5 删除未使用的图标组件

**删除文件**：
1. `frontend/src/components/icons/QRCode.vue`
2. `frontend/src/components/icons/Twitter.vue`

**原因**：这两个图标仅用于社交媒体按钮，移除后无其他引用

---

### 2.6 删除图片资源

**删除文件**：
1. `frontend/src/assets/images/wechat_official.png`
2. `frontend/src/assets/images/bilibili_official.png`
3. `docs/images/wechat_official.png`
4. `docs/images/bilibili_official.png`
5. `docs/images/wechat_sponsor.jpg`

---

## 三、文档修改

### 3.1 清理 README 文件

**修改文件**（共 11 个）：
- `README.md`
- `README_zh.md`
- `README_tw.md`
- `README_ja.md`
- `README_ko.md`
- `README_fr.md`
- `README_es.md`
- `README_pt.md`
- `README_ru.md`
- `README_tr.md`

**删除内容**：

1. **徽章链接**（部分 README）：
   - Discord 徽章
   - Twitter/X 徽章

2. **"关于"章节中的社交内容**：
   - 微信公众号二维码
   - B站官方账号
   - QQ群信息（仅中文版）
   - 微信赞赏码
   - "Sponsor" / "赞助" 整个小节

3. **保留内容**：
   - "Thanks" / "感谢" 章节（服务商赞助）

---

## 四、验证清单

### 4.1 功能验证
- [ ] 应用启动正常，无控制台错误
- [ ] Ribbon 侧边栏仅显示：配置、GitHub、退出登录（Web版）
- [ ] 首页空状态无广告展示
- [ ] 启动时自动检查更新仍然触发
- [ ] 手动检查更新（配置菜单）仍然工作
- [ ] 更新通知弹窗正常显示（仅 2 个按钮：跳过、稍后）
- [ ] "跳过此版本" 功能正常
- [ ] localStorage 中无 `sponsor_ad`、`banner`、`banner_next_time` 残留

### 4.2 兼容性验证
- [ ] 旧客户端仍能从后端获取 `download_page`（向后兼容）
- [ ] 新客户端不依赖 `download_page` 即可弹出更新通知

### 4.3 代码质量
- [ ] 无未使用的导入语句
- [ ] 无未使用的变量或函数
- [ ] 无控制台警告或错误
- [ ] 编译通过，无 TypeScript/ESLint 错误

---

## 五、风险与注意事项

### 5.1 已知风险
1. **更新通知条件变更**：从依赖 `downUrl` 改为仅依赖版本比较，需确保远端 JSON 始终返回 `latest` 字段
2. **旧版本兼容**：保留后端 `download_page` 返回，避免破坏已发布的旧客户端

### 5.2 回滚策略
- 所有修改均为删除操作，可通过 Git 快速回滚
- 如需恢复某个功能，从 Git 历史中恢复对应文件即可

---

## 六、实施顺序

1. **后端修改**（5 分钟）
   - 修改 `preferences_service.go`

2. **前端核心修改**（60 分钟）
   - Ribbon.vue
   - ContentServerPane.vue
   - preferences.js

3. **多语言文案**（30 分钟）
   - 更新 10 个语言文件

4. **资源清理**（10 分钟）
   - 删除图标组件
   - 删除图片文件

5. **文档清理**（45 分钟）
   - 更新 11 个 README 文件

6. **测试验证**（60 分钟）
   - 功能回归测试
   - 兼容性验证

**总计**：约 3.5 小时

---

## 七、成功标准

✅ 应用内无任何第三方社交媒体链接或按钮
✅ 无广告内容展示（sponsor_ad、banner）
✅ 更新检查功能正常，能显示版本信息
✅ 更新通知无下载按钮，仅提示版本可用
✅ 应用正常编译运行，无错误
✅ README 文档已清理社交和赞助内容
