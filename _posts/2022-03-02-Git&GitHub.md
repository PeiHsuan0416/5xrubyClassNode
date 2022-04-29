---
tags: 上課筆記共享用,Git
---

# 220302 Git&GitHub

:::info
前言:
萬一不小心刪除怎辦
+ 讓檔案回到最近一次
+ 這一行程式碼是誰寫的
`$ git blame` 檔案名稱 →即可知道誰在甚麼時候寫這一行
:::

## 分支
分支是GIT很大的賣點
只要進行一次Commit就會有一個分支點
HEAD表示現在所在的位置

**GIT的分支不是樹枝狀結構，像是貼紙一樣貼在某一個Commit點**
每多做一次新的Commmit時，被HEAD指著的Master(Main)則會跟著往前移動。
如果要新開的分支往前移動
分支只是貼在commit上的一個貼紙

### 開新的分支作法branch 貼標籤
`$ git branch` 查詢目前有哪些分支
`$ git branch <branchName>` →新增一個分支
`$ git branch -d <branchName>` →刪除分支(把貼紙撕掉)，只是把貼支撕掉，不影響他的內容，但沒事不要丟MAIN
`$ git branch -D <branchName> `→強制刪除分支
`$ git branch -m <branchName>` →改所在分支的名稱
`$ git branch <branchName:ccc> <commieID:85e7>` →在特定commie建立分支：表示在85e75這個地方新增ccc這個分支


+ **如果要新開的分支往前移動→==將HEAD交棒給新開的分支==**
`$ git switch <branchName分支名稱>` →切換給某個分支
`$ git checkout <branchName>` 同上(較舊的版本指令)
當回到過去某個時間點時，表示回到那個時候的樣子，不等於之後狀態下新增的東西不見
並不會每個點都有分支，因此回到過去的時候可能會每有分支
switch只能切換到有分支的地方，checkout可以切換到沒有分支的點或切換到有分支的地方


`$ git checkout -b a1a2 cc797 ` =意思同下
`$ git switch -c a1a2 cc797 `→將HEAD移到ID:cc797這個commit點，並建立一個a1a2的標籤

+ 利用` $ git log` 可以查詢每個Commit點的身分證代碼ID,字串很長一串，使用的時候可以只用前幾碼輸入，一樣找的到位置



### 分支合併merge
+ 注意權杖標籤(HEAD)在誰手上
+ ==事實上分支不能合併，只是把貼紙移過去而已，發生在簡單的合併==(快轉合併fast-forward)
+ 不能往前合併比自己時間舊的branch

#### :bulb:一般快轉合併fast-forward
:::success
如果MAIN(主)要合併CAT(副)，要先把權杖(HEAD)拿回來，才能發動合併
`$ git switch main `→將HEAD轉移到Main
`$ git merge cat` →將MAIN移動到CAT分支上(Main合併Cat)，fast-forward(快轉合併)，Main標籤和Cat標籤都在
:::
如果要取消merge，可以先用`$ git reflog `查詢合併前main在哪個commit點上(ID)
再利用`$ git reset <commitID> `將main退回到該commit點上
如果reset時沒有用`--hard`，則會有檔案被退回到工作區目錄上
此時需要再做一次add及commit，但會多一個支線，且main往前移
![](https://i.imgur.com/UIJNa2Z.png)



#### :bulb:Y型分支merge方式
無法快轉合併，比較複雜。
:::warning
從commit點A，branch兩個分支CAT和DOG貼紙，各自分別commit發展後
CAT貼紙下：cat1,cat2
DOG貼紙下：dog1,dog2

如果權杖(HEAD)在CAT(`$ git switch cat`)要合併DOG，如果直接輸入`$ git marge dog`，則會跳出VIM要輸入 →此步驟要花費時間學，因此改用
`$ git merge dog -m "..."` → 在CAT底下合併DOG
:::
+ 誰合併誰的結果都一樣，只是歷史軌跡呈現的內容不一樣
+ 權杖HEAD在CAT貼紙上，則CAT標籤往前移，歷史軌跡內則出現dog1,dog2，即DOG的歷史軌跡
+ 權杖HEAD在DOG貼紙上，則DOG標籤往前移，歷史軌跡內則出現cat1,cat2

`
若不小心進入vim又進入insert模式，使用 ctrl + [ ，:Q 離開，或是ctrl+X
VIM編輯器：暫時先不用學，很花時間學，但會的話很厲害XDD`

### 分支刪除branch -d
刪掉分支為把貼紙撕掉，貼紙撕掉則無人看管
`$ git branch -d abc` →刪除abc這個標籤貼紙
+ 快轉合併下，已經合併的分支刪除貼紙標籤，由另一個標籤控管
+ 沒有合併的分支，把貼紙撕掉，表示沒有人看管，則底下的內容被隱藏,即為刪除分支
+ 救回分支：貼紙貼回去` $ git branch new_dog 053f`→在ID:053f的位置上，貼上標籤並命名為new_dog貼紙 
    `$ git branch <new_branch_name> <commitID>`


### 為甚麼要使用分支


### 發生衝突時
不同的分支下，修改相同地方的東西，但改不一樣內容，會導致合併失敗，只會收割可以收割的地方，重複的地方不會成功
有衝突的地方GIT會標誌起來提醒你，可以看出衝突的地方
+ 討論GIT顯示的衝突要如何進行，如果檔案都要保留，則可以直接add後commit
+ 或是刪除衝突後再add-commit

### 改變分支地基rebase
`$ git rebase A` →將分支移採在A頭上，地基改為A的地基

+ rebase合併有甚麼好處
    比較乾淨，看起來少一個節點
    用rebase和merge合併都有可能產生衝突
+ rebase合併的壞處
    在不在意發展過程，無法完整保留當時開發過程的歷史紀錄
    但通常選擇上還是常用rebase
+ 不好到退一步，可以用`$ git reflog`幫助查尋歷史紀錄，再reset回到rebase前

:::danger
:question: rebase和merge的差異
- Merge
修改內容的歷史記錄會維持原狀，但是合併後的歷史紀錄會變得更複雜。
- Rebase
修改內容的歷史記錄會接在要合併的分支後面，合併後的歷史記錄會比較清楚簡單，但是，會比使用 merge 更容易發生衝突。

兩者之間差異在歷史時間軸，rebase將自身根基由(a)複製到(b)時，而會讓原本a的commit消除(狀況可參照前面分支提到沒有branch被消除的狀況)，而merge (a) 到 (b) 的時候，則可以收割b的歷史軌跡。
:::

### 如何回到上一步reset
- 將reset看待成become，將版本回溯至指定的location，並同時對檔案的變動進行處理
- 將目前所在的分支與 head ，移動到想要切換的版本，並隱藏該版本之後的 commit
- git世界中沒有刪除commit的指令
`$ git reset <commitID> ` →把整個過程回到C3時候的樣子
後面的參數會決定檔案的去留
`$ git reset <commitID>  --mixed` →移到工作目錄，要先add才能commit，**系統預設**
`$ git reset <commitID>  --soft` →移到暫存區，可以直接commit
`$ git reset <commitID>  --hard` →直接不要處理，直接被隱藏
如果沒有打後面的參數，mixed會是預設值

`^ `caret 倒退一步
`~ `tilde 倒退N步
`\ `backslash 跳脫字元，讓zsh可以辨識^，有些不加也可以
常用HEAD~N
`$ git reset abc\^` →回到abc的上一步
`$ git reset abc\^^` →回到abc的上兩步
以上這些會移到工作目錄上，可再用add-commit加回
`$ git reset abc~10` →退回abc的前10步

$ git reflog 會秀出HEAD的移動軌跡表
    
    哪些會造成HEAD的移動
    switch/checkout
    commit
    merge
    reset
    rebase
    push
    pull

把合併過去的標籤移回到原來的地方，因合併的位置沒有管理標籤，則隱藏起來，如同消失
怎麼被hard reset就怎麼hard回來，從reflog找回原路徑



## GitHub
+ 一款有Web介面的Git伺服器
+ Git不等於GitHub
+ GitHub的使用：可當履歷使用，可以看活耀度、放專案、自介

### ssh密碼設定
透過$ ssh-keygen
產生一對鑰匙：public/private key
到$ cd ~/.ssh →(~是指HOME的意思)
$ ls -al
`$ cat id_rsa.pub` 可以觀看id_rsa.pub內容

之後將產生的public_KEY貼上至個人帳號底下
(放在專案底下表示只給這專案用，下次新增專案就要另外再用一個KEY，切記**私鑰千萬不要給出去**)

$ git remote -v 查詢目前標籤檔案位置
origin  git@github.com:PeiHsuan0416/HW-html.git (fetch)
origin  git@github.com:PeiHsuan0416/HW-html.git (push)
$ git remote rm 移除origin標籤

### 把GIT專案上傳至GitHub
- 在GitHub上建立New repository後注意Quick setup的選擇，要選擇SSH，因為你是要放SSH的鑰匙上去
- 

+ 建立書籤
$ git remote add origin git@github.com:PeiHsuan0416/hello-git.git
+ 把main推到origin
$ git push -u origin main

HTTPS要打密碼，在上傳的時候需要輸入GitHub的帳密
SSH不用打密碼 →透過一對鑰匙(公用鑰匙VS私人鑰匙)，上傳的時候會用私鑰匙做比對

- 如果修改GitHug專案名稱
重新到專案底下
 git remote set-url origin git@github.com:PeiHsuan0416/HW-html.git



---
+ 昨天學到的
$ ls -al /usr/local/bin/code
$ cd /usr/local/bin
$ cd / →可以切換目錄位置
只有re -rf .git才是破壞性操作
*(沒事不要亂用~除非想死想辭職XDDD)*

+ 本日額外指令
$ echo 
$SHELL →

---------------------------------
:::info
 [<回到Hello Ruby 10^th^](/77o2yXRZSX-LHRtabnwLfA)
:::