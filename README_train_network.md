# Train network documentation translated from japanese
## About learning LoRA

[LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685) (arxiv), [LoRA](https://github.com/microsoft/LoRA) (github) to Stable Applied to Diffusion.

[cloneofsimo's repository](https://github.com/cloneofsimo/lora) was a great reference. thank you.

8GB VRAM seems to work just fine.

## A Note about Trained Models

Cloneofsimo's repository and d8ahazard's [Drebooth Extension for Stable-Diffusion-WebUI](https://github.com/d8ahazard/sd_drebooth_extension) are currently incompatible due to ongoing enhancements (see below).

In order to generate images using WebUI, it is necessary to merge the learned LoRA model with the Stable Diffusion model using the script in this repository. The resulting merged model file will incorporate the learning results from LoRA. Note that merging is not required when generating images with the script in this repository.

Note that merging is not required when generating with the image generation script in this repository.

## Learning method

Use train_network.py.

You can learn both the DreamBooth method (using identifiers (sks, etc.) and classes, optionally with regularized images) and the fine tuning method using captions.

Both methods can be learned in much the same way as existing scripts. We will discuss the differences later.

### Using the DreamBooth Method

Please refer to note.com [Environment preparation and DreamBooth learning script](https://note.com/kohya_ss/n/nba4eceaa4594) to prepare the data.

Specify train_network.py instead of train_db.py when training.

Almost all options are available (except model saving related to Stable Diffusion), but stop_text_encoder_training is not supported.

### When to use captions

Please refer to [fine-tuning guide](./fine_tune_README_en.md) and perform each step.

Specify train_network.py instead of fine_tune.py when training. Almost all options (except for model saving) can be used as is.

In addition, it will work even if you do not perform "Pre-obtain latents". Since the latent is acquired from the VAE when learning (or caching), the learning speed will be slower, but color_aug can be used instead.

### Options for Learning LoRA

In train_network.py, specify the name of the module to be trained in the --network_module option. LoRA is compatible with network.lora, so please specify it.

The learning rate should be set to about 1e-4, which is higher than normal DreamBooth and fine tuning.

Below is an example command line (DreamBooth technique).

```
accelerate launch --num_cpu_threads_per_process 12 train_network.py
     --pretrained_model_name_or_path=..\models\model.ckpt
     --train_data_dir=..\data\db\char1 --output_dir=..\lora_train1
     --reg_data_dir=..\data\db\reg1 --prior_loss_weight=1.0
     --resolution=448,640 --train_batch_size=1 --learning_rate=1e-4
     --max_train_steps=400 --use_8bit_adam --xformers --mixed_precision=fp16
     --save_every_n_epochs=1 --save_model_as=safetensors --clip_skip=2 --seed=42 --color_aug
     --network_module=networks.lora
```

The LoRA model will be saved in the directory specified by the --output_dir option.

In addition, the following options can be specified.

* --network_dim
   * Specify the number of dimensions of LoRA (such as ``--networkdim=4``). Default is 4. The greater the number, the greater the expressive power, but the memory and time required for learning also increase. In addition, it seems that it is not good to increase it blindly.
* --network_weights
   * Load pretrained LoRA weights before training and additionally learn from them.
* --network_train_unet_only
   * Valid only for LoRA modules related to U-Net. It may be better to specify it in fine-tuning study.
* --network_train_text_encoder_only
   * Only LoRA modules related to Text Encoder are enabled. You may be able to expect a textual inversion effect.
* --unet_lr
   * Specify when using a learning rate different from the normal learning rate (specified with the --learning_rate option) for the LoRA module related to U-Net.
* --text_encoder_lr
   * Specify when using a learning rate different from the normal learning rate (specified with the --learning_rate option) for the LoRA module associated with the Text Encoder. Some people say that it is better to set the Text Encoder to a slightly lower learning rate (such as 5e-5).

If both --network_train_unet_only and --network_train_text_encoder_only are not specified (default), both Text Encoder and U-Net LoRA modules will be enabled. ## About the merge script

merge_lora.py allows you to merge LoRA training results into a Stable Diffusion model, or merge multiple LoRA models.

### Merge LoRA model into Stable Diffusion model

The model after merging can be handled in the same way as normal Stable Diffusion ckpt. For example, a command line like:

```
python networks\merge_lora.py --sd_model ..\model\model.ckpt
     --save_to ..\lora_train1\model-char1-merged.safetensors
     --models ..\lora_train1\last.safetensors --ratios 0.8
```

Specify the --v2 option if you want to train with a Stable Diffusion v2.x model and merge with it.

Specify the Stable Diffusion model file to be merged in the --sd_model option (only .ckpt or .safetensors are supported, Diffusers is not currently supported).

Specify the save destination of the model after merging in the --save_to option (.ckpt or .safetensors, automatically determined by extension).

Specify the LoRA model file learned in --models. It is possible to specify more than one, in which case they will be merged in order.

For --ratios, specify the application rate of each model (how much weight is reflected in the original model) with a numerical value from 0 to 1.0. For example, if it is close to overfitting, it may be better if the application rate is lowered. Specify as many as the number of models.

When specifying multiple, it will be as follows.

```
python networks\merge_lora.py --sd_model ..\model\model.ckpt
     --save_to ..\lora_train1\model-char1-merged.safetensors
     --models ..\lora_train1\last.safetensors ..\lora_train2\last.safetensors --ratios 0.8 0.5
```

### Merge multiple LoRA models

After all, it may not be very useful because it cannot be inferred unless it is merged into the SD model. However, when merging multiple LoRA models one by one into the SD model, and when merging multiple LoRA models and then merging them into the SD model, the result will be slightly different in relation to the calculation order.

For example, a command line like:

```
python networks\merge_lora.py
     --save_to ..\lora_train1\model-char1-style1-merged.safetensors
     --models ..\lora_train1\last.safetensors ..\lora_train2\last.safetensors --ratios 0.6 0.4
```

The --sd_model option does not need to be specified.

Specify the save destination of the merged LoRA model in the --save_to option (.ckpt or .safetensors, automatically determined by extension).

Specify the LoRA model file learned in --models. Three or more can be specified.

For --ratios, specify the ratio of each model (how much weight is reflected in the original model) with a numerical value from 0 to 1.0. If you merge two models one-to-one, it will be "0.5 0.5". "1.0 1.0" would give too much weight to the sum, and the result would probably be less desirable.

LoRA trained with v1 and LoRA trained with v2, and LoRA with different number of dimensions cannot be merged. U-Net only LoRA and U-Net+Text Encoder LoRA should be able to merge, but the result is unknown.


### Other Options

* precision
   * The precision for merge calculation can be specified from float, fp16, and bf16. If omitted, it will be float to ensure accuracy. Specify fp16/bf16 if you want to reduce memory usage.
* save_precision
   * You can specify the precision when saving the model from float, fp16, bf16. If omitted, the precision is the same as precision.

## Generate with the image generation script in this repository

Add options --network_module, --network_weights, --network_dim (optional) to gen_img_diffusers.py. The meaning is the same as when learning.

You can change the LoRA application rate by specifying a value between 0 and 1.0 with the --network_mul option.

## Additional Information

### Differences from cloneofsimo's repository

As of 12/25, this repository has expanded LoRA application points to Text Encoder's MLP, U-Net's FFN, and Transformer's in/out projection, increasing its expressiveness. However, the amount of memory used increased, and it became the last minute of 8GB instead.

Also, the module replacement mechanism is completely different.

### About future expansion

It is possible to support not only LoRA but also other expansions, so we plan to add them as well.