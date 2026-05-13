# Tomcat installation with Servlets & JSPs

> **課程背景**：Java EE 7 — Servlet 4.0 / JavaServer Pages 2.3

---

## 安裝 Apache Tomcat 9.x

**學習重點**：如何在 Windows 上安裝、設定環境變數並啟動 Tomcat 9，以及設定 Manager 管理介面帳號。

---

## 一、前置條件

在開始安裝之前，請確認以下項目已完成：

| 項目 | 說明 |
|------|------|
| JDK 17 | 已安裝於 `C:\Program Files\Java\jdk-17` |
| 解壓縮軟體 | Windows 內建或 7-Zip / WinZip 皆可 |

---

## 二、下載與解壓縮

1. 開啟瀏覽器，前往官方下載頁：
   ```
   https://tomcat.apache.org/download-90.cgi
   ```
2. 下載 **Core → zip** 版本（例如 `apache-tomcat-9.0.71.zip`）。
3. 開啟「檔案總管」，找到下載的 zip 檔。
4. 滑鼠右鍵點擊 zip 檔 → 選擇「解壓縮至此」（或解壓縮到 `C:\`）。
5. 確認解壓縮後的目錄為：
   ```
   C:\apache-tomcat-9.0.71
   ```

---

## 三、設定系統環境變數

> **路徑**：控制台 → 系統 → 進階系統設定 → 環境變數

### 3-1 新增 `JAVA_HOME`（必設）

| 欄位 | 值 |
|------|----|
| 變數名稱 | `JAVA_HOME` |
| 變數值 | `C:\Program Files\Java\jdk-17` |

### 3-2 新增 `CATALINA_HOME`

| 欄位 | 值 |
|------|----|
| 變數名稱 | `CATALINA_HOME` |
| 變數值 | `C:\apache-tomcat-9.0.71` |

### 3-3 更新 `Path`（系統變數）

1. 在「系統變數」區塊找到 `Path`，點選「編輯」。
2. 點選「新增」，輸入：
   ```
   C:\Program Files\Java\jdk-17\bin
   ```
3. 點選「確定」儲存所有視窗。

> **提示**：修改環境變數後，需重新開啟 Command Prompt 才會生效。

---

## 四、啟動與驗證 Tomcat

1. 開啟 **Command Prompt**（cmd）。
2. 切換到 Tomcat 的 `bin` 目錄：
   ```cmd
   cd %CATALINA_HOME%\bin
   ```
3. 執行啟動指令：
   ```cmd
   startup.bat
   ```
4. 若跳出新的 Command Prompt 視窗，並顯示 `Server startup in [ms]` 訊息，表示**啟動成功**。
5. 開啟瀏覽器，輸入：
   ```
   http://localhost:8080
   ```
   看到 Tomcat 預設歡迎頁即代表安裝成功。

**停止 Tomcat：**
```cmd
shutdown.bat
```

---

## 五、設定 Manager 管理介面帳號

### 5-1 編輯 `tomcat-users.xml`

檔案路徑：
```
%CATALINA_HOME%\conf\tomcat-users.xml
```

在 `<tomcat-users>` 標籤內加入以下角色與使用者設定：

```xml
<role rolename="tomcat"/>
<role rolename="manager"/>
<role rolename="manager-gui"/>

<!-- 一般使用者：具備 Tomcat 應用程式管理 GUI 權限 -->
<user username="tomcat"   password="tomcat"   roles="tomcat,manager-gui"/>

<!-- 雙重角色使用者 -->
<user username="both"     password="tomcat"   roles="tomcat,manager"/>

<!-- 管理員：具備完整 Manager 權限 -->
<user username="manager"  password="manager"  roles="manager,manager-gui"/>
```

> ⚠️ **安全提醒**：正式環境請務必更換為強密碼，避免使用預設帳號密碼。

### 5-2 重新啟動 Tomcat

修改 `tomcat-users.xml` 後，必須重新啟動 Tomcat 使設定生效：
```cmd
shutdown.bat
startup.bat
```

### 5-3 進入管理介面

```
http://localhost:8080/manager/html
```

輸入上方設定的帳號密碼即可登入。

---

## 六、版本對應說明

| Tomcat 版本 | Servlet 規範 | JSP 規範 |
|-------------|-------------|---------|
| Tomcat 9.x  | Servlet 4.0 | JSP 2.3 |
| Tomcat 8.5  | Servlet 3.1 | JSP 2.3 |
| Tomcat 7.x  | Servlet 3.0 | JSP 2.2 |

> **補充**：Apache Tomcat 9 實作 Servlet 4.0 與 JavaServer Pages 2.3（Java EE 8）規範。