# Git Basics
## 取得 Git Repository
可使用兩種方式取得 Git Repository

* 將現有的專案或者目錄匯入 Git
若要開始使用 Git 追蹤現有的專案，只需要進入該專案的目錄並執行：  
`$ git init`

此指令會建立名為`.git`的子目錄，該目錄包含一個 Git Repository 必要的所有檔案。目前來說，專案內任何檔案都還沒有被追蹤。使用者能以少數的`git add`命令指定要追蹤的檔案，並提交：

	$ git add *.c
	$ git add README
	$ git commit -m 'initial project version'

* 從其他伺服器複製一份已存在的 Git Repository
若想要取得現有的 Git Repository 的複本，那需要使用的是`git clone`。此方式可以取得該專案歷史中所有檔案的所有版本。  
也可以使用`git clone [url]`，複制單一個 Repository 。例如：
`$ git clone git://github.com/schacon/grit.git`

接下來會有個名為`grit`的目錄被建立，並在旗下初始化名為`.git`的目錄。複製所以存在該  Repository 的所有資料，並取得最新版本為工作複本。不過也可以指定目錄的名字。
`$ git clone git://github.com/schacon/grit.git mygrit`

## Recording Changes to the Repository
在工作目錄內的每個檔案可能為兩種狀態的任一種： tracked 或 untracked。tracked 是最近的 snapshot ;可以被復原、修改或者暫存。untracked 則是其他未在最近 snapshot 也未被暫存的任何檔案。第一次複製 repository 食，所有檔案都是被追蹤且未被修改的。只要編輯任何已被 tracked 的檔案，Git 將視為被更動的。

![The lifecycle of the status of your files.](lifecycle.png)

### Checking the 