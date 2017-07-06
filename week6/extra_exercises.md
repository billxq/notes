### 打印文件中特定的某行到某行之间的内容  
  * 有个文件1.txt，内容为：  
  ```
  ert
  fff
  **
  [abcfd]
  123
  324
  444
  [rty]
  **
  fgfgf
  ```  
  现在要截取其中的这个部分，即：  
  ```
  [abcfd]
  123
  324
  444
  [rty]
  ```  
  如何操作？  
  ```
  [root@61 test]# sed -n '/\[abcfd\]/,/\[rty\]/'p 1.txt   
  [abcfd]
  123
  324
  444
  [rty]
  ```
  \# 如果不加脱义符号，那么\[abc\]就表示abc中的任何一个字母。  

  * 把每个单词的第一个小写字母变大写  
  ```
  [root@61 test]# echo -e "hello\nyou\nme" | sed 's/\b[a-z]/\u&/g'
  Hello
  You
  Me
  ``` 
  \# 例子中\\b表示单词的边界，如果要让每个单词最后一个字母大写，就可以写成```echo -e "hello\nyou\nme" | sed 's/[a-z]\b/\u&/g'```，\\u表示大写  

  * 与上相反，把开头的大写改成小写  
  ```
  [root@61 test]# echo -e "Hello\nYou\nMe" | sed 's/\b[A-Z]/\l&/g'
  hello
  you
  me
  ```  

  * 有文件2.txt，匹配以a开头的行，并在最后加12  
  ```  
  [root@61 test]# cat 2.txt 
  askdj
  aslkd aslkdjf3e
  skdjfsdfj
  sdkfjk
  fsdkfjksdjfkjsdf
  12sdfesdf
  aslkdjfkasdjf asdlfkjaskdfj
  [root@61 test]# sed 's/\(^a.*\)/\1 12/' 2.txt 
  askdj 12
  aslkd aslkdjf3e 12
  skdjfsdfj
  sdkfjk
  fsdkfjksdjfkjsdf
  12sdfesdf
  aslkdjfkasdjf asdlfkjaskdfj 12  
  ```  
  \# \1代表前面\(^a.*\)匹配的内容  

  



