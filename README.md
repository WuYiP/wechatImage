# wechatImage
# itchat+pillow实现微信好友头像爬取和拼接


### 使用方法（前提是设备安装了python）：

下载[本项目]到本地，打开项目主目录，打开命令行，输入：

    pip install -r requirements.txt

等待安装完成，输入：

    python wxImage.py

出现如下二维码：扫一扫，确认登陆即可。

------

## 核心

python：

 - itchat(用于爬取头像)
 - pillow(用于拼接图片)

------

##源码详解

首先登陆python版本微信itchat，生成二维码：

    itchat.auto_login(enableCmdQR=True)

获取好友列表：

    friends = itchat.get_friends(update=True)[0:]

然后使用itchat的get_head_img(userName=none)函数来爬取好友列表的头像,并下载到本地：

```
num = 0

for i in friends:
	img = itchat.get_head_img(userName=i["UserName"])
	fileImage = open(user + "/" + str(num) + ".jpg",'wb')
	fileImage.write(img)
	fileImage.close()
	num += 1
```
计算出每张头像缩小后的尺寸(由于为了拼接之后可以用来作为为微信头像，所以合成的图片大小都是640 * 640的，因为微信头像大小就是640 * 640)

计算每张头像缩小后的边长(默认为正方形)：

    eachsize = int(math.sqrt(float(640 * 640) / numPic))

 计算合成图片每一边分为多少小边：

    numline = int(640 / eachsize)

缩小并拼接图片：
```
x = 0
y = 0

for i in pics:
	try:
		#打开图片
		img = Image.open(user + "/" + i)
	except IOError:
		print("Error: 没有找到文件或读取文件失败")
	else:
		#缩小图片
		img = img.resize((eachsize, eachsize), Image.ANTIALIAS)
		#拼接图片
		toImage.paste(img, (x * eachsize, y * eachsize))
		x += 1
		if x == numline:
			x = 0
			y += 1
```
保存图片到本地：

    toImage.save(user + ".jpg")

在微信的文件传输助手发合成后的图片给使用者：

    itchat.send_image(user + ".jpg", 'filehelper')
