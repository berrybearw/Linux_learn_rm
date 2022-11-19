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
