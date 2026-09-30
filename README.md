# glados-checkin

GLaDOS（glados.cloud / glados.rocks / glados.network）每日自动签到脚本，使用 GitHub Actions 实现。

---
**2026.9.25**：
- GLaDOS账号的Cookie已改。请重新获取**完整的Cookie**后，更新 GitHub Secrets （详见：2.0 设置 GitHub Secrets），再点击仓库的 Actions 重新运行一次 workflows 工作流，显示“✅”即可。

**注意⚠️**(2026.4.20)：
- 2026年4月20日收到glados的邮件，原账号已经转移到新的平台[Railgun](https://railgun.info)，继承了之前账号的计划和剩余天数，但没有继承之前的点数
- 如需要继续自动签到，请移步到新的仓库[railgun_checkin](https://github.com/Zirui8/railgun_checkin)

**V2026.2.4**(2026.2.4)：
- 支持**多个账号**同时签到
- 失败会让 Actions 失败并发送绑定GitHub账号的邮箱提醒，重复签到视为正常，不触发失败邮件

**参考项目**：本仓库参考/部分 fork 自 [actions-integration/checkin](https://github.com/actions-integration/checkin)，本仓库改为纯 Python 实现。

---

## 使用说明

### 1.0 更新 Cookie
GLaDOS 签到脚本使用 **Cookie** 进行登录。Cookie 和 网址Api 可能需要定期更新，否则可能会签到失败。  

更新方法：  
1. 登录 [GLaDOS 官网](https://glados.cloud/)，进入[签到面板](https://glados.cloud/console/checkin) 
2. 按 `F12` 打开浏览器开发者工具 → 切到 **网络(Network)**
3. 刷新一下网页或点击一次“签到”按钮，浏览器开发者工具**Name**(名称)中会出现`checkin`字样，点击`checkin`
4. 在 **Headers** 中下滑找到 **Cookies** ，找到以下字段并完整复制：

```txt
koa:sess=xxx; koa:sess.sig=xxx
```

---

**多账号**：每个账号重复以上步骤，分别获取不同的 Cookie 字符串。

### 2.0 设置 GitHub Secrets

1. 登录自己的 [GitHub 账号](https://github.com/login)，点击本项目右上方 `Fork` ，再点击右下方 `Create frok` 复刻到自己仓库中
2. 打开你的 GitHub 仓库 → **Settings** → **Secrets and variables** → **Actions**
3. 点击 **New repository secret**
4. **Name**(名称)填：`GLADOS`（必须全大写）
5. **Secret**(密钥)填入所有账号的 Cookie，多账号用换行分隔
6. **单账号示例**：

```txt
koa:sess=xxx; koa:sess.sig=xxx
```

7. **多账号示例**：

```txt
koa:sess=账号1; koa:sess.sig=账号1;
koa:sess=账号2; koa:sess.sig=账号2;
koa:sess=账号3; koa:sess.sig=账号3;
```


---

### 3.0 查看签到日志

1. 打开仓库首页 → 点击 **Actions** 标签页  
2. 在左侧选择 `GLaDOS Checkin` workflow 工作流，运行一次workflows 工作流检测，显示“✅”表示成功
3. 点击最新一次运行记录（按日期排序）  
4. 点击 **Run checkin script** 步骤，可以查看所有账号的输出日志  
   - 如果签到成功，会显示类似 `Checkin! Got X Points` 的提示  
   - 如果返回 `"Today's observation logged. Return tomorrow for more points."` 表示今天已经签过到  
   - 如果是 Cookie 过期或其他错误，会有对应的报错信息，并会发送报错信息至绑定GitHub账号的邮箱

---

### 4.0 修改签到时间

当前自动签到时间约为 **每天东八区时间 00:30**（UTC 时间 16:30）。  

如果需要调整时间，可以修改 `.github/workflows/xxx.yml` 中的：

```yaml
schedule:
  - cron: '30 16 * * *'  # UTC 时间 16:30
```

**注意**：GitHub Actions 使用 UTC 时间，中国用户需要根据自己需求换算成东八区时间（+8 小时）。
