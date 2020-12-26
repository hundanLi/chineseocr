## CPU环境配置

### python虚拟环境

```bash
## 运用conda 创建python环境
conda create -n .venv python=3.6 pip scipy numpy jupyter ipython

## 使用venv 创建虚拟环境
python -m venv .venv
```

### 依赖库安装

```bash
# 如果使用darknet运行yolo
git clone https://github.com/pjreddie/darknet.git
cd darknet/ && make && cd ..

# 激活虚拟环境
source .venv/bin/activate

# 通用库
pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple/
# 如果使用keras的ocr模型
pip install keras==2.1.5 tensorflow==1.8
# 如果使用pytorch的ocr模型
pip install torch==1.7.1+cpu torchvision==0.8.2+cpu torchaudio==0.7.2 -f https://download.pytorch.org/whl/torch_stable.html
## 如果使用conda
conda install pytorch-cpu torchvision-cpu -c pytorch
```

