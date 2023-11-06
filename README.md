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
### 2 利用singularity啟動程式, 請選擇2.1 or 2.2
#### 2.1 模型為Llama2 7B (請自行修改第五行的port)
```
ps aux | grep chainlit | awk '{print $2}' | xargs kill -9 
ml libs/singularity/3.10.2
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif pip install -r requirements.txt
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif python3 ingest.py
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif ~/.local/bin/chainlit run model.py --port 9000
```
#### 2.2 模型為Llama2 7B GPTQ (請自行修改第六行的port)
```
ps aux | grep chainlit | awk '{print $2}' | xargs kill -9 
ml libs/singularity/3.10.2
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif pip install -r requirements.txt
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif pip install auto-gptq --extra-index-url https://huggingface.github.io/autogptq-index/whl/cu118/  # Use cu117 if on CUDA 11.7
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif python3 ingest.py
singularity exec --nv -B /work /work/u00cjz00/nvidia/pytorch_2.1.0-cuda11.8-cudnn8-devel.sif ~/.local/bin/chainlit run model_gptq.py --port 9000
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

### 4 利用slurm啟動程式
```
# 1. 登入 T2, 建議使用mobaxterm  
ssh c00cjz00 ln01.twcc.ai

# 2. 建立一個slurm log 紀錄目錄, 並進入此目錄
mkdir -p ~/genai_log; cd ~/genai_log

# 3. 輸入派送工作指令, 請更改計畫代號MST110386 及時間0-1:00:00 (一小時)
sbatch -A MST110386 --time=0-1:00:00 /work/u00cjz00/slurm_jobs/github/pdf_chatbot_llama2_vectorstore_chainlit/genai.slurm

# 4. 請打開slurm log 紀錄目錄裡, 最新一筆紀錄檔 genai_xxxxxxx.out, (第一次執行請等3啟動後約五分鐘後再執行4動作, 之後每一次約等一分鐘)
****************  請輸入下方指令  *****************
### STEP1: Execute cmd in your client below
ssh -L 48580:gn0416:48580 g00cjz00@ln01.twcc.ai
### STEP2: Open url below
http://localhost:48580/
### STEP3: 接續STEP1畫面, 進入計算節點, 觀看計算資源狀況 (OPTION)
ssh $(squeue -u $(whoami)|grep _t2g_  | awk '{print $8}')
watch -n0 nvidia-smi
### STEP4: 刪除計算資源(OPTION)
 scancel $(squeue -u $(whoami)|grep _t2g_  | awk '{print $1}')

# 5. chainlit 執行結果, 儲存於下方目錄
/work/$(whoami)/chainlit_demo
```


### 4 利用slurm啟動程式 並採用自己的PDF (更新步驟3)
```
# 1. 登入 T2, 建議使用mobaxterm  
ssh c00cjz00 ln01.twcc.ai

# 2. 建立一個slurm log 紀錄目錄, 並進入此目錄
mkdir -p ~/genai_log; cd ~/genai_log

# 3. 輸入派送工作指令, 請更改計畫代號MST110386 及時間0-1:00:00 (一小時),  並將/work/u00cjz00/slurm_jobs/github/pdf 改為你放置PDF的資料夾
#sbatch -A MST110386 --time=0-1:00:00 /work/u00cjz00/slurm_jobs/github/pdf_chatbot_llama2_vectorstore_chainlit/genai.slurm
sbatch -A MST110386 --time=0-1:00:00 --export=PDF_FOLDER=/work/u00cjz00/slurm_jobs/github/pdf /work/u00cjz00/slurm_jobs/github/pdf_chatbot_llama2_vectorstore_chainlit/genai_ur_pdf.slurm

# 4. 請打開slurm log 紀錄目錄裡, 最新一筆紀錄檔 genai_xxxxxxx.out, (第一次執行請等3啟動後約五分鐘後再執行4動作, 之後每一次約等一分鐘)
****************  請輸入下方指令  *****************
### STEP1: Execute cmd in your client below
ssh -L 48580:gn0416:48580 g00cjz00@ln01.twcc.ai
### STEP2: Open url below
http://localhost:48580/
### STEP3: 接續STEP1畫面, 進入計算節點, 觀看計算資源狀況 (OPTION)
ssh $(squeue -u $(whoami)|grep _t2g_  | awk '{print $8}')
watch -n0 nvidia-smi
### STEP4: 刪除計算資源(OPTION)
 scancel $(squeue -u $(whoami)|grep _t2g_  | awk '{print $1}')

# 5. chainlit 執行結果, 儲存於下方目錄
/work/$(whoami)/chainlit_demo
```

### 5. 問題範例
```
# 針灸
What is Acupuncture?
Give me some BOOKS about Acupuncture.

# 過敏
What are Allergies?

# 穴位
請列出跟肺臟有關的穴道位置
```
