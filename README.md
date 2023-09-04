# local-llms
Notes and examples on running local LLMs


# Quick Links

* https://github.com/facebookresearch/llama
* https://github.com/facebookresearch/llama#fine-tuned-chat-models

* https://huggingface.co/meta-llama

* https://github.com/ggerganov/llama.cpp
* https://replicate.com/blog/how-to-prompt-llama

* https://github.com/abetlen/llama-cpp-python

* https://github.com/nomic-ai/gpt4all
* https://github.com/nomic-ai/gpt4all/blob/main/gpt4all-bindings/python

* https://github.com/simonw/llm
* https://llm.datasette.io/en/stable/




# Conda environment

``` bash
conda create --name <env-name> python=3.11
```


# NVIDIA CUDA dev stack

* https://twitter.com/jeremyphoward/status/1697435241152127369?s=20



# git-lfs

``` bash
curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.deb.sh | sudo bash
sudo apt-get install git-lfs
git lfs install
```

# download llama models from HF

Make sure that git-lfs is installed.

``` bash
git clone https://huggingface.co/meta-llama/Llama-2-7b
```



# Quantizing models

This guy quantizes a lot and puts them on HF
* https://huggingface.co/TheBloke





# repos

## https://github.com/ggerganov/llama.cpp

### install

``` bash
git clone https://github.com/ggerganov/llama.cpp
cd llama.cpp
make
pip install -r requirements.txt
```

### convert to gguf

convert models downloaded from HF to gguf format,

``` bash
python convert.py /mnt/disks/data2/hf-model-repos/Llama-2-7b
```

this will produce

``` bash
/mnt/disks/data2/hf-model-repos/Llama-2-7b/ggml-model-f16.gguf
```

### quantize

``` bash
./quantize /mnt/disks/data2/hf-model-repos/Llama-2-7b/ggml-model-f16.gguf /mnt/disks/data2/hf-model-repos/Llama-2-7b/ggml-model-Q4_0.gguf Q4_0
```

The options are,

``` bash
Allowed quantization types:
   2  or  Q4_0   :  3.56G, +0.2166 ppl @ LLaMA-v1-7B
   3  or  Q4_1   :  3.90G, +0.1585 ppl @ LLaMA-v1-7B
   8  or  Q5_0   :  4.33G, +0.0683 ppl @ LLaMA-v1-7B
   9  or  Q5_1   :  4.70G, +0.0349 ppl @ LLaMA-v1-7B
  10  or  Q2_K   :  2.63G, +0.6717 ppl @ LLaMA-v1-7B
  12  or  Q3_K   : alias for Q3_K_M
  11  or  Q3_K_S :  2.75G, +0.5551 ppl @ LLaMA-v1-7B
  12  or  Q3_K_M :  3.07G, +0.2496 ppl @ LLaMA-v1-7B
  13  or  Q3_K_L :  3.35G, +0.1764 ppl @ LLaMA-v1-7B
  15  or  Q4_K   : alias for Q4_K_M
  14  or  Q4_K_S :  3.59G, +0.0992 ppl @ LLaMA-v1-7B
  15  or  Q4_K_M :  3.80G, +0.0532 ppl @ LLaMA-v1-7B
  17  or  Q5_K   : alias for Q5_K_M
  16  or  Q5_K_S :  4.33G, +0.0400 ppl @ LLaMA-v1-7B
  17  or  Q5_K_M :  4.45G, +0.0122 ppl @ LLaMA-v1-7B
  18  or  Q6_K   :  5.15G, -0.0008 ppl @ LLaMA-v1-7B
   7  or  Q8_0   :  6.70G, +0.0004 ppl @ LLaMA-v1-7B
   1  or  F16    : 13.00G              @ 7B
   0  or  F32    : 26.00G              @ 7B
          COPY   : only copy tensors, no quantizing
```

The notes are,

``` bash
    GGML_TYPE_Q2_K - "type-1" 2-bit quantization in super-blocks containing 16 blocks, each block having 16 weight. Block scales and mins are quantized with 4 bits. This ends up effectively using 2.5625 bits per weight (bpw)
    GGML_TYPE_Q3_K - "type-0" 3-bit quantization in super-blocks containing 16 blocks, each block having 16 weights. Scales are quantized with 6 bits. This end up using 3.4375 bpw.
    GGML_TYPE_Q4_K - "type-1" 4-bit quantization in super-blocks containing 8 blocks, each block having 32 weights. Scales and mins are quantized with 6 bits. This ends up using 4.5 bpw.
    GGML_TYPE_Q5_K - "type-1" 5-bit quantization. Same super-block structure as GGML_TYPE_Q4_K resulting in 5.5 bpw
    GGML_TYPE_Q6_K - "type-0" 6-bit quantization. Super-blocks with 16 blocks, each block having 16 weights. Scales are quantized with 8 bits. This ends up using 6.5625 bpw
```

### run

``` bash
./main -m /mnt/disks/data2/hf-model-repos/Llama-2-7b/ggml-model-f16.gguf -n 16 -p "The capital of France is"
```


``` bash
./main -m /mnt/disks/data2/hf-model-repos/Llama-2-7b-chat/ggml-model-f16.gguf -n 16 -p "[INST] What is the capital of France? [/INST]"
```
