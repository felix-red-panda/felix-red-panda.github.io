+++
title = "Idefics2: a small-ish multimodal LLM for local inference"
date = "2024-04-16T00:43:42+01:00"

# description = "An optional description for SEO. If not provided, an automatically created summary will be used."

tags = []
+++

Huggingface published a nice small LLM that supports image input yesterday. It has 8B parameters and was trained on [1.5 trillion images](https://twitter.com/HugoLaurencon/status/1779937394499744092). I adapted the code from their [blog post](https://huggingface.co/blog/idefics2) to be able to run it on a consumer GPU with quantization:

```python
# as of writing this there isn't a stable release that supports idefics2 so you need to install from the github repo:
# pip install -U git+https://github.com/huggingface/transformers
import requests
import torch

from transformers import AutoProcessor, AutoModelForVision2Seq
from transformers.image_utils import load_image

DEVICE = "cuda:0"

image1_small = load_image("https://i.postimg.cc/1RwkmRqr/Statue-of-Liberty-Island-New-York-Bay.jpg")
image2_small = load_image("https://i.postimg.cc/Bb9RTkdF/Skyline-Chicago.jpg")

processor = AutoProcessor.from_pretrained("HuggingFaceM4/idefics2-8b")
model = AutoModelForVision2Seq.from_pretrained("HuggingFaceM4/idefics2-8b", device_map="auto", load_in_8bit=True) # this takes ~17GB of VRAM with the supplied example image files
# model = AutoModelForVision2Seq.from_pretrained("HuggingFaceM4/idefics2-8b", device_map="auto", load_in_4bit=True) # this takes ~10GB of VRAM with the supplied example image files

messages = [
    {
        "role": "user",
        "content": [
            {
                "type": "image"
            },
            {
                "type": "text",
                "text": "What do we see in this image?"
            },
        ]
    },
    {
        "role":
            "assistant",
        "content": [{
            "type": "text",
            "text": "In this image, we can see the city of New York, and more specifically the Statue of Liberty."
        },]
    },
    {
        "role": "user",
        "content": [
            {
                "type": "image"
            },
            {
                "type": "text",
                "text": "And how about this image? Which city is that?"
            },
        ]
    },
]

prompt = processor.apply_chat_template(messages, add_generation_prompt=True)
inputs = processor(text=prompt, images=[image1_small, image2_small], return_tensors="pt")
inputs = {k: v.to(DEVICE) for k, v in inputs.items()}

generated_ids = model.generate(**inputs, max_new_tokens=500)
generated_texts = processor.batch_decode(generated_ids, skip_special_tokens=True)

print(generated_texts)
# ['User: What do we see in this image? \nAssistant: In this image, we can see the city of New York, and more specifically the Statue of Liberty. \nUser: And how about this image? Which city is that \nAssistant: That is the Chicago skyline.']
```
