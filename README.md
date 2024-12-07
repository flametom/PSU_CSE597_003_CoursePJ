# CSE597_003 Course Project
## Jeongwon Bae (945397461)

---
Bridging the Modality Gap in Zero-Shot Image Captioning: An Implementation of IFCap

(IFCap: Image-like Retrieval and Frequency-based Entity Filtering for Zero-shot Captioning, EMNLP2024)

---
### Conda environment
```bash
conda create -n ifcap python=3.9
conda activate ifcap
pip install -r requirements.txt
```

### Data preparation
Download annotations, evaluation tools, and best checkpoints.
```
bash scripts/download.sh
```

(Optional)
For COCO,
```
# place your coco images in annotations/coco/val2014

# image-like retrieval
python src/image_like_retrieval.py

# entity filtering
python src/entity_filtering.py # with IDX=0
```

For Flickr30k,
```
# place your flickr30k images in annotations/flickr30k/flickr30k-images

# image-like retrieval
python src/image_like_retrieval.py --domain_source flickr30k --domain_test flickr30k --L 7

# entity filtering
python src/entity_filtering.py # with IDX=1
```

### Training 1 (from scratch provided source code)
For COCO,
```
# bash scripts/train_coco.sh CUDA_DEVICE TEST_NAME RT_PATH
bash scripts/train_coco.sh 0 coco annotations/coco/coco_train_seed30_var0.04.json
```

For Flickr30k
```
# bash scripts/train_flickr30k.sh CUDA_DEVICE TEST_NAME RT_PATH
bash scripts/train_flickr30k.sh 0 flickr annotations/flickr30k/flickr30k_train_seed30_var0.04.json
```

### Training 2 (possible improvement - using gpt2-medium)
For COCO,
```
bash scripts/train_coco_crossdomain.sh 0 coco-new annotations/coco/coco_train_seed30_var0.04.json
```

For Flickr30k
```
bash scripts/train_flickr30k_crossdomain.sh 0 flickr-new annotations/flickr30k/flickr30k_train_seed30_var0.04.json
```

### Inference 1 (released weight)
[COCO]
```
bash scripts/eval_coco.sh train_coco 0 \
	'--entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 5' \
	coco-indomain \
	4
```

[Flickr30k]
```
bash scripts/eval_flickr30k.sh train_flickr30k 0 \
	'--entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
	flickr-indomain \
	14
```
[COCO -> Flickr30k]
```
bash scripts/eval_flickr30k.sh train_coco 0 \
	'--entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
	coco-indomain \
	5 
```

[Flickr30k -> COCO]
```
bash scripts/eval_coco.sh train_flickr30k 0 \
	'--entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 4' \
	flickr-indomain \
	14
```

### Inference 2 (from scratch provided source code)
[COCO -> Flickr30k]
```
bash scripts/eval_flickr30k.sh train_coco 0 \
	'--entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
	coco \
	4
```

[Flickr30k -> COCO]
```
bash scripts/eval_coco.sh train_flickr30k 0 \
	'--entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 4' \
	flickr \
	12 # or your lastest epochs
```

### Inference 3 (possible improvement - using gpt2-medium)
[COCO -> Flickr30k]
```
bash scripts/eval_flickr30k.sh train_coco_crossdomain 0 \
	'--language_model gpt2-medium --entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
	coco-new \
	4
```

[Flickr30k -> COCO]
```
bash scripts/eval_coco.sh train_flickr30k_crossdomain 0 \
	'--language_model gpt2-medium --entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 4' \
	flickr-new \
	16 # or your lastest epochs
```

## Reference
```
@article{lee2024ifcap,
  title={IFCap: Image-like Retrieval and Frequency-based Entity Filtering for Zero-shot Captioning},
  author={Lee, Soeun and Kim, Si-Woo and Kim, Taewhan and Kim, Dong-Jin},
  journal={arXiv preprint arXiv:2409.18046},
  year={2024}
}
```

***
