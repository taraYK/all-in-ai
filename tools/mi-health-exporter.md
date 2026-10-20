# mi-health-exporter

项目地址：https://github.com/LilZeeCN/mi-health-exporter （MIT 开源，个人开发的非官方工具，与小米无关）

## 它是什么

把**小米运动健康** App 云端的数据同步到自己电脑上（SQLite 数据库 + CSV 表格），然后你可以把 CSV 交给 AI 做趋势分析。

能导出 8 类数据：心率、睡眠、步数、血氧、压力、体重、耳机记录、运动记录。

## 有什么用

- 把手环数据拿到自己手里，不被 App 界面限制。
- 配合 AI 做睡眠、步数、心率的按天趋势复盘。
- 作者修正了一个常见坑：时间戳被多算 16 小时（例如上午 09:05 的心率被错放到次日凌晨 01:05）。CSV 统一输出北京时间。

## 怎么用

需要 Python 3.9 以上，不用 `pip install` 任何东西。作者只在 macOS 上测过，Windows / Linux 未实测。

1. 下载并首次登录（登录成功后自动同步最近 7 天）：

```bash
git clone https://github.com/LilZeeCN/mi-health-exporter.git
cd mi-health-exporter
python3 sync_cloud.py --login
```

2. 以后每次先让手环同步到手机 App，再在电脑上运行：

```bash
python3 sync_cloud.py            # 最近 7 天
python3 sync_cloud.py --days 30  # 最近 30 天
```

3. 结果在 `analysis/` 文件夹里，比如 `sleep_daily.csv`、`steps_daily.csv`、`hr_raw.csv`。

4. 把愿意分享的 CSV 交给 AI，可以这样问：

> 读取最近一周的睡眠与步数 CSV，给出按天趋势。先检查时区、缺测和字段含义；把原始记录、推断和无法判断的部分分开，不作医学诊断。

## 注意事项

- 登录用小米账号密码；密码不保存，但**会话凭证以明文存在本地 `xiaomi_token.json`**，别上传、别分享。
- 账号要求验证码或二次验证时，命令行登录可能失败。
- 每日汇总每类最多取 100 条，`--days 365` 不保证拿到完整一年。
- 只适用于小米运动健康，不是 Zepp Life / 小米运动的通用导出器。
- 导出的健康数据属于隐私，**不要放进公开仓库**；交给外部 AI 前自己决定分享多少。
- 手环数据只能辅助复盘，不能用来诊断疾病。
