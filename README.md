# pdf_chatbot_llama2_vectorstore_chainlit

### 0. 下載程式碼
```
git clone https://github.com/c00cjz00/pdf_chatbot_llama2_vectorstore_chainlit.git
```

### 1. 放置 PDF 於data 資料夾 (預設已放置, 不需要執行, 除非你要自己增加文件)
```
cd pdf_chatbot_llama2_vectorstore_chainlit
mkdir data
cp Medical_Chatbot.pdf data/
```
### 2. 利用singularity啟動程式 (請自行修改第四行的port)
```
ps aux | grep chainlit | awk '{print $2}' | xargs kill -9 
ml libs/singularity/3.10.2
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.0.1-cuda11.7-cudnn8-runtime.sif pip3 install -r requirements.txt
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.0.1-cuda11.7-cudnn8-runtime.sif python3 ingest.py
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.0.1-cuda11.7-cudnn8-runtime.sif ~/.local/bin/chainlit run model.py --port 9000
```

### 3. ssh forwarding (修改gn1101為你機器的hostname, 根據項目2 修改9000 為你的port)
a. ssh forwarding
```
ssh -L 9000:gn1101:9000 xxxx@ln01.twcc.ai
```

b. 打開本地端瀏覽器
```
http://localhost:9000
```

### 4. 問題範例
```
# 針灸
ware is Acupuncture?
Give me some BOOKS about Acupuncture.

# 過敏
What are Allergies?

# 穴位
請列出跟肺臟有關的穴道位置
```
