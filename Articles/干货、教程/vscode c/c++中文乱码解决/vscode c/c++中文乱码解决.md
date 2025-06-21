**大概率是编码问题**，看右下角编码是否为GBK，默认一般是UTF-8编码！
![image.png](./c++中文乱码解决.assert/1726918990164-e4d7b886-3564-4ccd-bc99-7a58e172e0c4.png)


---
解决方法很简单，在项目文件的.vscode文件下找到setting.json
![image.png](./c++中文乱码解决.assert/1726919460488-5499bbec-2148-46ed-8c7a-80cb7189480d.png)

以c++为例，在对应语言下添加"files.encoding":"gbk"即可解决！
添加的上方如果还有其他设置，记得加个逗号！
![image.png](./c++中文乱码解决.assert/1726919666670-0e90f0be-7e7d-4f23-b908-85b25059ef96.png)

先前创建的c/c++文件更改编码方式只需在对应文件下按ctrl+shift+p，搜索“更改文件编码”，通过编码保存，选择GBK即可
![image.png](./c++中文乱码解决.assert/1726919760691-da251848-1fb3-4fd9-9ba7-ca22a3be6f37.png)

![image.png](./c++中文乱码解决.assert/1726919783891-ceb34fea-370d-4866-bf05-1f14a7dc72e3.png)

![image.png](./c++中文乱码解决.assert/1726919807613-e9bb3185-92ad-4509-843c-cde02a92864f.png)

快去愉快的coding吧~
![image.png](./c++中文乱码解决.assert/1726919969143-be582482-ca9a-4be9-914e-c110c8df3278.png)



