## BioVERSE: Biomedical Vector Embedding Realignment for Semantic Engagement

### Objectives
We propose BioVERSE, a modular architecture that connects large language models (LLMs) with biomedical foundation models (BioFMs) by aligning biological and textual representations. Building on prior biomedical efforts and recent advances in vision-language models, BioVERSE enables seamless integration of diverse bio-embeddings with open-source LLMs. Users can bring their own models, align embeddings, and build customizable multimodal question-answering systems. Through contrastive alignment and instruction tuning, BioVERSE supports zero-shot annotation, multimodal reasoning, and explainable interaction. Its lightweight design allows on-premises, privacy-preserving deployment, empowering researchers and clinicians to query complex biological data through multi-turn dialogue.

<img width="1080" height="540" alt="image" src="https://github.com/user-attachments/assets/fdb95bfd-0372-42a9-8ec9-b663c3303b92" />
Figure 1. Multimodal scRNA-seq Q&A Demo Interface. This interface enables interactive exploration of scRNA-seq data using a language model. Step 1: Users upload their own scRNA-seq dataset, which is visualized as a UMAP plot in the left panel. Step 2: Users can select individual or grouped cells and pose natural language questions. The selected cells are encoded by BioFMs and then passed to the language model as context for generating cell-specific answers.

### Background
Existing efforts to integrate biomedical data with LLMs vary in approach but share key limitations. CellWhisperer1 uses BioBERT with contrastive learning for retrieval from scRNA-seq and Mistral for generation via a retrieval-augmented generation (RAG) setup but lacks direct generation from embeddings and broader modality support. TxGemma2 reasons over tokenized inputs like SMILES and protein sequences but does not support non-tokenized data such as scRNA-seq. In contrast, vision-language models like LLaVA5 employ projection layers and instruction tuning to align non-text modalities with LLMs in a modular, scalable manner, offering a more effective blueprint for developing extensible, bio-text integrated QA systems.
<img width="864" height="345" alt="image" src="https://github.com/user-attachments/assets/dd886882-daf5-4d74-9e65-e394501bafb5" />
Figure 2. Embedding spaces before and after alignment.
Left: Bio-entities (green) and their associated text descriptions (blue) operate in separate embedding space.
Right: The goal of alignment training is to bring paired embeddings closer together in the shared latent space.

### Methods
<img width="1080" height="463" alt="image" src="https://github.com/user-attachments/assets/e1d90a81-9591-4684-a70a-4148e5f63fa0" />
Figure 3. Default alignment training in BioVERSE. Biological data (e.g., scRNA-seq) is first encoded using a pretrained BioFM. The resulting hidden states are pooled (e.g., using CLS or custom pooling) and projected via a Bio-Lang Adapter into the LLM token embedding space. Textual queries and answers are tokenized and embedded using a standard LLM tokenizer. The projected biological embeddings and text tokens are jointly processed by a decoder-only LLM (e.g., LLaMA or Granite). InfoNCE loss is used to train the projection weights and bring paired embeddings closer to each other in the LLM embedding space. 

BioVERSE includes three trainable components: (1) a projection layer that aligns bio-embeddings, [BIO], with the LLM token space, (2) an optional trainable [Bt] token to capture global/task-level context, and (3) a LoRA module for efficient adaptation of the LLM during instruction tuning. During the alignment phase, we freeze all LLM weights and train the projection layer (and the optional [Bt] token). In the subsequent instruction tuning phase, we freeze the projection layer and [Bt] token, and fine-tune only the LoRA parameters using a standard autoregressive cross-entropy loss. This two-stage process ensures that biological representations are aligned with language while preserving generation capabilities for downstream tasks.
<img width="1080" height="447" alt="image" src="https://github.com/user-attachments/assets/c2cc8db2-ee4a-4985-9c87-0830e4879416" />
Figure 4. Default instruction tuning in BioVERSE. Only LoRA adapter is trainable. 






