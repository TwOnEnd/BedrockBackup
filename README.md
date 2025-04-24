# 🗃️ BedrockBackup

**Minecraft基岩版服务器备份解决方案**

---

## 📦 功能概览

- **备份/恢复**：支持全量/差异备份，快速回滚存档
- **删除/查询**：删除和查询全量/差异备份
- **定时任务**：自动备份、自动清理
- **多模式触发**：定时备份或按固定间隔执行
- **安全防护**：启动/回滚操作前自动创建保险备份

---

## 💡 工作原理

`MCSL-T.exe` 基于 Windows API 管道通信，并挂载 `BedrockBackup.dll` 实现存档备份管理

---

## 🛠️ 安装与配置

### 1. 文件结构

```js
MCSL-T/
├── .backups/                # 备份存档存放目录
│   ├── full/                # 全备份 存放目录
│   ├── differential/        # 差异备份 存放目录
│   ├── rollback_temp_path/  # 回滚前临时备份 存放目录
│   ├── startup_temp_path/   # 启动前临时备份 存放目录
│   └── log/                 # BedrockBackup 操作日志记录
│
├── .dll/ # 插件目录
|   ├── BedrockBackup/                  # 备份插件目录
│   	├── BedrockBackup.dll           # 备份插件
│   	└── BedrockBackup_Config.json   # 配置文件
│
├── .server_bedrock/         # BDS服务器存放目录
│
└── MCSL-T.exe               # 主程序
```

### 2. 首次运行

1. 确保`BedrockBackup.dll`在`.dll/BedrockBackup/`目录中
2. 启动`MCSL-T.exe`，自动生成默认配置文件

---

## ⚙️ 配置文件详解

### 备份策略 (`backup_strategy`)

```json
{
  "current_backup_type": "Full",  // Full（全量）或 Differential（差异）
  "auto_backup": {
    "enabled": true,                      // 自动备份
    "trigger_mode": "TimeInterval",       // TimeInterval（间隔触发）或 ScheduledTime（定时触发）
    "interval_minutes": 480,              // 间隔分钟数（仅TimeInterval模式有效）
    "scheduled_times": [                  // 定时触发时间点（仅ScheduledTime模式有效）
      {05, 00}, // 默认计划时间1: 凌晨5点
      {13, 00}, // 默认计划时间2: 下午1点 
      {23, 00}  // 默认计划时间3: 晚上11点      
    ]
  }
}
```

### 自动清理 (`auto_cleanup`)

```json
{
  "enabled": true,                        // 启动清理
  "cleanup_mode": "ByAge",                // 清理模式（ByAge/ByCount等）
  "max_backup_age_hours": 24,             // 保留最近72小时的备份
  "max_backups_to_keep": 10               // 最多保留10个备份
}
```

### 安全管理 (`safety_config`)

```json
{
  "startup_safety": {
    "keep_startup_backups": true,     // 服务器启动前自动备份
    "max_startup_backups_to_keep": 2  // 保留(n+1)次启动备份
  },

  "rollback_safety": {
    "keep_rollback_backups": true,    // 服务器回滚前自动备份
    "max_rollback_backups_to_keep": 3 // 保留(n+1)次回滚备份
  }
}
```

### 系统限制 (`system_limits`)

```json
{
  "minimum_required_disk_space_gb": 30 // 创建备份时磁盘至少所需空间 (GB)
}
```

## ❓ 常见问题

**Q：差异备份如何工作？**
A：基于前一个全量备份，仅保存变化的数据

**Q：备份失败怎么办？**

1. 检查 `.backups/log/` 中的错误日志
2. 确认磁盘剩余空间 > 设置的大小
3. 重启服务器后重试

---

## 📜 本项目使用以下第三方库：

```
[nlohmann/json](https://github.com/nlohmann/json)
[Zip-Utils](https://www.codeproject.com/KB/files/zip_utils/zip_utils_src.zip)
```


