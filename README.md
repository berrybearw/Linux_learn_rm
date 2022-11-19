# Linux_learn_rm
刪除各種檔案

參考 : 

* https://blog.longwin.com.tw/2016/12/linux-delete-many-files-quick-2016/
* https://kknews.cc/zh-tw/code/o38nvn5.html
* https://yonglhuang.com/rm-file/
* https://www.baeldung.com/linux/delete-large-directory
* https://juejin.cn/post/6844903766366371854
* https://www.firbug.com/a/202106/216584.html

* 檔案名稱有空格

範例 : azzi000 648116.tmp

step 1 : 

`find . -name '* *'`

![image](https://user-images.githubusercontent.com/96226780/202835936-0638d951-608f-42e1-8493-a56c9ae453f1.png)

step 2 : 

`find . -name '* *’ | awk -F / '{print "\""$2"\""}'`

![image](https://user-images.githubusercontent.com/96226780/202835945-1f59e070-c759-4191-bc6f-b69a6b5d45ff.png)

step 3 : 

`find . -name '* *' | awk -F / '{print "\""$2"\""}' | xargs rm -f`

Linux 清理檔案沒釋放空間
---

參考 : 

* [https://access.redhat.com/solutions/2316](https://access.redhat.com/solutions/2316)

* [https://unix.stackexchange.com/questions/68523/find-and-remove-large-files-that-are-open-but-have-been-deleted](https://unix.stackexchange.com/questions/68523/find-and-remove-large-files-that-are-open-but-have-been-deleted)

* [https://serverfault.com/questions/207100/how-can-i-find-phantom-storage-usage](https://serverfault.com/questions/207100/how-can-i-find-phantom-storage-usage)

可以用 lsof 檢查已清理 程序卻卡住 process

`lsof | grep deleted`

刪除檔案介紹
---

平時刪除檔案可以用 `rm`

也可以用到 `unlink`

Linux 檔案關聯簡介

* https://www.796t.com/content/1544701155.html

可如檔案還在被程序使用

delete 後會進入到 背景

查看方式可以用

`find /proc/*/fd -ls | grep deleted`

* 查看檔案大小 ( 第 11 個欄位 )

`lsof | grep deleted`

![image](https://user-images.githubusercontent.com/96226780/202836095-c951e80d-70a2-461c-8dd8-b45eba38ef73.png)

要回收空間 ( 暫時 ) 

`: > /proc/process id/fd/號碼`

永久需用到 

1. 執行寫入指令程序 跑 exec

* https://www.hy-star.com.tw/tech/linux/fd/fd.html#exec_fd_to_file

關閉 stderr

`exec 2>&-`

2. gdb 或 ****reredirect****

gdb 方式 參考 

* https://www.baeldung.com/linux/redirect-output-of-running-process

找到要重新導向 pid

**假設我們要重定向 PID 為 14560 的進程的輸出。**因此，我們應該首先將*gdb*附加到該 PID：

`gdb -p 14560`

**一旦我們附加到進程，我們可以將文件描述符** *1*重定向到*/tmp/process_stdout*：

`(gdb) p dup2(open("/tmp/process_stdout", 1089, 0777), 1)`

**請注意，我們使用了數字*1089*，因為它代表*O_WRONY | O_CREAT | O_APPEND***。

*我們也可以通過使用2*作為文件描述符以完全相同的方式重定向標準錯誤：

`(gdb) p dup2(open("/tmp/process_stderr", 1089, 0777), 2)`

此時，輸出已被重定向。但是，該進程已停止並且*gdb*已附加到該進程。

把通道 1 導向 /dev/null

`(gdb) p dup2(open("/dev/null"), 1)`

退出*gdb*

`(gdb) q`

上述指令撰寫執行檔

``c
p dup2(open("/tmp/process_stdout", 1089, 0777), 1)
p dup2(open("/tmp/process_stderr", 1089, 0777), 2)
q
```

例如執行檔名為 test

`gdb -p process id -x test`

gdb 內核出現異常

'open64' has unknown return type; cast the call to its declared return type

參考 : 

* [https://stackoverflow.com/questions/72071279/attaching-to-a-process-and-call-dup2-on-aarch64](https://stackoverflow.com/questions/72071279/attaching-to-a-process-and-call-dup2-on-aarch64)

可改用 

`(gdb) p (int)dup2((int)open("/tmp/my_stdout", 1089, 0777), 1)`

reredirect
---

* [https://blog.csdn.net/gkxg001/article/details/123010554](https://blog.csdn.net/gkxg001/article/details/123010554)

* [https://icode.best/i/19331945521107](https://icode.best/i/19331945521107)

安裝 

make;make install

用法 : 找出所有撰寫 檔案 pid

把 pid 通道 2 給 /dev/null

****`reredirect -e /dev/null pid`**

![image](https://user-images.githubusercontent.com/96226780/202836162-5752e16f-6479-4460-a74f-ad74004b8582.png)


