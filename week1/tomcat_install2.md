# Apache Tomcat 9 安裝與入門學習文件
### Web Component Development with Servlets & JSPs

> 適用程度：初學者 ｜ 語言：Java ｜ 規範：Java EE 8 (Servlet 4.0 / JSP 2.3)

---

## 學習目標

完成本文件後，你將能夠：

- [x] 說明 Tomcat 在 Web 應用程式中扮演的角色
- [x] 完成 Windows 環境的安裝與環境變數設定
- [x] 啟動、驗證、停止 Tomcat 伺服器
- [x] 設定 Manager 管理介面帳號
- [x] 部署並執行第一個 Servlet 應用程式
- [x] 排查常見安裝錯誤

---

## 一、核心概念說明

> 在動手安裝之前，先理解每個元件的角色，能幫助你更有效率地解決問題。

### 1-1 什麼是 Tomcat？

**Apache Tomcat** 是一個開放原始碼的 **Web 容器（Web Container）**，也稱為 **Servlet 容器（Servlet Container）**。

```
使用者瀏覽器
      │
      │  HTTP 請求 (HTTP Request)
      ▼
┌─────────────────────────┐
│   Apache Tomcat         │  ← Web 容器 (Web Container)
│                         │
│  ┌───────────────────┐  │
│  │   Servlet 引擎    │  │  ← 負責執行 Java Servlet 程式碼
│  └───────────────────┘  │
│  ┌───────────────────┐  │
│  │   JSP 引擎        │  │  ← 將 JSP 轉換成 Servlet 並執行
│  └───────────────────┘  │
└─────────────────────────┘
      │
      │  HTTP 回應 (HTTP Response)
      ▼
使用者瀏覽器（顯示網頁）
```

### 1-2 關鍵術語對照

| 英文術語 | 中文說明 |
|---------|---------|
| **Servlet** | 一種 Java 類別，用來處理 HTTP 請求並產生回應（通常是 HTML） |
| **JSP (JavaServer Pages)** | 在 HTML 中嵌入 Java 程式碼的樣板技術 |
| **Web Container** | 負責管理 Servlet 生命週期的執行環境 |
| **WAR (Web Archive)** | 打包 Web 應用程式的標準格式（.war 檔） |
| **CATALINA_HOME** | Tomcat 安裝根目錄的環境變數名稱 |
| **JAVA_HOME** | JDK 安裝根目錄的環境變數名稱 |
| **Port 8080** | Tomcat 預設監聽的 HTTP 通訊埠 |

### 1-3 Tomcat 目錄結構

安裝完成後，你會看到以下重要目錄：

```
C:\apache-tomcat-9.0.71\
├── bin\            ← 啟動/停止指令 (startup.bat, shutdown.bat)
├── conf\           ← 設定檔 (server.xml, tomcat-users.xml)
├── webapps\        ← 部署 Web 應用程式的位置（重要！）
│   ├── ROOT\       ← 預設首頁應用程式
│   └── manager\    ← Manager 管理介面
├── logs\           ← 記錄檔（排錯時必看）
└── lib\            ← Tomcat 核心函式庫
```

> 💡 **學習提示**：記住 `bin\`、`conf\`、`webapps\`、`logs\` 這四個目錄，90% 的操作都在這裡。

---

## 二、前置條件

| 項目 | 需求 | 確認指令 |
|------|------|---------|
| JDK 17 | 已安裝於 `C:\Program Files\Java\jdk-17` | `java -version` |
| 解壓縮軟體 | Windows 內建或 7-Zip / WinZip 皆可 | — |

**確認 JDK 是否已安裝：**
```cmd
java -version
```
預期輸出：
```
java version "17.x.x" ...
```

> ⚠️ 若顯示 `'java' is not recognized`，請先安裝 JDK 17 再繼續。

---

## 三、下載與解壓縮

1. 前往官方下載頁（選擇穩定版）：
   ```
   https://tomcat.apache.org/download-90.cgi
   ```
2. 在 **Binary Distributions → Core** 區塊，下載 **zip** 格式。
3. 將 zip 解壓縮至 `C:\`，確認結果目錄為：
   ```
   C:\apache-tomcat-9.0.71\
   ```

> 💡 **建議**：安裝路徑避免含有中文或空格，以免啟動時出現路徑解析錯誤。

### 現在試試看 ✅
開啟「檔案總管」，確認 `C:\apache-tomcat-9.0.71\bin\startup.bat` 存在。

---

## 四、設定系統環境變數

> **路徑**：`Win + S` 搜尋「環境變數」→「編輯系統環境變數」→「環境變數(N)」

### 4-1 新增 `JAVA_HOME`（必設）

| 欄位 | 值 |
|------|----|
| 變數名稱 | `JAVA_HOME` |
| 變數值 | `C:\Program Files\Java\jdk-17` |

### 4-2 新增 `CATALINA_HOME`

| 欄位 | 值 |
|------|----|
| 變數名稱 | `CATALINA_HOME` |
| 變數值 | `C:\apache-tomcat-9.0.71` |

### 4-3 更新 `Path`（系統變數）

在「系統變數 → Path」中**新增一行**：
```
C:\Program Files\Java\jdk-17\bin
```

> ⚠️ **重要**：設定完成後須**重新開啟** Command Prompt，新的環境變數才會生效。

### 驗證環境變數設定是否正確

開啟**新的** Command Prompt，執行以下指令：
```cmd
echo %JAVA_HOME%
echo %CATALINA_HOME%
java -version
```

預期輸出：
```
C:\Program Files\Java\jdk-17
C:\apache-tomcat-9.0.71
java version "17.x.x"
```

### 現在試試看 ✅
執行上方三個 `echo` 指令，若三個都有正確輸出，才繼續下一步。

---

## 五、啟動與驗證 Tomcat

### 5-1 啟動 Tomcat

```cmd
cd %CATALINA_HOME%\bin
startup.bat
```

啟動成功的訊號：跳出新視窗，最後顯示：
```
Server startup in [數字] milliseconds
```

### 5-2 瀏覽器驗證

開啟瀏覽器，輸入：
```
http://localhost:8080
```
看到 Tomcat 的橘色貓咪歡迎頁，即代表**安裝成功**。

### 5-3 停止 Tomcat

```cmd
cd %CATALINA_HOME%\bin
shutdown.bat
```

### 現在試試看 ✅
啟動 Tomcat 後，前往 `http://localhost:8080` 截圖記錄成功畫面。

---

## 六、設定 Manager 管理介面帳號

Manager 介面（**Tomcat Web Application Manager**）讓你可以透過瀏覽器部署、啟動、停止 WAR 應用程式，不需要手動複製檔案。

### 6-1 編輯 `tomcat-users.xml`

**檔案路徑：**
```
C:\apache-tomcat-9.0.71\conf\tomcat-users.xml
```

找到 `</tomcat-users>` 結尾標籤，在**其前方**插入以下設定：

```xml
<role rolename="tomcat"/>
<role rolename="manager"/>
<role rolename="manager-gui"/>
<role rolename="manager-script"/>

<!-- 開發用帳號（僅限本機使用） -->
<user username="tomcat"   password="tomcat"   roles="tomcat,manager-gui"/>

<!-- 腳本部署帳號（CI/CD 使用） -->
<user username="deployer" password="deployer" roles="manager-script"/>

<!-- 管理員帳號 -->
<user username="manager"  password="manager"  roles="manager,manager-gui"/>
```

> ⚠️ **安全提醒**：上方密碼僅供學習環境使用。正式環境請使用強密碼並限制 IP 存取。

### 6-2 重新啟動 Tomcat 使設定生效

```cmd
cd %CATALINA_HOME%\bin
shutdown.bat
startup.bat
```

### 6-3 登入 Manager 介面

```
http://localhost:8080/manager/html
```

使用 `tomcat / tomcat` 登入，應可看到應用程式清單。

### 現在試試看 ✅
登入 Manager 介面後，確認 `/examples` 和 `/manager` 應用程式狀態為 `running`。

---

## 七、部署第一個 Servlet（Hello World）

> **目標**：從零建立並部署一個能回應 HTTP 請求的 Java Servlet。

### 7-1 建立目錄結構

```
C:\myapp\
└── WEB-INF\
    ├── web.xml
    └── classes\
        └── HelloServlet.class   ← 編譯後放這裡
```

### 7-2 撰寫 `HelloServlet.java`

```java
import java.io.*;
import jakarta.servlet.*;
import jakarta.servlet.http.*;
import jakarta.servlet.annotation.*;

// @WebServlet 註解：宣告這個類別是 Servlet，對應 URL /hello
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    // doGet：處理 HTTP GET 請求
    @Override
    protected void doGet(HttpServletRequest request,
                         HttpServletResponse response)
            throws ServletException, IOException {

        // 設定回應的內容類型 (Content-Type)
        response.setContentType("text/html;charset=UTF-8");

        // 取得輸出串流，寫入 HTML 回應
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello, Tomcat!</h1>");
        out.println("<p>Servlet 執行成功 ✅</p>");
        out.println("</body></html>");
    }
}
```

### 7-3 建立 `web.xml`（部署描述器）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee
                             https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">
  <display-name>My First Servlet App</display-name>
</web-app>
```

### 7-4 編譯 Servlet

```cmd
javac -cp "%CATALINA_HOME%\lib\servlet-api.jar" HelloServlet.java -d C:\myapp\WEB-INF\classes
```

### 7-5 部署至 Tomcat

將整個 `myapp\` 目錄複製到：
```
C:\apache-tomcat-9.0.71\webapps\myapp\
```

### 7-6 存取 Servlet

重啟 Tomcat 後，開啟瀏覽器：
```
http://localhost:8080/myapp/hello
```

看到「Hello, Tomcat!」即表示部署成功。

### 現在試試看 ✅
修改 `HelloServlet.java` 中的回應文字，重新編譯後重啟 Tomcat，確認畫面更新。

---

## 八、常見錯誤排查

| 錯誤症狀 | 可能原因 | 解決方式 |
|---------|---------|---------|
| `'startup' is not recognized` | 未切換到 `bin\` 目錄 | `cd %CATALINA_HOME%\bin` |
| 啟動後瀏覽器顯示「無法連線」 | Port 8080 被其他程式佔用 | `netstat -ano \| findstr :8080`，終止佔用程序 |
| Manager 介面出現 403 Forbidden | `tomcat-users.xml` 未設定 `manager-gui` 角色 | 確認 XML 設定並重啟 |
| Servlet 顯示 404 Not Found | 目錄結構錯誤或未重啟 | 確認 `webapps\myapp\WEB-INF\classes\` 存在後重啟 |
| `JAVA_HOME` 未設定警告 | 環境變數名稱或路徑有誤 | 確認路徑不含結尾 `\`，重開 cmd |
| `Address already in use: 8080` | Tomcat 已在執行中 | 先執行 `shutdown.bat` 再重啟 |

### 查看錯誤記錄檔

當遇到無法判斷的問題時，開啟：
```
C:\apache-tomcat-9.0.71\logs\catalina.out
```
搜尋 `ERROR` 或 `SEVERE` 關鍵字，找到錯誤根因。

---

## 九、版本對應說明

| Tomcat 版本 | Java EE / Jakarta EE | Servlet 規範 | JSP 規範 |
|-------------|---------------------|-------------|---------|
| Tomcat 10.x | Jakarta EE 9+        | Servlet 5.0 | JSP 3.0 |
| **Tomcat 9.x** | **Java EE 8**    | **Servlet 4.0** | **JSP 2.3** |
| Tomcat 8.5  | Java EE 7            | Servlet 3.1 | JSP 2.3 |
| Tomcat 7.x  | Java EE 6            | Servlet 3.0 | JSP 2.2 |

> ⚠️ **重要注意**：Tomcat 10+ 將套件命名從 `javax.servlet.*` 改為 `jakarta.servlet.*`，兩者**不相容**。本文件以 Tomcat 9 為準。

---

## 十、學習里程碑

完成以下步驟，確認自己已達到各里程碑：

```
[ ] 里程碑 1：環境確認
    └─ java -version 與 echo %CATALINA_HOME% 均輸出正確結果

[ ] 里程碑 2：Tomcat 啟動
    └─ http://localhost:8080 顯示 Tomcat 歡迎頁

[ ] 里程碑 3：Manager 介面
    └─ http://localhost:8080/manager/html 登入成功

[ ] 里程碑 4：第一個 Servlet
    └─ http://localhost:8080/myapp/hello 顯示 Hello, Tomcat!

[ ] 里程碑 5：錯誤排查
    └─ 能看懂 logs/catalina.out 的 ERROR 訊息
```

---

## 十一、下一步學習方向

| 主題 | 說明 |
|------|------|
| HTTP 方法 | 學習 `doPost()`、`doPut()`、`doDelete()` 的使用場景 |
| 請求參數 | `request.getParameter()` 取得表單輸入值 |
| 會話管理 | `HttpSession` 追蹤使用者登入狀態 |
| JSP 模板 | 使用 JSP + JSTL 分離 Java 邏輯與 HTML 呈現 |
| WAR 打包 | 用 Maven 打包並部署 WAR 檔到 Tomcat |
| JDBC 整合 | Servlet 連接 MySQL 資料庫進行 CRUD |

---

*文件版本：v2.0 ｜ 最後更新：2026-05-13 ｜ 適用 Tomcat 9.0.x + JDK 17*