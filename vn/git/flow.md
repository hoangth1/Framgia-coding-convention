# Git Flow

## 1. Quản lý branch

### 1.1 Các branch chính
   
   * main
       - Đây là branch ổn định nhất và chứa code của phiên bản mới nhất đã được release lên production. Sau khi một phiên bản được release code sẽ merge vào branch này để đảm bảo rằng code ở branch này là code của phiên bản mới nhất.
  * develop
    - Nhánh này sẽ chứa source code mới nhất đến thời điểm hiện tại phục vụ việc release ở phiên bản tiếp theo.

### 1.2 Các nhánh hỗ trợ

* Features branches
    * Được checkout từ develop
    * Được merge vào develop
    * Quy tắc đặt tên:
    * Các nhánh này được sử dụng để phát triển các tính năng mới để phục vụ cho các bản realease sau này. Mỗi tính năng sẽ là một nhánh riêng và được checkout từ develop sau đó sẽ được merge vào develop

Lưu ý: Các nhánh chính sẽ được bảo vệ để không cho commit trực tiếp lên và tránh đi việc xoá nhầm.
* Hotfix branches
    -  Được checkout từ main
    -  Merge vào develop và main
    -  Quy tắc đặt tên: hotfix-[số task jira]-[tên]
    -  Nhánh này được sử dụng để fix những bug được phát hiện sau khi sản phẩm đã lên môi trường production và đang phát triển tính năng mới cho bản release sau và những bug này bắt buộc phải fix ngay. Một branch hotfix sẽ được checkout ra từ nhánh main để fixbug và release sau đó nhánh này sẽ được merge vào develop và main. 

Chú ý: Các nhánh hỗ trợ có thể được xoá đi sau khi hoàn thành xong nhiệm vụ của nó

## 2. Flow sử dụng git
### 2.1 Flow làm việc cho developer
1. Clone repository từ github
    ```sh
    $ git clone [URL của remote repository]
    ```
   
  2. Truy cập vào local repository trên máy
     ```sh
     $ cd [Đường dẫn của folder vừa clone ở mục 1]
     ```
     
   3. Thực hiện checkout sang nhánh develop
       ```sh
       $ git checkout develop
       ```
   
   4. Sau khi nhận task, để bắt đầu làm việc thì tiến hành việc tạo feature branch để làm tassk
       ```sh
       $ git checkout -b [Tên branch]
       ```
   Lưu ý: Mỗi task chỉ tạo một branch duy nhất để làm việc, mối quan hệ giữa task và branch là mối quan hệ 1:1
   
   5. Tiến hành làm task, trong quá trình làm task có thể tạo bao nhiêu commit tuỳ ý
   Cách tạo commit
       ```
       // Để tạo commit trước hết ta cần add các file vào index, ta có thể add thủ công từng file hoặc add tất cả bằng câu lệnh sau:
       $ git add -A 
       hoặc
       $ git add .
       
       // Tạo commit
       $ git commit  -m "[Commit message]"
       ```
6.  Sau khi làm xong task sẽ tiến hành tạo push và tạo pull request

    Lưu ý rằng mỗi pull request chỉ nên có một commit duy nhất, nếu trong quá trình làm tạo ra nhiều commit thì phải gộp các commit lại   thành một commit và đặt tên theo quy tắc sau : `[ID task][Loại task] [Mô tả ngắn gọn về việc mà commit này làm]` , ví dụ: `[SCP-120][Task] Implement home screen`
    
    Trước khi push lên phải đảm bảo code trên branch phải là code mới nhất từ develop, ta thực hiện như sau:
    ```
    // Checkout sang develop
    $ git checkout develop
    //Pull từ develop về
    $ git pull origin develop
    // Checkout về lại về branch đang làm việc
    $ git checkout [tên branch]
    // Rebase branch với develop
    $ git rebase develop
    ```
    Quá trình rebase có thể xảy ra conflict xem phần sau để biết cách giải quyết
    
    Để  push branch lên remote repository làm như sau:
    ```sh
    $ git push origin [tên branch]
    ```
    
    Trong trường hợp branch đã từng được push lên trước đó và muốn push đè lên thì phỉa force push
    ```sh
    $ git push origin [tên branch] -f
     ```
     
     Sau khi tạo pull thì gửi link pull request vào nhóm dự án để leader review và merge, không được tự ý merge vào develop
     
     Khi có comment từ leader cần phải sửa thì tiến hành sửa code sau đó thực việc add, commit, push lại
     
     ```
     $ git add -A
     $ git commit --amend -m "[commit message]"
     // Trong trường hợp không muốn thay đổi commit message có thể sử dụng câu lệnh sau để tránh việc gõ lại commit message
     $ git commit --amend --no-edit
     ```
     
### 2.2 Flow làm việc cho leader.
* Sau khi thành viên gửi pull request leader sẽ review và merge vào nhánh develop trên github. Trước khi merge leader có thể lấy code về chạy thử bằng cách
    ```
    $ git fetch origin [tên branch]
    $ git checkout [tên branch]
    ```
* Sau khi release sản phẩm thì phải thực hiện merge nhánh develop vào nhánh master
* Khi phát sinh nhánh hotfix thì phải merge nhánh hotfix vào nhánh develop và nhánh master


## 3. Xử lý conflict

Khi xảy ra conflict trong quá trình rebase, sẽ có hiển thị như dưới đây (tại thời điểm này sẽ bị tự động chuyển về một branch vô danh)
```sh
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: refs #1234 Sửa lỗi cache
Using index info to reconstruct a base tree...
Falling back to patching base and 3-way merge...
Auto-merging path/to/conflicting/file
CONFLICT (add/add): Merge conflict in path/to/conflicting/file
Failed to merge in the changes.
Patch failed at 0001 refs #1234 Sửa lỗi cache
The copy of the patch that failed is found in:
    /path/to/working/dir/.git/rebase-apply/patch

When you have resolved this problem, run "git rebase --continue".
If you prefer to skip this patch, run "git rebase --skip" instead.
To check out the original branch and stop rebasing, run "git rebase --abort".
```

1. Hãy thực hiện fix lỗi conflict thủ công（những phần được bao bởi <<< và >>> ）。
Trong trường hợp muốn dừng việc rebase lại, hãy dùng lệnh `git rebase --abort`。

2. Khi đã giải quyết được tất cả các conflict, tiếp tục thực hiện việc rebase bằng:

    ```sh
    $ git add -A
    $ git rebase --continue
    ```
