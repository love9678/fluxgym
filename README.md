# Flux Gym

Dead simple web UI for training FLUX LoRA **with LOW VRAM (12GB/16GB/20GB) support.**

- **Frontend:** The WebUI forked from [AI-Toolkit](https://github.com/ostris/ai-toolkit) (Gradio UI created by https://x.com/multimodalart)
- **Backend:** The Training script powered by [Kohya Scripts](https://github.com/kohya-ss/sd-scripts)

FluxGym supports 100% of Kohya sd-scripts features through an [Advanced](#advanced) tab, which is hidden by default.

![screenshot.png](screenshot.png)

---


# What is this?

1. I wanted a super simple UI for training Flux LoRAs
2. The [AI-Toolkit](https://github.com/ostris/ai-toolkit) project is great, and the gradio UI contribution by [@multimodalart](https://x.com/multimodalart) is perfect, but the project only works for 24GB VRAM.
3. [Kohya Scripts](https://github.com/kohya-ss/sd-scripts) are very flexible and powerful for training FLUX, but you need to run in terminal.
4. What if you could have the simplicity of AI-Toolkit WebUI and the flexibility of Kohya Scripts?
5. Flux Gym was born. Supports 12GB, 16GB, 20GB VRAMs, and extensible since it uses Kohya Scripts underneath.

---

# News

- September 25: Docker support + Autodownload Models (No need to manually download models when setting up) + Support custom base models (not just flux-dev but anything, just need to include in the [models.yaml](models.yaml) file.
- September 16: Added "Publish to Huggingface" + 100% Kohya sd-scripts feature support: https://x.com/cocktailpeanut/status/1835719701172756592
- September 11: Automatic Sample Image Generation + Custom Resolution: https://x.com/cocktailpeanut/status/1833881392482066638

---

# Supported Models

1. Flux1-dev
2. Flux1-dev2pro (as explained here: https://medium.com/@zhiwangshi28/why-flux-lora-so-hard-to-train-and-how-to-overcome-it-a0c70bc59eaf)
3. Flux1-schnell (Couldn't get high quality results, so not really recommended, but feel free to experiment with it)
4. More?

The models are automatically downloaded when you start training with the model selected.

You can easily add more to the supported models list by editing the [models.yaml](models.yaml) file. If you want to share some interesting base models, please send a PR.

---

# How people are using Fluxgym

Here are people using Fluxgym to locally train Lora sharing their experience:

https://pinokio.computer/item?uri=https://github.com/cocktailpeanut/fluxgym





## 1. 安裝

First clone Fluxgym and kohya-ss/sd-scripts:

```
git clone https://github.com/love9678/fluxgym.git
cd /workspace/fluxgym
git clone -b sd3 https://github.com/kohya-ss/sd-scripts
```

Linux:

```
python -m venv env
source env/bin/activate
```

這將在“fluxgym”資料夾正下方建立一個“env”資料夾：
```
/fluxgym
  app.py
  requirements.txt
  /sd-scripts
  /env
```

現在轉到“sd-scripts”資料夾並將依賴項安裝到已啟動的環境:

```
cd /workspace/fluxgym/sd-scripts
pip install -r requirements.txt
```

安裝應用程式依賴項:

```
cd /workspace/fluxgym 
pip install -r requirements.txt
```

2.最後，安裝 pytorch Nightly:

```
pip install --pre torch torchvision torchaudio --index-url https://pypi.tuna.tsinghua.edu.cn/whl/cu121
pip install --upgrade pip
```


# 3.開始

python app.py

> Linux: `source env/bin/activate`


4.下載模型:

pip install aria2

cd /workspace/fluxgym/models/clip

aria2c -c -x 16 -s 16 -k 50M https://hf-mirror.com/kines9661/dww/resolve/main/clip_l.safetensors -o clip_l.safetensors

cd /workspace/fluxgym-cn/models/clip

aria2c -c -x 16 -s 16 -k 50M https://hf-mirror.com/kines9661/dww/resolve/main/t5xxl_fp16.safetensors -o t5xxl_fp16.safetensors

cd /workspace/fluxgym-cn/models/vae

aria2c -c -x 16 -s 16 -k 50M https://hf-mirror.com/kines9661/dww/resolve/main/ae.sft -o ae.sft




# Usage

The usage is pretty straightforward:

1. Enter the lora info
2. Upload images and caption them (using the trigger word)
3. Click "start".

That's all!

![flow.gif](flow.gif)

# Configuration

## Sample Images

By default fluxgym doesn't generate any sample images during training.

You can however configure Fluxgym to automatically generate sample images for every N steps. Here's what it looks like:

![sample.png](sample.png)

To turn this on, just set the two fields:

1. **Sample Image Prompts:** These prompts will be used to automatically generate images during training. If you want multiple, separate teach prompt with new line.
2. **Sample Image Every N Steps:** If your "Expected training steps" is 960 and your "Sample Image Every N Steps" is 100, the images will be generated at step 100, 200, 300, 400, 500, 600, 700, 800, 900, for EACH prompt.

![sample_fields.png](sample_fields.png)

## Advanced Sample Images

Thanks to the built-in syntax from [kohya/sd-scripts](https://github.com/kohya-ss/sd-scripts?tab=readme-ov-file#sample-image-generation-during-training), you can control exactly how the sample images are generated during the training phase:

Let's say the trigger word is **hrld person.** Normally you would try sample prompts like:

```
hrld person is riding a bike
hrld person is a body builder
hrld person is a rock star
```

But for every prompt you can include **advanced flags** to fully control the image generation process. For example, the `--d` flag lets you specify the SEED.

Specifying a seed means every sample image will use that exact seed, which means you can literally see the LoRA evolve. Here's an example usage:

```
hrld person is riding a bike --d 42
hrld person is a body builder --d 42
hrld person is a rock star --d 42
```

Here's what it looks like in the UI:

![flags.png](flags.png)

And here are the results:

![seed.gif](seed.gif)

In addition to the `--d` flag, here are other flags you can use:


- `--n`: Negative prompt up to the next option.
- `--w`: Specifies the width of the generated image.
- `--h`: Specifies the height of the generated image.
- `--d`: Specifies the seed of the generated image.
- `--l`: Specifies the CFG scale of the generated image.
- `--s`: Specifies the number of steps in the generation.

The prompt weighting such as `( )` and `[ ]` also work. (Learn more about [Attention/Emphasis](https://github.com/AUTOMATIC1111/stable-diffusion-webui/wiki/Features#attentionemphasis))

## Publishing to Huggingface

1. Get your Huggingface Token from https://huggingface.co/settings/tokens
2. Enter the token in the "Huggingface Token" field and click "Login". This will save the token text in a local file named `HF_TOKEN` (All local and private).
3. Once you're logged in, you will be able to select a trained LoRA from the dropdown, edit the name if you want, and publish to Huggingface.

![publish_to_hf.png](publish_to_hf.png)


## Advanced

The advanced tab is automatically constructed by parsing the launch flags available to the latest version of [kohya sd-scripts](https://github.com/kohya-ss/sd-scripts). This means Fluxgym is a full fledged UI for using the Kohya script.

> By default the advanced tab is hidden. You can click the "advanced" accordion to expand it.

![advanced.png](advanced.png)


## Advanced Features

### Uploading Caption Files

You can also upload the caption files along with the image files. You just need to follow the convention:

1. Every caption file must be a `.txt` file.
2. Each caption file needs to have a corresponding image file that has the same name.
3. For example, if you have an image file named `img0.png`, the corresponding caption file must be `img0.txt`.
