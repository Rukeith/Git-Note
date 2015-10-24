# Git on Server
此處在講如何架設 Git

## Protocol
Git 可以使用四種主要的 Protocol 來傳輸資料：Local, HTTP, SSH, Git。其中除了
HTTP 外，其他都要求在伺服器端安裝並運行 Git。

### Local Protocol
Local protocol 是最基礎的，在此協定中 Remote repository 就是指另一個硬碟中的資料夾。這種常見於使用共用的檔案系統。當要 clone 時，只需將 Remote repository 的路徑作為 URL 使用:

	如果只給路徑，Git 會嘗試用硬連結或直接複製所需的檔案
	$ git clone /opt/git/project.git
	or
	如果使用了 file://，Git 會調用平實通過網路來傳輸資料的方式
	但此方式的效率相對較低
	$ git clone file:///opt/git/project.git

要添加一個 Local Repository 作為現有 Git 的 Remote repository

	$ git remote add <local project> /opt/git/project.git

**優點**  
優點在於簡單，同時保留了現存檔的許可權和網路存取權限。相當容易建置。  

**缺點**  
缺點是，與其他方式相比，難以控制從不同位置來的存取權限。另一個問題是有時候會比其他協定還要慢

### SSH
這是 Git 最常見的傳輸協定。因為大多數環境都支援 SSH，即使沒有支援架設也很容易。SSH 也是唯一一個同時支持讀寫操作的網路通訊協定。另外兩個網路通訊協定（HTTP 和 Git）通常都是唯讀的，所以雖然二者對大多數人都可用，但執行寫操作時還是需要 SSH。SSH 同時也是一個驗證授權的網路通訊協定。

	$ git clone ssh://user@server/project.git
	或者不指明某個協定，這時 Git 會預設使用 SSH
	$ git clone user@server:project.git

**優點**  
架設很方便，很多作業系統都自帶了 SSH 或相關的管理工具。而且，通過 SSH 進行訪問是安全的，所有資料都是加密和授權的。最後，和 Git 及本地協議一樣，SSH 也很高效，會在傳輸之前盡可能壓縮資料。

**缺點**
SSH 的限制在於不能使用匿名訪問，即使只是讀取資料，也必須在能通過 SSH 訪問主機的前提下。這樣不利於開源的項目，所以想允許匿名訪問，還需要支援其他協定以便他人訪問讀取。

### Git
這是一個包含在 Git 中的特殊守護進程。它會監聽一個提供類似於 SSH 的特定 port(9418)，而無需任何授權。要支援 Git 協定的倉庫，需要先創建`git-daemon-export-ok`檔案 — 它是協定進程提供倉庫服務的必要條件 — 但除此之外該服務沒有什麼安全措施。要麼所有人都能克隆 Git 倉庫，要麼誰也不能。這也意味著該協議通常不能用來進行推送。你可以允許推送操作；然而由於沒有授權機制，一旦允許該操作，網路上任何一個知道專案 URL 的人將都有推送許可權。

**優點**  
Git 協定是現存最快的傳輸協議。如果你在提供一個有很大訪問量的公共專案，或者一個不需要對讀操作進行授權的龐大項目，架設一個 Git 守護進程來供應倉庫是個不錯的選擇。它使用與 SSH 協定相同的資料傳輸機制，但省去了加密和授權的開銷。

**缺點**  
Git 協議消極的一面是缺少授權機制。用 Git 協議作為訪問專案的唯一方法通常是不可取的。一般的做法是，同時提供 SSH 介面，讓幾個開發者擁有推送（寫）許可權，其他人通過 `git://` 擁有唯讀許可權。 Git 協定可能也是最難架設的協議。它要求有單獨的守護進程，需要定制 — 我們將在本章的 “Gitosis” 一節詳細介紹它的架設 — 需要設定`xinetd`或類似的程式，而這些工作就沒那麼輕鬆了。該協議還要求防火牆開放 9418 埠，而企業級防火牆一般不允許對這個非標準埠的訪問。大型企業級防火牆通常會封鎖這個少見的埠。

### HTTP/HTTPS
Git 可以用兩種不同的模式連接 HTTP，在 Git 1.6.6 之前只有一種方式可以連接，非常簡單的且唯讀。在 1.6.6 版本之後，Git 能夠聰明的判斷用一種類似於 SSH 的資料傳輸。

#### Smart HTTP
此新版本連結方式執行起來非常類似於 SSH 和 Git 協定，但是運行在標準的 HTTP/S ports 和可以使用多種 HTTP 認證機制。意味著對使用者來說像是更基本的 username/password 的基本認證方式而不是產生 SSH keys。

這是現在最受歡迎的 Git 使用方式。因為可以設定如`git://`的匿名服務和 SSH 協定的認證和加密機制。

#### Dumb HTTP
如果當 Git 的沒有接受到 Smart HTTP 伺服器的回應，Git client 會嘗試退回使用簡單的"dumb HTTP"。Dumb HTTP 預計 Git 倉庫會像一般伺服器傳送檔案給 web 伺服器。HTTP 協議的優美之處在於架設的簡便性。基本上，只需要把 Git 的裸倉庫檔放在 HTTP 的根目錄下，配置一個特定的`post-update`掛鉤（hook）就可以搞定。每個能訪問 Git 倉庫所在伺服器上 web 服務的人都可以進行克隆操作。下面的操作可以允許通過 HTTP 對倉庫進行讀取：

	$ cd /var/www/htdocs/
	$ git clone --bare /path/to/git_project gitproject.git
	$ cd gitproject.git
	$ mv hooks/post-update.sample hooks/post-update
	$ chmod a+x hooks/post-update

Git 附帶的`post-update`掛鉤會默認運行合適的命令（`git update-server-info`）來確保通過 HTTP 的獲取和克隆正常工作。這條命令在你用 SSH 向倉庫推送內容時運行；之後，其他人就可以用下面的命令來 clone 倉庫：

	$ git clone http://example.com/gitproject.git

一般來說會選擇 Smart HTTP 或是 Dumb HTTP，很少會混用兩者。

**優點**  
在此我們會著重於 Smart 版本的 HTTP 協定。  
可以使用 useranme 和 password 的方式來認證，是跟 SSH 相比上一個很大的優勢。使用者不用再產生 SSH Keys 和上傳。減少了許多複雜的操作。而且 HTTP/S 是一個相當常用的協定，公司的防火牆都會允許使用的 ports，而且也可以通過 HTTPS，意味著可以用加密傳輸，並且如 SSL 的認證。

**缺點**  
在認證方面，HTTP/S 比起 SSH 複雜了一些

## 