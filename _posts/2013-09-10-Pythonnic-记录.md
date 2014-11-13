---

layout: post
categories: [Programming language]
tags: [Python, Pythonnic, 技巧]

---

1.map的使用  

        map(int, raw_input().split(' '))  

*注:input()的输入参数可以是python的表达式, 而raw_input()是原原本本的输入字符串  
*这个技巧在刷题的时候用来输入一串整型数经常用到  

2.字符与值的转换

        print ord('C')
        print chr(97)

*注: 打印出来就明白了  

3.全排列  

        itertools.permutations([0,1,2,3,4,5,6,7,8,9])
        
