# Korean-Chit-Chat-Dialog-Generation-Model

## Overview

This project aims to develop a conversation generation system capable of generating simple, context-aware responses. Initially, the goal included **style transformation** of responses, considering the tone and manner of the conversation. However, due to time constraints, only the **conversation generation** task was developed.

_Initial pipeline — only the section in the red box was developed._

## Dataset Setup and Preprocessing

### Dataset Selection
We selected **Korean SNS data**, consisting of 2 million multi-turn conversations. Each conversation has between 4 to 20 turns, with an average of 8 turns, involving 2 to 10 participants. This dataset captures typical informal chat styles, including abbreviations, misspellings, and casual expressions (e.g., “ㅇㅇ”, “ㅋㅋ”). For experiments, we used 500,000 conversations focused on personal and relational categories.

### Preprocessing Steps
- **Merging Split Utterances**: Conversations often contained split messages, which we merged into single utterances for consistency.
- **Multi-Turn Subsets**: To handle varying contexts, conversations were partitioned into 2-turn and 3-turn subsets.
- **Normalizing Repeated Characters**: We limited repeated characters (e.g., “ㅋㅋㅋ”, “ㅎㅎ”) to a maximum of two occurrences using soynlp’s repeat_normalize() function.
- **Reducing Dataset Size**: Non-essential fields like timestamps were removed to minimize data size for training.

## Experiments
During training, **special tokens (e.g., <P01>, <P02>)** were introduced to distinguish participants. The initial **SKT KoBART** model generated repetitive responses (e.g., “ㅋㅋㅋ”), so we adjusted the preprocessing to **limit repeated characters** and removed 1- and 2-turn conversations, only keeping dialogues with **3 or more turns**. Special characters (except question marks) were also removed to improve performance.

We evaluated various **encoder-decoder models** of different sizes to find the best trade-off between performance and efficiency:

- **pko-T5 (Large, 800M)**
  - Generated natural responses initially, but performance plateaued after the first epoch with repetitive or irrelevant outputs.
  - Loss: 5.1388 (stagnant after 400 steps)
  
- **SKT KoBART (Base, 130M)**
  - Performed well but struggled with repetitive character outputs.
  - Loss: 3.832 after 10 epochs
  
- **LMKor BERT Shared (Mid, 130M)**
  - Delivered the best results, producing human-like responses aligned with conversation context.
  - Loss: 4.61 after 5 epochs

- **comoquester BART (Mini, 13M)**
  - The smallest model tested, but it failed to generate meaningful sentences, often repeating the same word. The small parameter size limited its ability to handle complex dialogue generation.

The **LMKor BERT Shared model** emerged as the most effective, balancing context-awareness with fluent response generation.
