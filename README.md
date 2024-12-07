# CSE597_003 Course Project
**Jeongwon Bae (945397461)**

**Bridging the Modality Gap in Zero-Shot Image Captioning: An Implementation of IFCap**

*IFCap: Image-like Retrieval and Frequency-based Entity Filtering for Zero-shot Captioning, EMNLP 2024*

---

## Repository Structure
- **src/**: Contains the source code for IFCap implementation, including:
  - Image-like Retrieval (ILR) methods.
  - Fusion Module (FM) code integrating text and retrieved embeddings.
  - Frequency-based Entity Filtering (EF) routines.
  - Model definitions and utility scripts for training and inference.

- **scripts/**: Shell scripts for:
  - Downloading datasets, annotations, evaluation tools, and checkpoints.
  - Running training routines for both COCO and Flickr30k datasets.
  - Performing inference (evaluation) on trained models.
  - Conducting cross-domain captioning experiments and improvements (e.g., switching to GPT-2 Medium).

After running the download script, the following directories are created:
- **annotations/**: Holds dataset annotations (e.g., COCO, Flickr30k), including caption data and any auxiliary JSON files used for training and inference.
- **evaluation/**: Contains code and scripts for computing evaluation metrics (BLEU@4, METEOR, CIDEr, SPICE) and summarizing the experimental results.
- **checkpoints/**: Includes the authors’ best released weights for IFCap and newly generated weights from your own training sessions. Use these checkpoints to replicate original results or test enhancements (e.g., using GPT-2 Medium).

---

## Environment Setup

```bash
conda create -n ifcap python=3.9
conda activate ifcap
pip install -r requirements.txt
```

---

## Data Preparation
Run the download script to retrieve annotations, evaluation tools, and best checkpoints:
```bash
bash scripts/download.sh
```
Ensure the corresponding image directories are properly placed (e.g., `annotations/coco/val2014` for COCO images or `annotations/flickr30k/flickr30k-images` for Flickr30k).

(Optional) Perform image-like retrieval and entity filtering as needed using the provided scripts:
```bash
# For COCO
python src/image_like_retrieval.py
python src/entity_filtering.py # with IDX=0

# For Flickr30k
python src/image_like_retrieval.py --domain_source flickr30k --domain_test flickr30k --L 7
python src/entity_filtering.py # with IDX=1
```

---

## Training (From Scratch)
To train on COCO:
```bash
bash scripts/train_coco.sh 0 coco annotations/coco/coco_train_seed30_var0.04.json
```

To train on Flickr30k:
```bash
bash scripts/train_flickr30k.sh 0 flickr annotations/flickr30k/flickr30k_train_seed30_var0.04.json
```

**For Cross-domain and GPT-2 Medium Experiments:**
- Modify the language model setting in the training scripts to `gpt2-medium`.
- For COCO:
```bash
bash scripts/train_coco_crossdomain.sh 0 coco-new annotations/coco/coco_train_seed30_var0.04.json
```
- For Flickr30k:
```bash
bash scripts/train_flickr30k_crossdomain.sh 0 flickr-new annotations/flickr30k/flickr30k_train_seed30_var0.04.json
```

---

## Inference (Evaluation)
### Using Released Weights (Original Authors’ Checkpoints)
- COCO:
```bash
bash scripts/eval_coco.sh train_coco 0 \
    '--entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 5' \
    coco-indomain \
    4
```

- Flickr30k:
```bash
bash scripts/eval_flickr30k.sh train_flickr30k 0 \
    '--entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
    flickr-indomain \
    14
```

- COCO → Flickr30k:
```bash
bash scripts/eval_flickr30k.sh train_coco 0 \
    '--entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
    coco-indomain \
    5
```

- Flickr30k → COCO:
```bash
bash scripts/eval_coco.sh train_flickr30k 0 \
    '--entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 4' \
    flickr-indomain \
    14
```

### Using Newly Trained Weights (From Scratch)
- COCO → Flickr30k:
```bash
bash scripts/eval_flickr30k.sh train_coco 0 \
    '--entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
    coco \
    4
```

- Flickr30k → COCO:
```bash
bash scripts/eval_coco.sh train_flickr30k 0 \
    '--entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 4' \
    flickr \
    12 # or your latest epochs
```

### Using GPT-2 Medium for Improvements
- COCO → Flickr30k:
```bash
bash scripts/eval_flickr30k.sh train_coco_crossdomain 0 \
    '--language_model gpt2-medium --entity_filtering --retrieved_info caption_flickr30k_image_flickr30k_7.json --K 3' \
    coco-new \
    4
```

- Flickr30k → COCO:
```bash
bash scripts/eval_coco.sh train_flickr30k_crossdomain 0 \
    '--language_model gpt2-medium --entity_filtering --retrieved_info caption_coco_image_coco_9.json --K 4' \
    flickr-new \
    16 # or your latest epochs
```

---

## Notes
- Ensure that both training and inference steps explicitly declare the correct language model (either `gpt2` or `gpt2-medium`) to maintain consistency.
- The provided scripts and commands reproduce the key experiments and improvements discussed in our project report.

---

## Contact
For questions or issues with the code or replication, please contact Jeongwon Bae.