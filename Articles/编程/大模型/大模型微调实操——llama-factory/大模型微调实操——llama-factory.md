源文件：![(1.1.4)-llama-factory微调.pdf](./大模型微调实操——llama-factory.assert/1746951096298-0f185e66-a89d-4e09-a196-a2206c6cb0f0.pdf)

## llama-factory环境安装
### 前置准备

1. **英伟达显卡驱动更新地址**：[https://www.nvidia.cn/Download/index.aspx?lang=cn](https://www.nvidia.cn/Download/index.aspx?lang=cn)
2. **cuda下载安装地址**：[https://developer.nvidia.com/cuda-12-2-0-download-archive/](https://developer.nvidia.com/cuda-12-2-0-download-archive/)
3. **pytorch下载安装地址**：[https://pytorch.org/get-started/previous-versions/](https://pytorch.org/get-started/previous-versions/)
4. **llama-factory项目和文档地址**：
- 项目：[https://github.com/hiyouga/LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)
- 文档：[https://llamafactory.readthedocs.io/zh-cn/latest/getting_started/installation.html](https://llamafactory.readthedocs.io/zh-cn/latest/getting_started/installation.html)
1. **python环境下载地址**：[https://www.python.org/downloads/](https://www.python.org/downloads/)
2. **miniconda下载地址**：[https://docs.anaconda.com/miniconda/](https://docs.anaconda.com/miniconda/)
3. **git下载地址**：[https://git-scm.com/downloads](https://git-scm.com/downloads)### 硬件环境校验

1. **显卡驱动和CUDA的安装**：访问[https://www.nvidia.cn/Download/index.aspx?lang=cn和https://developer.nvidia.com/cuda-12-2-0-download-archive/进行安装。](https://www.nvidia.cn/Download/index.aspx?lang=cn和https://developer.nvidia.com/cuda-12-2-0-download-archive/进行安装。)
2. **校验命令**：
- 使用```nvidia-smi
```
命令进行简单校验。
- 打开cmd输入```nvcc -V
```
，若出现相关内容则安装成功。### 软件环境准备

1. **拉取LLaMA-Factory代码**：
- 若没有git，先从[https://git-scm.com/downloads安装。也可直接下载代码解压缩。](https://git-scm.com/downloads安装。也可直接下载代码解压缩。)
- 运行指令：
```bash
git clone --depth 1 https://github.com/hiyouga/LLaMA-Factory.git
cd LLaMA-Factory
pip install -e ".[torch,metrics]"
```

1. **创建虚拟环境**：
- 建议使用conda创建虚拟环境，需先安装conda或miniconda。
- **conda创建环境**：
```bash
conda create -n llama_factory python=3.10
conda activate llama_factory
conda install pytorch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 pytorch-cuda=12.1 -c pytorch -c nvidia
```

```plain
- **venv创建环境（在LLaMA-Factory目录创建python310子目录为例）**：
```

```bash
python -m venv python310
python310/Scripts/activate
```

1. **量化环境**：
- 在Windows上启用量化LoRA(QLoRA)，根据CUDA版本选择适当的bitsandbytes发行版本，如：
```bash
pip install https://github.com/jllllll/bitsandbytes-windows-webui/releases/download/wheels/bitsandbytes-0.41.2.post2-py3-none-win_amd64.whl
```

```plain
- QLoRA最好安装cuda11.8以上版本（如12.1），使用AWQ等量化算法的基础模型时，cuda11.8可能出现pytorch错误。
- 若大模型使用awq量化，需安装autoawq模块：
```

```bash
pip install autoawq
```

1. **安装后校验**：
```python
import torch
torch.cuda.current_device()
torch.cuda.get_device_name(0)
torch.__version__
```
若识别不到可用的GPU，说明环境准备有问题，需处理后再继续。5. **硬件需求参考**：[https://github.com/hiyouga/LLaMA-Factory?tab=readme-ov-file#hardware-requirement](https://github.com/hiyouga/LLaMA-Factory?tab=readme-ov-file#hardware-requirement)
### 启动LLaMA-Factory

1. **校验安装**：输入```llamafactory-cli train -h
```
获取训练相关参数指导，若无法获取则说明库未安装成功。```llamafactory-cli
```
命令在使用的python虚拟环境的scripts目录下，正常激活虚拟环境后可使用。
2. **启动webui**：
```bash
llamafactory-cli webui
# 或指定GPU设备
CUDA_VISIBLE_DEVICES=0 llamafactory-cli webui
```
目前webui版本只支持单机单卡和单机多卡，多机多卡请使用命令行版本。3. **开启gradio的share功能或修改端口号**：

```bash
CUDA_VISIBLE_DEVICES=0 GRADIO_SHARE=1 GRADIO_SERVER_PORT=7860 llamafactory-cli webui
```
## 手动下载模型

1. **以Meta-Llama-3-8B-Instruct为例**：
- 通过huggingface下载（可能需先提交申请通过）：
```bash
git clone https://huggingface.co/meta-llama/Meta-Llama-3-8B-Instruct
```

```plain
- chat版本下载地址：https://huggingface.co/shenzhi-wang/Llama3-8B-Chinese-Chat/tree/main
- modelscope下载（适合中国大陆网络环境）：
```

```bash
git clone https://www.modelscope.cn/LLM-Research/Meta-Llama-3-8B-Instruct.git
```

1. **代码下载模型（用modelscope库）**：
```python
from modelscope import snapshot_download
# linux系统
# local_dir = "/LLaMA-Factory/Qwen2-1.5B-Instruct"
# windows系统
model_dir = "F:/sotaAI/LLaMA-Factory/Qwen2-1.5B-Instruct"
model_dir = snapshot_download('qwen/Qwen2-1.5B-Instruct',local_dir=local_dir)
```
## 使用transformer编写推理代码

```python
import transformers
import torch

# 切换为你下载的模型文件目录，这里的demo是Qwen2-1.5B-Instruct
# 如果是其他模型，比如llama3,chatglm，请使用其对应的官方demo
# linux系统
# model_id = "/LLaMA-Factory/Qwen2-1.5B-Instruct"
# windows系统
model_id = "F:/sotaAI/LLaMA-Factory/Qwen2-1.5B-Instruct"
pipeline = transformers.pipeline(
    "text-generation",
    model=model_id,
    model_kwargs={"torch_dtype": torch.bfloat16},
    device_map="auto",
)
messages = [
    {"role": "system", "content": "你是一个电商客服,专业回答售后问题"},
    {"role": "user", "content": "你们这儿包邮吗?"},
]
prompt = pipeline.tokenizer.apply_chat_template(
    messages,
    tokenize=False,
    add_generation_prompt=True
)
# 不同模型的eos_token_id不同，比如llama3,"<|eot_id|>"
terminators = [
    pipeline.tokenizer.eos_token_id,
    pipeline.tokenizer.convert_tokens_to_ids("<|im_end|>")
]
outputs = pipeline(
    prompt,
    max_new_tokens=256,
    eos_token_id=terminators,
    do_sample=True,
    temperature=0.6,
    top_p=0.9,
)
print(outputs[0]["generated_text"][len(prompt):])
```
## 微调数据集

1. **偏好数据**：sft微调一般用alpaca格式，dpo优化的偏好数据一般用sharegpt格式。
2. **DPO优化偏好数据集示例**：[https://huggingface.co/datasets/hiyouga/DPO-En-Zh-20k/viewer/zh?row=5](https://huggingface.co/datasets/hiyouga/DPO-En-Zh-20k/viewer/zh?row=5)
3. **英伟达开源的HelpSteer2**：
- 数据集：[https://huggingface.co/datasets/nvidia/HelpSteer2](https://huggingface.co/datasets/nvidia/HelpSteer2)
- 论文：[https://arxiv.org/pdf/2406.08673](https://arxiv.org/pdf/2406.08673)
1. **数据集注册（以甄嬛传语料为例）**：下载alpaca格式的huanhuan.json数据集，下载地址：[https://www.modelscope.cn/datasets/longgeai3x3/huanhuan-chat/files](https://www.modelscope.cn/datasets/longgeai3x3/huanhuan-chat/files)## 微调过程
### 参数解析与微调命令

1. **微调命令示例**：
```bash
llamafactory-cli train \
--stage sft \
--do_train True \
--model_name_or_path /data1/models/Llama3-8B-Chinese-Chat \
--preprocessing_num_workers 16 \
--finetuning_type lora \
--template llama3 \
--flash_attn auto \
--dataset_dir /data1/workspaces/llama-factory/data/fiance-neixun \
--dataset yinlian-sharegpt-neixun \
--cutoff_len 1024 \
--learning_rate 5e-05 \
--num_train_epochs 3.0 \
--max_samples 100000 \
--per_device_train_batch_size 2 \
--gradient_accumulation_steps 8 \
--lr_scheduler_type cosine \
--max_grad_norm 1.0 \
--logging_steps 5 \
--save_steps 100 \
--warmup_steps 0 \
--optim adamw_torch \
--packing False \
--report_to none \
--output_dir saves/LLaMA3-8B-Chat/lora/train_2024-06-18-09-02-25 \
--fp16 True \
--plot_loss True \
--ddp_timeout 180000000 \
--include_num_input_tokens_seen True \
--lora_rank 8 \
--lora_alpha 16 \
--lora_dropout 0 \
--lora_target all \
--deepspeed cache/ds_z3_config.json
```

1. **使用ymal文件**：可将参数按格式保存为ymal文件，参考根目录下examples文件夹下的例子，使用方式如下：
```bash
llamafactory-cli train examples/train_lora/llama3_lora_sft.yaml
```

1. **Windows下命令**：在cmd窗口中，把命令中```\
```
和换行删掉，当成一行命令即可。### 中断继续训练

1. **继续训练命令**：
```bash
--resume_from_checkpoint /workspace/checkpoint/codellama34b_5k_10epoch/checkpoint-4000
--output_dir new_dir
# --resume_lora_training #这个可以不设置
```
若不需要换output_dir，另外两条命令都不加，脚本会自动寻找最新的checkpoint。2. **生成loss图**：使用命令训练时，加```--plot_loss
```
参数可生成loss图，训练结束后，loss图会保存在```--output_dir
```
指定的目录中。3. **用新数据集继续训练**：可通过添加命令从检查点开始继续训练，但训练集会从头开始训练。
## 模型评估

1. **大模型主流评测benchmark**：完成模型训练后，可通过```llamafactory-cli eval examples/train_lora/llama3_lora_eval.yaml
```
评估模型效果。
2. **配置示例文件（examples/train_lora/llama3_lora_eval.yaml）**：
```yaml
### model
model_name_or_path: meta-llama/Meta-Llama-3-8B-Instruct
adapter_name_or_path: saves/llama3-8b/lora/sft # 可选项
### method
finetuning_type: lora
### dataset
task: mmlu_test
template: fewshot
lang: en
n_shot: 5
### output
save_dir: saves/llama3-8b/lora/eval
### eval
batch_size: 4
```

1. **Chat版本模型评测命令（CUDA_VISIBLE_DEVICES=0）**：
```bash
llamafactory-cli eval \
--model_name_or_path /llama3/Meta-Llama-3-8B-Instruct \
--template llama3 \
--task mmlu_test \
--lang en \
--n_shot 5 \
--batch_size 1
```

1. **Windows下评测命令实例**：
```bash
llamafactory-cli eval --model_name_or_path Qwen/Qwen2-7B-Instruct-AWQ --adapter_name_or_path F:\sotaAI\LLaMA-Factory\saves\Qwen2-7B-int4-Chat\lora\train_2024-08-18-14-43-59 --finetuning_type lora --template qwen --task cmmlu_test --lang zh --n_shot 3 --batch_size 1
```

1. **大语言模型评估集**：两个开源自动化评测项目：
- [https://github.com/open-compass/opencompass](https://github.com/open-compass/opencompass)
- [https://github.com/EleutherAI/lm-evaluation-harness/tree/main](https://github.com/EleutherAI/lm-evaluation-harness/tree/main)## 批量推理
### 环境准备
安装相关库：

```bash
pip install jieba #中文文本分词库
pip install rouge-chinese
pip install nltk #自然语言处理工具包(Natural Language Toolkit)
```
### 参数解释与推理示例

1. **批量推理命令例子**：
```bash
CUDA_VISIBLE_DEVICES=0 llamafactory-cli train \
--stage sft \
--do_predict \
--model_name_or_path /llama3/Meta-Llama-3-8B-Instruct \
--adapter_name_or_path ./saves/LLaMA3-8B/lora/sft \
--eval_dataset alpaca_gpt4_zh,identity,adgen_local \
--dataset_dir ./data \
--template llama3 \
--finetuning_type lora \
--output_dir ./saves/LLaMA3-8B/lora/predict \
--overwrite_cache \
--overwrite_output_dir \
--cutoff_len 1024 \
--preprocessing_num_workers 16 \
--per_device_eval_batch_size 1 \
--max_samples 20 \
--predict_with_generate
```

1. **推理示例（Windows下的测试命令）**：
```bash
llamafactory-cli train --stage sft --model_name_or_path Qwen/Qwen2-7B-Instruct-AWQ --preprocessing_num_workers 16 --finetuning_type lora --quantization_method bitsandbytes --template qwen --flash_attn auto --dataset_dir data --eval_dataset huanhuan_chat,ruozhiba_gpt4 --cutoff_len 1024 --max_samples 20 --per_device_eval_batch_size 2 --predict_with_generate True --max_new_tokens 512 --top_p 0.7 --temperature 0.95 --output_dir saves\Qwen2-7B-int4-Chat\lora\eval_2024-08-24-10-42-52 --do_predict True --adapter_name_or_path saves\Qwen2-7B-int4-Chat\lora\train_2024-08-18-14-43-59 --quantization_bit 4
```
## 模型部署
### LoRA模型合并导出

1. **导出命令**：
```bash
CUDA_VISIBLE_DEVICES=0 llamafactory-cli export \
--model_name_or_path /llama3/Meta-Llama-3-8B-Instruct \
--adapter_name_or_path ./saves/LLaMA3-8B/lora/sft \
--template llama3 \
--finetuning_type lora \
--export_dir megred-model-path \
--export_size 2 \
--export_device cpu \
--export_legacy_format False
```

1. **参数说明**：
- ```model_name_or_path
```
：预训练模型的名称或路径。
- ```template
```
：模型模板。
- ```export_dir
```
：导出路径。
- ```export_quantization_bit
```
：量化位数，全精度导出时，不用填写。
- ```export_quantization_dataset
```
：量化校准数据集。
- ```export_size
```
：最大导出模型文件大小，如果模型权重大小比较大，就会分成多个文件导出。
- ```export_device
```
：导出设备。
- ```export_legacy_format
```
：是否使用旧格式导出。### 导出GGUF

1. **安装gguf库**：从源码安装llama.cpp：
```bash
conda create -n llama_cpp python=3.10
conda activate llama_cpp
conda install pytorch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 pytorch-cuda=12.1 -c pytorch -c nvidia
git clone https://github.com/ggerganov/llama.cpp.git
cd llama.cpp
pip install --editable.
```

1. **格式转换**：在llama.cpp根目录，使用官方提供的```convert-hf-to-gguf.py
```
脚本进行转换：
```bash
python convert_hf_to_gguf.py F:\sotaAI\LLaMA-Factory\saves\export #需要转换的模型路径
```
### ollama安装

1. **下载地址**：[https://ollama.com/](https://ollama.com/)
2. **项目信息**：
- 基于Go语言开发的开源项目，其github地址为[https://github.com/ollama/ollama](https://github.com/ollama/ollama) 。
- 相关文档可参考[https://github.com/ollama/ollama/tree/main/docs](https://github.com/ollama/ollama/tree/main/docs) 。
- ollama仅支持gguf文件格式，若使用其他格式模型，需先进行转换。
1. **Linux安装步骤**：
- 线上GPU算力服务器通常已安装驱动，但AMD的amdgpu驱动程序若版本过旧，可能无法支持所有ROCm功能，建议从[https://www.amd.com/en/support/linux-drivers](https://www.amd.com/en/support/linux-drivers) 安装最新驱动。
- **一键安装**：运行```curl -fsSL https://ollama.com/install.sh | sh
```
即可完成安装。
- **手动安装**：执行以下命令：
```bash
sudo curl -L https://ollama.com/download/ollama-linux-amd64 -o /usr/bin/ollama
sudo chmod +x /usr/bin/ollama
```

```plain
- **设置为启动服务（推荐操作）**：
```

```bash
# 创建ollama用户
sudo useradd -r -s /bin/false -m -d /usr/share/ollama ollama
```

```plain
- 在`/etc/systemd/system/ollama.service`中创建服务文件，内容如下：
```

```properties
[Unit]
Description=Ollama Service
After=network-online.target
[Service]
ExecStart=/usr/bin/ollama serve
User=ollama
Group=ollama
Restart=always
RestartSec=3
[Install]
WantedBy=default.target
```

```plain
- 随后执行以下命令启动服务：
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable ollama
sudo systemctl start ollama
```

```plain
- 若要移除ollama服务，可执行：
```

```bash
sudo systemctl stop ollama
sudo systemctl disable ollama
sudo rm /etc/systemd/system/ollama.service
```

1. **Windows安装步骤**：
- 无需WSL，可直接从[https://ollama.com/download](https://ollama.com/download) 选择Windows系统版本进行安装启动。
- 默认安装位置：
- **程序文件目录**：```C:\Users\Administrator\AppData\Local\Programs\Ollama
```

- **日志文件夹**：```C:\Users\Administrator\AppData\Local\Ollama
```

- **模型和数据文件夹**：```C:\Users\Administrator\.ollama
```
 （可通过设置```OLLAMA_MODELS
```
环境变量更改）
- **系统要求**：
- 操作系统需为Windows 10或更新版本（家庭版或专业版）。
- 若使用NVIDIA显卡，需安装452.39或更新版本的驱动程序，下载地址为[https://www.nvidia.cn/Download/index.aspx?lang=cn](https://www.nvidia.cn/Download/index.aspx?lang=cn) 。
- 若使用Radeon显卡，则需安装AMD Radeon驱动程序，可从[https://www.amd.com/en/support](https://www.amd.com/en/support) 获取。
1. **启动与基本命令使用**：
- 安装Windows预览版后，ollama会在后台运行，可在cmd、powershell或其他终端中使用```ollama
```
命令行。
- **启动服务**：执行```./ollama serve
```
 。
- **加载模型**：如加载```gemma2
```
模型的2b版本，执行```./ollama run gemma2:2b
```
 。若模型未下载，系统会自动下载到```OLLAMA_MODELS
```
环境变量指定目录（未设置则为默认目录）。
1. **环境变量设置**：
- ```OLLAMA_MODELS
```
：用于自定义模型目录，若不设置，模型默认安装在```C:\Users\文件夹下
```
 。例如设置为```OLLAMA_MODELS=d:/ollama/models
```
 。
- ```OLLAMA_BASE_URL
```
：用于自定义ollama api服务的url和端口，默认是```http://127.0.0.1:11434
```
 ，可修改为如```http://127.0.0.1:自定义端口
```
 。
1. **ollama命令详细介绍**：
- **官方支持模型操作**：
- **下载模型**：使用```ollama pull llama3.1
```
 ，可下载名为```llama3.1
```
的模型。
- **删除模型**：执行```ollama rm llama3.1
```
 ，可删除已下载的```llama3.1
```
模型。
- **显示模型信息**：运行```ollama show llama3.1
```
 ，可查看```llama3.1
```
模型的详细信息。
- **列出已下载模型**：通过```ollama list
```
 ，可列出当前已下载到本地的所有模型。
- **自定义模型操作**：
- 需新建```Modelfile
```
文件，示例内容如下（假设路径为```d:/ollama/longgemf
```
 ）：
```plain
FROM llama3.1 #官方支持的模型
#FROM d:/ollama/llama3-huanhuan.gguf #自定义支持的模型，需指定路径加模型文件名
#温度参数（可选），参数越大，分布曲线越平缓
PARAMETER temperature 1
#系统提示词（可选）
SYSTEM """
You are Mario from Super Mario Bros. Answer as Mario, the assistant, only.
"""
```

```plain
    - **注册模型**：执行`ollama create huanhuan -f d:/ollama/longgemf` （`-f`后接自定义的`modelfile`文件路径），可注册名为`huanhuan`的模型。
- **命令聊天示例**：运行`ollama run huanhuan` ，启动名为`huanhuan`的模型进行交互，输入问题即可获取回答，如输入“你是谁”，模型可能回复“我是甄嬛，家父是大理寺少卿甄远道” 。
```

1. **实验示例**：
- **设置模型目录环境变量**：```OLLAMA_MODELS=F:\sotaAI\ollama-openwebui\llm_models
```
 。
- ```longgemf
```
这个```modelfile
```
文件内容：```FROM F:\sotaAI\LLaMA-Factory\saves\export\Qwen2-0.5B-F16.gguf
```
 。
- **运行命令注册huanhuan**：```ollama create huanhuan -f F:\sotaAI\ollama-openwebui\llm_models\longgemf
```
 。
- **使用huanhuan进行推理**：```ollama run huanhuan
```
 。### open-webui本地模型部署ui项目
如果需要在本地电脑部署一个带有UI界面的大模型项目，以便管理模型、文档资料，并实现类似GPT的聊天功能，可以安装open-webui项目。它是一个开源的本地模型推理的webui项目，后端与ollama兼容。

- **项目下载地址**：[https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui)### API调用服务

1. **llama-factory的api服务**：训练好模型后，若想将模型能力通过API调用，接入到langchain或其他下游业务中，llama-factory项目提供了此功能。其API参考OpenAI的相关接口协议，基于uvicorn服务框架开发，启动方式如下：
```bash
CUDA_VISIBLE_DEVICES=0 API_PORT=8000 llamafactory-cli api \
--model_name_or_path /llama3/Meta-Llama-3-8B-Instruct \
--adapter_name_or_path ./saves/LLaMA3-8B/lora/sft \
--template llama3 \
--finetuning_type lora
```
若要加速推理，可使用vllm推理后端，但vllm仅支持Linux系统。使用时，需要提前将LoRA模型进行merge，可使用merge后的完整版模型目录或训练前的模型原始目录，启动命令如下：

```bash
CUDA_VISIBLE_DEVICES=0 API_PORT=8000 llamafactory-cli api \
--model_name_or_path megred-model-path \
--template llama3 \
--infer_backend vllm \
--vllm_enforce_eager
```
服务启动后，按照OpenAI的API进行远程访问，主要是替换其中的```base_url
```
，指向所部署机器的url和端口号。示例代码如下：

```python
import os
from openai import OpenAI
from transformers.utils.versions import require_version
require_version("openai>=1.5.0", "To fix: pip install openai>=1.5.0")

if __name__ == '__main__':
    # 可自定义端口
    port = 8000
    client = OpenAI(
        api_key="0",
        base_url="http://localhost:{}/v1".format(os.environ.get("API_PORT", 8000)),
    )
    messages = []
    messages.append({"role": "user", "content": "hello, where is USA"})
    result = client.chat.completions.create(messages=messages, model="test")
    print(result.choices[0].message)
```

1. **ollama的api服务**：启动ollama服务后，可通过API调用来进行推理。API的url可通过环境变量```OLLAMA_BASE_URL
```
指定，默认是```http://127.0.0.1:11434
```
。支持流式生成或非流式生成，通过post请求生成聊天内容，如```POST /api/generate
```
、```POST /api/chat
```
 。详细API文档可参考：[https://github.com/ollama/ollama/blob/main/docs/api.md](https://github.com/ollama/ollama/blob/main/docs/api.md) 。
2. **openai兼容api**：ollama支持openai兼容的api接口，国内大部分闭源模型也支持该接口。这样无需重复开发多套接口，直接在原有模型支持的基础上，更换模型和api url地址，即可支持ollama模型推理。示例代码如下：
```python
import os
from openai import OpenAI
from transformers.utils.versions import require_version

# 检查openai库的版本，要求至少为1.5.0
require_version("openai>=1.5.0", "To fix: pip install openai>=1.5.0")

# 创建OpenAI客户端，设置API基础URL和API密钥（这里的密钥仅为占位，实际使用中可能不同）
client = OpenAI(
    base_url='http://localhost:11434/v1/',
    api_key='ollama'
)

# 使用llama3模型进行聊天
chat_completion = client.chat.completions.create(
    messages=[
        {
            "role": "user",
            "content": "Say this is a test"
        }
    ],
    model='llama3'
)
print("llama3聊天结果:", chat_completion.choices[0].message.content)

# 使用llava模型进行多模态任务，分析图片内容
response = client.chat.completions.create(
    model="llava",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image?"},
                {
                    "type": "image_url",
                    "image_url": "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"
                }
            ]
        }
    ],
    max_tokens=300
)
print("llava多模态分析结果:", response.choices[0].message.content)

# 使用llama3模型进行文本生成
completion = client.completions.create(
    model="llama3",
    prompt="Say this is a test"
)
print("llama3文本生成结果:", completion.choices[0].text)

# 获取模型列表
list_completion = client.models.list()
print("模型列表:", list_completion.data)

# 检索llama3模型
model = client.models.retrieve("llama3")
print("检索llama3模型结果:", model)

# 使用all-minilm模型生成文本嵌入向量
embeddings = client.embeddings.create(
    model="all-minilm",
    input=["why is the sky blue?", "why is the grass green?"]
)
print("文本嵌入向量:", embeddings.data)
```

