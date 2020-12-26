# 基于[yolo3](https://github.com/pjreddie/darknet.git) 与[crnn](https://github.com/meijieru/crnn.pytorch.git)  实现中文自然场景文字检测及识别

## 0.功能特性

- [x] 文字方向检测 0、90、180、270度检测（支持dnn/tensorflow） 
- [x] 支持(darknet/opencv dnn /keras)文字检测,支持darknet/keras训练
- [x] 不定长OCR训练(英文、中英文) crnn\dense ocr 识别及训练 ,新增pytorch转keras模型代码(tools/pytorch_to_keras.py)
- [x] 支持darknet 转keras, keras转darknet, pytorch 转keras模型
- [x] 身份证/火车票结构化数据识别 
- [x] 新增CNN+ctc模型，支持DNN模块调用OCR，单行图像平均时间为0.02秒以下     
- [ ] CPU版本加速    
- [ ] 支持基于用户字典OCR识别    
- [ ] 新增语言模型修正OCR识别结果  
- [ ] 支持树莓派实时识别方案  



## 1.训练代码和数据集    

训练代码在master分支。

ocr训练数据集：

ocr ctc训练数据集(压缩包解码:chineseocr)         
百度网盘地址:链接: https://pan.baidu.com/s/1UcUKUUELLwdM29zfbztzdw 提取码: atwn       
gofile地址:http://gofile.me/4Nlqh/uT32hAjbx   密码 https://github.com/chineseocr/chineseocr   




## 2.环境配置

GPU部署 参考:setup.md     
CPU部署 参考:setup-cpu.md   



## 3.编译darknet

如果直接运用opencv dnn或者keras yolo3 可忽略darknet的编译

```bash
git clone https://github.com/pjreddie/darknet.git 
mv darknet chineseocr/
##编译对GPU、cudnn的支持 修改 Makefile
#GPU=1
#CUDNN=1
#OPENCV=0
#OPENMP=0
make 
```

修改 darknet/python/darknet.py line 48

```python
root = '/root/'##chineseocr所在目录     
lib = CDLL(root+"chineseocr/darknet/libdarknet.so", RTLD_GLOBAL)    
```

修改 darknet/python/darknet.py line 155:

```python
print(r)
```





## 4.预训练模型

模型文件地址:
* 百度网盘:https://pan.baidu.com/s/1gTW9gwJR6hlwTuyB6nCkzQ

模型转换：

pytorch ocr 转keras ocr     

``` Bash
python tools/pytorch_to_keras.py  -weights_path models/ocr-dense.pth -output_path models/ocr-dense-keras.h5
```
darknet 转keras     
``` Bash
python tools/darknet_to_keras.py -cfg_path models/text.cfg -weights_path models/text.weights -output_path models/text.h5
```
keras 转darknet      
``` Bash
python tools/keras_to_darknet.py -cfg_path models/text.cfg -weights_path models/text.h5 -output_path models/text.weights
```



## 5.模型选择  

参考config.py文件



## 6.构建docker镜像 

``` Bash
##下载Anaconda3 python 环境安装包（https://repo.anaconda.com/archive/Anaconda3-2019.03-Linux-x86_64.sh） 放置在chineseocr目录下   
##建立镜像   
docker build -t chineseocr .   
##启动服务   
docker run -d -p 8080:8080 chineseocr /root/anaconda3/bin/python app.py

```



## 7.web服务启动

``` Bash
cd chineseocr## 进入chineseocr目录
python app.py 8080 ##8080端口号，可以设置任意端口
```



## 8.疑难问题

### 1.web.py抛出异常

```bash
RuntimeError: generator raised StopIteration
```

【解决方法】

1. 修改site-packages中的web/utils.py文件

   第524行：

   ```python
   def take(seq, n):
   	for i in range(n):
   		yield next(seq)	
   ```

   改为：

   ```python
   def take(seq, n):
       try:
           yield next(seq)
       except StopIteration:
           return
   ```

   

2. 修改site-packages中的web/httpserver.py文件

   第230行：

   ```python
   self.start_response = start_response
   ```

   后面增加一行：

   ```python
   self.directory = os.getcwd() ## add
   ```

### 2.opencv版本的ocr模型无法使用



## 9.访问服务

http://127.0.0.1:8080/ocr



## 10.参考
1. yolo3 https://github.com/pjreddie/darknet.git   
2. crnn  https://github.com/meijieru/crnn.pytorch.git              
3. ctpn  https://github.com/eragonruan/text-detection-ctpn    
4. CTPN  https://github.com/tianzhi0549/CTPN       
5. keras yolo3 https://github.com/qqwweee/keras-yolo3.git    
6. darknet keras 模型转换参考 参考：https://www.cnblogs.com/shouhuxianjian/p/10567201.html  
7. 语言模型实现 https://github.com/lukhy/masr


## 11.技术支持   
mail:chineseocr@hotmail.com  
wechat:lywen52  
