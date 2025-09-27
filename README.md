### **玩客云安装 Home Assistant 及 HACS  Xiaomi home 插件教程**

---

#### **一、准备工作**
1. **设备要求**：
   - 玩客云（S805 芯片，1GB 内存，8GB 存储）。
   - 已刷入 Armbian 系统（教程使用 `https://github.com/hzyitc/armbian-onecloud/releases/download/ci-20250305-162012-UTC/Armbian-unofficial_25.05.0-trunk_Onecloud_bookworm_current_6.12.17.burn.img.xz` 镜像）。
   - 网络环境：有线连接优先，确保可访问互联网。

2. **工具准备**：
   - SSH 客户端（如 xshell8，其他）。
   - 文件传输工具（如 xftp8，其他）。

---

### **二、安装 Home Assistant（Docker 版）**

#### **1. 安装 Docker**
```bash
sudo curl -fsSL https://get.docker.com| bash -s docker --mirror Aliyun
```

#### **2. 拉取 Home Assistant 镜像**
```bash
docker pull homeassistant/home-assistant:stable
```

#### **3. 启动容器**
```bash
docker run -d \
  --name homeassistant \
  --privileged \
  --restart=unless-stopped \
  -v /homeassistant:/config \
  --network=host \
  homeassistant/home-assistant:stable
```

**注意事项**：
- `-v /homeassistant:/config`：配置目录映射，确保路径正确。
- 若启动失败，检查命令拼写，避免镜像名或参数错误。

#### **4. 访问 Home Assistant**
浏览器输入 `http://玩客云IP:8123`，按向导初始化账户。

---

### **三、安装 HACS 社区商店（Home Assistant 米家集成一样最下面有链接）**

#### **1. 进入配置目录**
```bash
cd /homeassistant
mkdir -p custom_components
cd custom_components
```

#### **2. 下载并解压 HACS**
```bash
wget https://ghproxy.com/https://github.com/hacs/integration/releases/latest/download/hacs.zip
unzip hacs.zip -d hacs  # 必须解压到 hacs 子目录
rm hacs.zip
```

#### **3. 重启 Home Assistant**
```bash
docker restart homeassistant
```

#### **4. 配置 HACS**
1. 登录 Home Assistant → **「配置」→「设备与服务」→「添加集成」**。
2. 搜索 **HACS** → 点击安装。
3. 根据提示访问 GitHub 授权页面：
   - 输入 GitHub 账号 → 生成授权码。
   - 将授权码粘贴回 Home Assistant。

---

### **四、解决 HACS 无法访问 GitHub**

#### **方法 1：修改 hosts（推荐）**
1. **进入容器终端**：
   ```bash
   docker exec -it homeassistant bash
   ```

2. **添加 GitHub 域名解析**：
   ```bash
   echo "199.232.68.133 raw.githubusercontent.com" >> /etc/hosts
   echo "140.82.113.3 github.com" >> /etc/hosts
   ```

3. **退出并重启**：
   ```bash
   exit
   docker restart homeassistant
   ```

---

### **五、验证 HACS 安装**
1. 登录 Home Assistant → 侧边栏出现 **HACS** 菜单。
2. 在 HACS 中搜索并安装插件（如 `Xiaomi Miot`）：
   - 点击 **「下载」** → 重启 Home Assistant。
   - 在 **「集成」** 中添加设备。

---

### **六、常见问题解决**

**1. Home Assistant拉取失败**
```bash
配置镜像加速
sudo vi /etc/docker/daemon.json
```
```bash
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://docker.1panel.live",
        "https://hub.rat.dev"
    ]
}
```
```bash
sudo service docker restart
```

#### **2. HACS 无法初始化**
- **症状**：卡在 GitHub 授权页面。
- **解决**：
  - 检查 hosts 文件是否生效。
  - 尝试手机热点或全局代理。

#### **3. 文件权限错误**
- **症状**：日志提示 `Permission denied`。
- **修复**：
  ```bash
  chmod -R 755 /homeassistant/custom_components
  ```

#### **4. 容器启动失败**
- **查看日志**：
  ```bash
  docker logs homeassistant
  ```

---

### **总结**
通过本教程，你已成功在玩客云上部署 Home Assistant 并集成 HACS。后续可通过 HACS 安装更多插件（如天气、摄像头支持），打造个性化智能家居系统。遇到网络问题优先使用 **镜像源** 和 **hosts 修改**，保持系统轻量化以适配玩客云的有限硬件资源。




### **来源**
- https://github.com/hzyitc/armbian-onecloud/releases/download/ci-20250305-162012-UTC/Armbian-unofficial_25.05.0-trunk_Onecloud_bookworm_current_6.12.17.burn.img.xz

- https://github.com/tech-shrimp/docker_installer

- https://github.com/hacs/integration/releases/download/2.0.5/hacs.zip

- https://github.com/XiaoMi/ha_xiaomi_home/blob/main/doc/README_zh.md

