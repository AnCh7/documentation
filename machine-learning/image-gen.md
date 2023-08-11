# CivitAI: How to use models

## [AUTOMATIC1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)

### Fine-tuned Model Checkpoints (Dreambooth Models)

1. Download the custom model
2. Place the model inside the `models/Stable-diffusion` directory of your AUTOMATIC1111 Web UI instance
3. Refresh your model list or restart the Stable Diffusion Web UI
4. Select the custom model from the `Stable Diffusion checkpoint` input field
5. Use the trained keyword in a prompt (listed on the custom model's page)

### Textual Inversions

1. Download the textual inversion
2. Place the textual inversion inside the `embeddings` directory of your AUTOMATIC1111 Web UI instance
3. Use the trained keyword in a prompt (listed on the textual inversion's page)

### Aesthetic Gradients

1. Install the [Aesthetic Gradients extension](https://github.com/AUTOMATIC1111/stable-diffusion-webui-aesthetic-gradients)
2. Download the aesthetic gradient
3. Place the aesthetic graident inside the `aesthetic_embeddings` directory of your AUTOMATIC1111 Web UI instance
4. Restart the Stable Diffusion Web UI
5. Adjust the aesthetic gradient setting

### Hypernetwork

1. Download the hypernetwork
2. Place the hypernetwork inside the `models/hypernetworks` directory of your AUTOMATIC1111 Web UI instance
3. Restart the Stable Diffusion Web UI
4. Select the hypernetwork from the `Hypernetwork` input field in settings
5. Adjust the hypernetwork strength using the `Hypernetwork strength` range slider in settings

### LoRA

[Video Guide by Lykon](https://www.youtube.com/watch?v=-bMeyXOZwN0)

1. Download the LoRA
2. Place the file inside the `models/lora` folder
3. Click on the `show extra networks` button under the `Generate` button (purple icon)
4. Go to the `Lora` tab and refresh if needed
5. Click on the one you want to apply, it will be added in the prompt
6. **Make sure to adjust the weight**, by default it's `:1` which is usually to high

### LoCon

1. Ensure that you've installed the [LoCon Extension](https://github.com/KohakuBlueleaf/a1111-sd-webui-locon)
2. Download the LoCon
3. Place the file inside the `models/lora` folder
4. Click on the `show extra networks` button under the `Generate` button (purple icon)
5. Go to the `Lora` tab and refresh if needed
6. Click on the one you want to apply, it will be added in the prompt
7. **Make sure to adjust the weight**, by default it's `:1` which is usually to high

### Wildcards

1. Ensure that you've installed the [Wildcard Extension](https://github.com/AUTOMATIC1111/stable-diffusion-webui-wildcards)
2. Download the Wildcards archive
3. Extract the archive into `extensions/stable-diffusion-webui-wildcards/wildcards`
4. Use one or many of the wildcard words in your prompt to trigger the word replacement on generation (ex. `__hair-style__`)

## [Cmdr2's Stable Diffusion UI v2](https://stable-diffusion-ui.github.io/)

### Fine-tuned Model Checkpoints (Dreambooth Models)

1. Download the custom model in Checkpoint format (.ckpt)
2. Place the model file inside the models\stable-diffusion directory of your installation directory (e.g.  C:\stable-diffusion-ui\models\stable-diffusion)
3. Reload the web page to update the model list
4. Select the custom model from the Model list in the Image Settings section
5. Use the trained keyword in a prompt (listed on the custom model's page)

## [aiNodes](https://github.com/XmYx/ainodes-pyside/tree/main)

1. Open the model download widget, search for the models in the list from Civitai.
2. Select the model you want as well as the config yaml which has to be used by that model.
3. Wait until the progressbar shows 100%.
4. Go back to the sampler widget where you choose the model from the  list of installed models and press the dream button to render your  prompt with the downloaded model.

You can follow these directions to download checkpoints (ckpt,  safetensors), hypernetworks, textual inversion or aesthetic gradients.  (And soon VAEs). We also support V2 models, both V2 formats are welcome  here.

**For all those steps you never leave the UI**, just  click buttons and you stay in your flow of creating art, not getting  distracted with having to deal with your OS to move files or anything  like that. It is as convenient as possible for now.
