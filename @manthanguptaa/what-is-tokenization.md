# What is tokenization?

**Author:** @manthanguptaa  
**Date:** 2026-01-17T04:55:53.000Z  
**URL:** https://x.com/manthanguptaa/article/2012388383642022127  
**Images:** 5  

---

TL;DR: Modern LLMs use subword tokenization (BPE, WordPiece, or Unigram) to balance vocabulary size with sequence length. Tokenization directly affects API costs, training compute, and model capabilities. It’s why LLMs struggle with arithmetic and spelling. For custom domains, train your own tokenizer using HuggingFace’s tokenizers library. Use Tiktoken for speed, SentencePiece for multilingual.

The wrong tokenization strategy could be costing you $500K+ annually.

Here’s why: language model APIs charge by the token. OpenAI, Anthropic, Google, etc. And the thing most people don’t realize is that the same sentence can be 10 tokens or 50 tokens depending on how you tokenize it. A company processing billions of API calls? That difference compounds over time to make a hole in the bank.

But it goes deeper than API costs. If you are training a model, tokenization affects:

- Training time - more tokens means more compute
- Context window utilization - inefficient tokenization wastes your precious context length
- Model quality - a tokenizer that butchers your domain vocabulary will handicap your model from day one

I have been meaning to understand how language models are trained for a while now. That curiosity led me down the rabbit hole of building one from scratch. This is Part 1 of what I hope becomes a comprehensive series and we are starting with tokenization because it is the foundation everything else sits on.

By the end of this post, you will understand why different tokenization methods exist, how the numbers actually work, and how to build your own tokenizer from scratch.

Let’s get into it.

# What is tokenization?

Neural networks don’t understand words, sentences, or characters, they understand numbers. Tokenization is the process of converting text into numbers. We will explain what these numbers represent shortly. But here’s the key insight: how you convert text to numbers matters enormously.

## Tokenization pipeline

Tokenization happens in stages. Each stage transforms text before passing it to the next:

[![Image](https://pbs.twimg.com/media/G-1o9PbaEAA2A4w?format=png&name=small)](/manthanguptaa/article/2012388383642022127/media/2012380009827274752)

## Three levels of granularity

1. Character-level: Every character is a token.

“Hello World” -> [“H”, “e”, “l”, “l”, “o”, " “, “W”, “o”, “r”, “l”, “d”] -> [72, 101, 108, 108, 111, 32, 87, 111, 114, 108, 100]

Pros:

- Tiny vocabulary (~256 for ASCII, ~65K for Unicode)
- Can represent any text, no “unknown” tokens

Cons:

- Sequences become very long (expensive!)
- Model must learn spelling from scratch

2. Word-level: Every word is a token.

“Hello world” -> [“Hello”, “world”] -> [15496, 995]

Pros:

- Semantically meaningful units
- Short sequences

Cons:

- Huge vocabulary (English has 170K+ words)
- Can’t handle typos, new words, or morphology (“running” != “run”)

3. Subword-level: Breaks words into meaningful pieces.

“unhappiness” -> [“un”, “happiness”] -> [403, 26019]

Pros:

- Reasonable vocabulary size (32K–100K typical)
- Handles rare words by decomposition
- Captures morphological patterns (“running” -> “run” + “ning”)
- This is what modern LLMs use

Cons:

- More complex to implement

Modern tokenizers (BPE, WordPiece, Unigram) are all trying to find the optimal balance by maximizing compression while maintaining meaningful units.

# The Numbers Explained: Token IDs and Embeddings

Before we dive into the algorithms, let’s understand what these numbers actually mean. When you see "Hello" -> [15496], what does that 15496 represent?

## Token IDs: The Vocabulary Index

A token ID is simply an index into the vocabulary. Think of it like a dictionary:

python

```
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("gpt2")

# The vocabulary is a dictionary: token -> ID
print(f"Vocabulary size: {len(tokenizer.vocab)}")
# Vocabulary size: 50257

# Look up a token
token = "hello"
token_id = tokenizer.convert_tokens_to_ids(token)
print(f"'{token}' -> ID {token_id}")
# 'hello' -> ID 31373

# Reverse lookup: ID -> token
print(f"ID {token_id} -> '{tokenizer.convert_ids_to_tokens([token_id])[0]}'")
# ID 31373 -> 'hello'
```

Key insight: Token IDs are just integers from 0 to vocab\_size - 1. They have no inherent meaning; they are just positions in the vocabulary array.

## From Token IDs to Embeddings

When you pass token IDs to a language model, they get converted to embeddings, dense vector representations that the model can work with.

```
Text: "Hello World"
  ↓ Tokenization
Token IDs: [15496, 995]
  ↓ Embedding Lookup
Embeddings: [[0.1, 0.3, -0.2, ...], [0.5, -0.1, 0.8, ...]]
  ↓ Model Processing
Output: ...
```

## The Embedding Layer

Every language model has an embedding layer (also called a lookup table):

python

```
import torch
import torch.nn as nn

# Simplified embedding layer
vocab_size = 50257
embedding_dim = 768  # GPT-2's embedding dimension

# Create embedding layer
embedding = nn.Embedding(vocab_size, embedding_dim)

# Token IDs
token_ids = torch.tensor([15496, 995])  # "Hello World"

# Convert to embeddings
embeddings = embedding(token_ids)
print(embeddings.shape)
# torch.Size([2, 768])  # 2 tokens, each with 768-dimensional vector
```

What’s happening:

- Token ID 15496 -> Look up row 15496 in the embedding matrix
- Each row is a learned vector (e.g. 768 dimensions for GPT-2)
- The model learns these embeddings during training
- Similar tokens end up with similar embeddings (closer in vector space)

Why Token IDs Matter

1. Memory efficiency:Storing token IDs (integers) is much smaller than storing token strings
   A 4-byte integer vs potentially 10+ bytes per token string
2. Model input:Models expect integer tensors, not strings
   Token IDs are the interface between text and neural networks
3. Embedding lookup:Token IDs directly index into the embedding matrix
   Fast, efficient array indexing
4. Batch processing:All sequences in a batch must have the same length
   Token IDs make padding/truncation straightforward

Padding & Truncation

When processing multiple sequences, they need to be the same length

python

```
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("gpt2")
tokenizer.add_special_tokens({'pad_token': '[PAD]'})

texts = [
    "Hello",
    "Hello world",
    "Hello world, how are you?"
]

# Encode with padding
encodings = tokenizer(
    texts,
    padding=True,        # Pad shorter sequences
    truncation=True,     # Truncate longer sequences
    max_length=10,       # Maximum length
    return_tensors="pt"  # Return PyTorch tensors
)

print(encodings["input_ids"])
# tensor([[15496, 50257, 50257, 50257, 50257, 50257, 50257],
#        [15496,   995, 50257, 50257, 50257, 50257, 50257],
#        [15496,   995,    11,   703,   389,   345,    30]])

# The padding token ID is typically 0 or a special token
print(f"Padding token ID: {tokenizer.pad_token_id}")
# Padding token ID: 50257
```

Padding token:

- Usually ID 50257 or a special token like [PAD]
- The model learns to ignore padding during attention
- Padding doesn’t contribute to loss calculations

# Building Intuition: How Tokenizers Work

Before diving into BPE, WordPiece, and Unigram, let’s build a simple tokenizer from scratch. This will give you a concrete understanding of what tokenization actually does.

## A Simple Word-Level Tokenizer

Let’s start with the simplest possible approach: word-level tokenization. Every unique word gets its own token ID.

Step 1: Building the Vocabulary

First, we need to create a vocabulary, a mapping from words to numbers. We will:

1. Read a corpus of text
2. Extract all unique words
3. Assign each word a unique integer ID

You can use this 

[link](https://www.libraryofshortstories.com/onlinereader/a-transgression)

 to use as training corpus. Copy it in a .txt file and save it as training-corpus.txt.

python

```
import re

# Read a text file (our training corpus)
with open("training-corpus.txt", "r", encoding="utf-8") as file:
    raw_text = file.read()

# Preprocess: split on punctuation and whitespace, keep the delimiters
# This regex splits on punctuation/whitespace but keeps them as separate tokens
preprocessed = re.split(r'([,.:;?_!"()\']|--|\s+)', raw_text)
# Remove empty strings and strip whitespace
preprocessed = [item.strip() for item in preprocessed if item.strip()]

# Get all unique tokens (words + punctuation)
all_words = sorted(set(preprocessed))

# Add special tokens
# <|endoftext|> marks the end of a document
# <|unk|> represents unknown words (words not in vocabulary)
all_words.extend(["<|endoftext|>", "<|unk|>"])

# Create vocabulary: word -> integer ID
vocab = {token: integer for integer, token in enumerate(all_words)}

print(f"Vocabulary size: {len(vocab)}")
print(f"First 10 tokens: {list(vocab.items())[:10]}")
# Vocabulary size: 709
# First 10 tokens: [('!', 0), (',', 1), ('.', 2), (':', 3), (';', 4), ('?', 5), ('A', 6), ('Again', 7), ('Agnia', 8), ('Agnia’s', 9)]
```

What just happened?

- We split text into tokens (words and punctuation)
- Created a vocabulary mapping each unique token to an integer
- Added special tokens for control flow (<|endoftext|> and <|unk|>)

Step 2: The Tokenizer Class

python

```
class SimpleTokenizerV1:
    def __init__(self, vocab):
        """
        Initialize with a vocabulary.
        
        vocab: dictionary mapping token strings to integer IDs
        """
        # Forward mapping: token -> ID (for encoding)
        self.str_to_int = vocab
        
        # Reverse mapping: ID -> token (for decoding)
        self.int_to_str = {v: k for k, v in vocab.items()}

    def encode(self, text):
        """
        Convert text into a list of token IDs.
        
        Process:
        1. Split text into tokens (same preprocessing as vocabulary building)
        2. Replace unknown tokens with <|unk|>
        3. Convert each token to its ID
        """
        # Same preprocessing as when building vocabulary
        preprocessed = re.split(r'([,.:;?_!"()\']|--|\s+)', text)
        preprocessed = [item.strip() for item in preprocessed if item.strip()]
        
        # Handle unknown words: if token not in vocab, use <|unk|>
        preprocessed = [
            item if item in self.str_to_int else "<|unk|>" 
            for item in preprocessed
        ]
        
        # Convert tokens to IDs
        ids = [self.str_to_int[item] for item in preprocessed]
        return ids

    def decode(self, ids):
        """
        Convert token IDs back into text.
        
        Process:
        1. Map each ID to its token string
        2. Join tokens with spaces
        3. Clean up spacing around punctuation
        """
        # Convert IDs back to tokens
        tokens = [self.int_to_str[id] for id in ids]
        
        # Join with spaces
        text = " ".join(tokens)
        
        # Fix spacing: remove space before punctuation
        # "Hello , world" -> "Hello, world"
        text = re.sub(r'\s+([,.?!"()\'])', r'\1', text)
        
        return text
```

Step 3: Using the Tokenizer

python

```
# Initialize tokenizer with our vocabulary
tokenizer = SimpleTokenizerV1(vocab)

# Example texts
text1 = "Hello, do you like tea?"
text2 = "<|endoftext|> In the sunlit terraces of the palace."
text = " ".join([text1, text2])

# Encode: text -> numbers
ids = tokenizer.encode(text)
print("Encoded IDs:", ids)
# Output (IDs depend on your vocabulary):
# [708, 1, 200, 670, 351, 708, 5, 707, 34, 576, 708, 708, 401, 576, 708, 2]

# Decode: numbers -> text
decoded = tokenizer.decode(ids)
print("Decoded text:", decoded)
# Output: "<|unk|>, do you like <|unk|>? <|endoftext|> In the <|unk|> <|unk|> of the <|unk|>."
```

What’s happening under the hood?

```
Text: "Hello, do you like tea?"

Step 1: Preprocess (split on punctuation/whitespace)
  -> ["Hello", ",", " ", "do", " ", "you", " ", "like", " ", "tea", "?"]

Step 2: Strip and filter empty strings
  -> ["Hello", ",", "do", "you", "like", "tea", "?"]

Step 3: Map to IDs using vocabulary
  -> [708, 1, 200, 670, 351, 708, 5]  (IDs from our vocabulary lookup)

Step 4: Decode back (reverse mapping)
  -> ["Hello", ",", "do", "you", "like", "tea", "?"]

Step 5: Join and clean spacing
  -> "Hello, do you like tea?"
```

Now that you understand the core mechanics of tokenization, building a vocabulary, encoding text to IDs, and decoding back, let’s tackle the real question: how do modern tokenizers solve the vocabulary-size vs. sequence-length tradeoff?

Word-level tokenization is simple but problematic, huge vocabularies, no handling of new words, and no morphological awareness. Character-level goes to the other extreme, tiny vocabulary but extremely long sequences.

Subword tokenization finds the sweet spot. Let’s dive into the three algorithms that power today’s language models: BPE, WordPiece, and Unigram.

# Deep Dive: The Three Major Subword Tokenizers

## Byte Pair Encoding (BPE)

Byte Pair Encoding (BPE) is a subword tokenization algorithm that builds a vocabulary by iteratively merging the most frequent pairs of adjacent tokens.

The core idea: start with a base vocabulary of individual characters (or bytes), repeatedly merge the most frequent adjacent pair into a new token, and stop when you reach your target vocabulary size. The result is a vocabulary that naturally captures common subwords, morphological patterns, and even full words for frequent terms.

Let’s walk through an example to understand how BPE works.

Training Phase: Building the Vocabulary

```
Training text: "low low low low lowest newest"

Step 0: Initialisation
First, we split each word into characters and add an end of word marker (typically `</w>`)
Vocabulary: {l, o, w, n, e, s, t, </w>}
Word Frequencies: {"l o w </w>": 4, "l o w e s t </w>": 1, "n e w e s t </w>": 1}

Step 1: Count all adjacent pairs
We count how many times each pair of adjacent tokens appears:
Pair Counts: {("l", "o"): 5, ("o", "w"): 5, ("w", "</w>"): 4, ("w", "e"): 2, ("e", "s"): 2, ("s", "t"): 2, ("t", "</w>"): 2, ("n", "e"): 1, ("e", "w"): 1}
The most frequent pair is "lo" with 5 occurrences. Merge it into a new token:

Step 2: Merge the most frequent pair
Create a new token `lo` by merging `l` and `o`:
Vocabulary: {l, o, w, n, e, s, t, </w>, lo}
Word Frequencies: {"lo w </w>": 4, "lo w e s t </w>": 1, "n e w e s t </w>": 1}

Step 3: Recount and merge again
Pair Counts: {("lo", "w"): 5, ("w", "</w>"): 4, ("w", "e"): 2, ("e", "s"): 2, ("s", "t"): 2, ("t", "</w>"): 2, ("n", "e"): 1, ("e", "w"): 1}
Merge the most frequent pair "lo" and "w" with 5 occurrences:
Word Frequencies: {"low </w>": 4, "low e s t </w>": 1, "n e w e s t </w>": 1}

Step 4: Continue merging
Next most frequent pairs might be:
- `(e, s)` -> `es` (appears 2 times)
- `(es, t)` -> `est` (appears 2 times)
- `(n, e)` -> `ne` (appears 1 time)
- `(ne, w)` -> `new` (appears 1 time)

After several more iterations, our vocabulary might look like:
Vocabulary: {l, o, w, e, s, t, n, </w>, lo, low, es, est, ne, new, newest, lowest}
```

Notice what happened:

- Common words became single tokens: low (appeared 4 times)
- Morphological patterns emerged: est (suffix for superlatives)
- Rare words stay decomposed: newest might become ["new", "est"]

Encoding Phase: Tokenizing New Text

Once training is complete, we have a list of merge rules in the order they were learned. To encode new text, we apply these merges iteratively until no more can be applied.

1. Split the text into characters
2. Iterate through merge rules in the order they were learned
3. For each merge rule, check if the pair exists in the current token sequence
4. If found, merge the pair
5. Continue until no more merges can be applied
6. Return the final tokens

```
Example: encoding `"lowest"` (assume not in training data)

Let's say our trained BPE tokenizer learned these merge rules (in order of their frequency):

(l, o) -> "lo"
(lo, w) -> "low"
(e, s) -> "es"
(es, t) -> "est"
(n, e) -> "ne"
(ne, w) -> "new"
... (and many more)

Step-by-step encoding:

Initial: Split "lowest" into characters
["l", "o", "w", "e", "s", "t", "</w>"]
Iteration 1: Check merge rule #1: (l, o) -> "lo"
Found! Merge the first occurrence
["lo", "w", "e", "s", "t", "</w>"]
Iteration 2: Check merge rule #2: (lo, w) -> "low"
Found! Merge the pair
["low", "e", "s", "t", "</w>"]
Iteration 3: Check merge rule #3: (e, s) -> "es"
Found! Merge the pair
["low", "es", "t", "</w>"]
Iteration 4: Check merge rule #4: (es, t) -> "est"
Found! Merge the pair
["low", "est", "</w>"]
Iteration 5: Check merge rule #5: (n, e) -> "ne"
Not found in current sequence, skip
Iteration 6: Check merge rule #6: (ne, w) -> "new"
Not found in current sequence, skip
... Continue checking all remaining merge rules ...
No more applicable merges found.
Final result: ["low", "est"]
```

Even though "lowest" wasn’t in training, BPE handles it by decomposing into known subwords.

Why BPE Works

BPE discovers linguistic structure automatically:

1. Frequency-based compression: Common words and phrases become single tokens, reducing sequence length
2. Morphological awareness: Suffixes like -ing, -tion, -est naturally emerge as tokens
3. Graceful degradation: Rare words decompose into known subwords rather than becoming unknown tokens
4. Deterministic: Same input always produces same output (important for reproducibility)

## Byte-Level BPE: Popularized by GPT-2

Standard BPE operates on characters. Byte-level BPE (used by GPT-2, GPT-3, GPT-4) operates on bytes (0-255).

Why bytes? Because any UTF-8 character is 1-4 bytes, so ANY text can be encoded.

```
Standard BPE might start with:
base_vocab = ['a', 'b', 'c', ..., 'z', 'A', 'B', ..., 'Z', '0', '1', ...]
Problem: What about Chinese characters? Emojis? Code?

Byte-level BPE starts with:
base_vocab = list(range(256))  # All possible bytes
Any UTF-8 character is 1-4 bytes, so ANY text can be encoded
```

Benefits:

- No unknown tokens ever: Any UTF-8 text can be encoded
- Language-agnostic: Works for any language without modification
- Fixed base vocabulary: Always exactly 256 tokens
- Handles everything: Emojis, code, special characters, etc.

## Code Example

python

```
import tiktoken

# GPT-2 uses byte-level BPE
tokenizer = tiktoken.get_encoding("gpt2")

# Tokenize some text
text = "tokenization is fascinating"
tokens = tokenizer.encode(text)

print(f"Text: {text}")
# Text: tokenization is fascinating
print(f"Token IDs: {tokens}")
# Token IDs: [30001, 1634, 318, 13899]
print("\nToken breakdown:")
for i, token_id in enumerate(tokens):
    token_str = tokenizer.decode([token_id])
    print(f"  Token {i+1}: ID={token_id:5d} -> '{token_str}'")

# Token breakdown:
#  Token 1: ID=30001 -> 'token'
#  Token 2: ID= 1634 -> 'ization'
#  Token 3: ID=  318 -> ' is'
#  Token 4: ID=13899 -> ' fascinating'


text = "supercalifragilisticexpialidocious"
tokens = tokenizer.encode(text)
print(f"Text: {text}")
# Text: supercalifragilisticexpialidocious
print(f"Token IDs: {tokens}")
# Token IDs: [16668, 9948, 361, 22562, 346, 396, 501, 42372, 498, 312, 32346]
print("\nToken breakdown:")
for i, token_id in enumerate(tokens):
    token_str = tokenizer.decode([token_id])
    print(f"  Token {i+1}: ID={token_id:5d} -> '{token_str}'")

# Token breakdown:
#  Token 1: ID=16668 -> 'super'
#  Token 2: ID= 9948 -> 'cal'
#  Token 3: ID=  361 -> 'if'
#  Token 4: ID=22562 -> 'rag'
#  Token 5: ID=  346 -> 'il'
#  Token 6: ID=  396 -> 'ist'
#  Token 7: ID=  501 -> 'ice'
#  Token 8: ID=42372 -> 'xp'
#  Token 9: ID=  498 -> 'ial'
#  Token 10: ID=  312 -> 'id'
#  Token 11: ID=32346 -> 'ocious'
```

## Limitations of BPE

While BPE is powerful, it has some limitations:

- Greedy algorithm: Locally optimal merges may not be globally optimal
- No probabilistic reasoning: Doesn’t consider context or multiple valid segmentations
- Morphologically rich languages: Languages like Turkish or Finnish with complex inflections may get suboptimal splits
- Rare word handling: Very rare words might decompose into single characters

## Who uses BPE?

BPE (especially byte-level BPE) is the most widely used tokenization method by GPT-2, GPT-3, GPT-4 (byte-level BPE), LLaMA, LLaMA 2, LLaMA 3 (byte-level BPE), Mistral (byte-level BPE), Claude (Anthropic’s models use BPE variants), and most open-source LLMs default to BPE.

## WordPiece

WordPiece is a subword tokenization algorithm developed at Google, originally for Japanese and Korean speech recognition, then adapted for BERT in 2018. It’s similar to BPE in structure but uses a different criterion for selecting which pairs to merge.

The core idea: start with a base vocabulary of individual characters, repeatedly merge the pair that maximizes the likelihood of the training data (rather than just frequency), and stop when you reach your target vocabulary size. This subtle change leads to better handling of rare words and more linguistically meaningful merges.

Note: Tokens starting words have no prefix (l, low, new), while
continuation tokens have ## prefix (#

[#est](https://x.com/search?q=%23est&src=hashtag_click)

, #

[#ing](https://x.com/search?q=%23ing&src=hashtag_click)

, #

[#s](https://x.com/search?q=%23s&src=hashtag_click)

).

```
Training text: "low low low low lowest newest"

Step 0: Initialisation
First, we split each word into characters. Tokens that don't start a word get the ## prefix.
Initial vocabulary: {l, o, w, n, e, s, t, ##o, ##w, ##e, ##s, ##t, ...}
Word representations: 
  "low" (4 times) -> [l, ##o, ##w]
  "lowest" (1 time) -> [l, ##o, ##w, ##e, ##s, ##t]
  "newest" (1 time) -> [n, ##e, ##w, ##e, ##s, ##t]

Step 1: Count individual token frequencies
We need to know how often each token appears individually:
Token Frequencies: {l: 5, ##o: 5, ##w: 6, ##e: 3, ##s: 2, ##t: 2, n: 1, ...}

Step 2: Count all adjacent pairs and compute WordPiece scores
We count pair frequencies and compute: score = count(ab) / (count(a) × count(b))

Pair (l, ##o):
count(l, ##o) = 5
count(l) = 5, count(##o) = 5
score = 5 / (5 × 5) = 0.2

Pair (##o, ##w):
count(##o, ##w) = 5
count(##o) = 5, count(##w) = 6
score = 5 / (5 × 6) = 0.167

Pair (##e, ##s):
count(##e, ##s) = 2
count(##e) = 3, count(##s) = 2
score = 2 / (3 × 2) = 0.333

Pair (##s, ##t):
count(##s, ##t) = 2
count(##s) = 2, count(##t) = 2
score = 2 / (2 × 2) = 0.5 -> Highest score!
The pair (##s, ##t) has the highest WordPiece score (0.5), so we merge it first.

Step 3: Merge the highest scoring pair
Create a new token ##st by merging ##s and ##t:
Vocabulary now includes: {..., ##st}
Word representations update:
  "lowest" -> [l, ##o, ##w, ##e, ##st]
  "newest" -> [n, ##e, ##w, ##e, ##st]

Step 4: Recount and merge again
Recount token frequencies and pair scores, then merge the next highest-scoring pair.
This process continues until the vocabulary reaches the target size.

After several more iterations, our vocabulary might look like:
{l, o, w, e, s, t, n, ##o, ##w, ##e, ##s, ##t, ##st, ##est, lo, low, ne, new, ...}
```

Notice what happened:

- WordPiece merged (#

  [#s](https://x.com/search?q=%23s&src=hashtag_click)

  , #

  [#t](https://x.com/search?q=%23t&src=hashtag_click)

  ) before (l, #

  [#o](https://x.com/search?q=%23o&src=hashtag_click)

  ) even though (l, #

  [#o](https://x.com/search?q=%23o&src=hashtag_click)

  ) is more frequent, because (#

  [#s](https://x.com/search?q=%23s&src=hashtag_click)

  , #

  [#t](https://x.com/search?q=%23t&src=hashtag_click)

  ) has a higher score, the tokens appear together more consistently
- More informative merges: The scoring formula favors pairs where individual tokens are rare but the combination is common
- Morphological patterns still emerge: #

  [#est](https://x.com/search?q=%23est&src=hashtag_click)

  , low, new
- The ## prefix explicitly marks continuation tokens vs. word-starting tokens

Encoding Phase: Tokenizing New Text

WordPiece uses a longest-match-first (greedy left-to-right) algorithm for encoding:

1. Start from the beginning of the word
2. Find the longest token that matches from the current position
3. Mark it with ## if it’s not at word start
4. Move to the next position
5. Repeat until the entire word is tokenized

```
Example: encoding "tokenization" (not in training data)

Let's say our trained WordPiece tokenizer has this vocabulary:
{token, ##ization, ##tion, ##ing, to, ken, ...}

Step-by-step encoding:
Start: "tokenization"

Iteration 1: Find longest match from start
Check: "tokenization" -> not in vocab
Check: "tokenizatio" -> not in vocab
Check: "tokenizati" -> not in vocab
...
Check: "token" -> found in vocab!
Result: ["token"], remaining: "ization"

Iteration 2: Find longest match from "ization"
Check: "ization" -> found in vocab!
Mark with ## (not at word start)
Result: ["token", "##ization"]
```

Even though "tokenization" wasn’t in training, WordPiece handles it by decomposing into known subwords. The ## prefix indicates that "#

[#ization](https://x.com/search?q=%23ization&src=hashtag_click)

" is a continuation token (not at word start).

## Why WordPiece Works

WordPiece discovers linguistic structure with a more principled approach:

1. Likelihood-based compression: Merges are chosen to maximize the probability of the training data, not just frequency
2. Better rare word handling: The scoring formula favors informative pairs, leading to better decomposition of rare words
3. Morphological awareness: Suffixes and prefixes naturally emerge, similar to BPE
4. The ## convention: Explicitly marks word boundaries, helping the model understand morphology

## The ## Convention

WordPiece uses a special marker to distinguish word-starting tokens from word-continuing tokens:

- Tokens at the start of a word have no prefix: "token"
- Tokens inside a word are prefixed with ##: "#

  [#ization](https://x.com/search?q=%23ization&src=hashtag_click)

  "

This helps the model understand:

- "token" can start a word
- "#

  [#ization](https://x.com/search?q=%23ization&src=hashtag_click)

  " is a continuation (suffix)
- "#

  [#s](https://x.com/search?q=%23s&src=hashtag_click)

  " is a continuation (plural marker)

Example: “I like tokens” -> [“I”, “like”, “token”, “#

[#s](https://x.com/search?q=%23s&src=hashtag_click)

”]

## Code Example

python

```
from transformers import AutoTokenizer

# BERT uses WordPiece
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")

# Tokenize some text
text = "tokenization is fascinating"
tokens = tokenizer.tokenize(text)
ids = tokenizer.encode(text, add_special_tokens=False)

print(f"Text: {text}")
# Text: tokenization is fascinating
print(f"Tokens: {tokens}")
# Tokens: ['token', '##ization', 'is', 'fascinating']
print(f"Token IDs: {ids}")
# Token IDs: [19204, 3989, 2003, 17160]

print("\nToken breakdown:")
for i, token in enumerate(tokens):
    token_id = tokenizer.convert_tokens_to_ids(token)
    print(f"  Token {i+1}: '{token}' -> ID={token_id}")

# Token breakdown:
#  Token 1: 'token' -> ID=19204
#  Token 2: '##ization' -> ID=3989
#  Token 3: 'is' -> ID=2003
#  Token 4: 'fascinating' -> ID=17160

# Decode back
print(f"\nDecoded: {tokenizer.decode(ids)}")
# Decoded: tokenization is fascinating

# BERT adds special tokens automatically
full_encoding = tokenizer.encode(text)
print(f"\nWith special tokens: {tokenizer.convert_ids_to_tokens(full_encoding)}")
# ['[CLS]', 'token', '##ization', 'is', 'fascinating', '[SEP]']
```

## Limitations of WordPiece

While WordPiece is powerful, it has some limitations:

- Can have unknown tokens: Unlike byte-level BPE, WordPiece can’t encode everything, unknown characters become [UNK]
- Greedy encoding: Longest match-first algorithm may not always be optimal
- More complex scoring: The likelihood formula is more expensive to compute than frequency
- Language-specific: Works best for languages similar to the training data

## Who uses WordPiece?

WordPiece is primarily used in the BERT ecosystem by BERT, BERT-base, BERT-large (all variants), DistilBERT (distilled BERT), Electra (efficiently trained BERT variant), and most encoder-only models in the BERT family.

## Unigram

Unigram is a subword tokenization algorithm introduced by Kudo (2018) as part of SentencePiece. Unlike BPE and WordPiece which build vocabulary bottom-up (starting small and growing), Unigram works top-down: start with a large vocabulary of candidate tokens and iteratively prune the least useful ones until reaching the target vocabulary size.

The core idea: assign a probability to each token, compute how much removing each token would hurt the overall likelihood of the training data, and remove the tokens with lowest impact. This probabilistic approach allows for multiple valid segmentations of the same word, which can be used for data augmentation during training.

Training Phase: Building the Vocabulary

```
Training text: "low low low low lowest newest"

Step 0: Initialisation - Build Large Candidate Vocabulary
Start by generating all possible substrings from the training corpus up to a maximum length (e.g., 16 characters).

For our training corpus, we extract all unique substrings:
All single characters: {l, o, w, e, s, t, n}
All 2-grams: {lo, ow, we, es, st, ne, ew}
All 3-grams: {low, owe, wes, est, new, ewe, wes}
All 4-grams: {lowe, owest, west, este, ...}
All 5-grams: {lowes, owest, ...}
... up to maximum length

Initial Vocabulary (simplified):
{l, o, w, e, s, t, n, lo, ow, we, es, st, ne, ew, low, owe, wes, est, new, lowe, owest, west, este, lowest, newest, ...}
Size: ~10,000+ candidate tokens

Step 1: Estimate Token Probabilities Using EM Algorithm
We need to assign probabilities to each token. The Expectation-Maximization (EM) algorithm does this:

E-step: For each word in training data, find all possible segmentations and their probabilities
M-step: Update token probabilities based on how often they appear in the best segmentations

Initial probabilities (uniform or frequency-based):
P(low) = 0.04 (appears 4 times out of 6 words in the training corpus)
P(lowest) = 0.01 (appears 1 time)
P(newest) = 0.01 (appears 1 time)
P(l) = 0.05
P(o) = 0.08
P(w) = 0.10
... (all other tokens have small probabilities)
After EM iterations, probabilities converge to:
P(low) = 0.03
P(est) = 0.02
P(new) = 0.01
P(lowest) = 0.005
P(newest) = 0.005
P(l) = 0.05
P(o) = 0.08
... (probabilities reflect actual usefulness)

Step 2: Compute Loss Impact for Each Token
For each token, we compute: "If we remove this token, how much does the total log-likelihood decrease?"

The loss impact formula:
L(token) = -log(P(alternative_segmentation)) - (-log(P(current_segmentation)))
Where:
P(current_segmentation) uses the token
P(alternative_segmentation) is the best segmentation without the token

Example 1: Removing "low" (appears 4 times)
Current segmentation: ["low"] -> P = 0.03
Alternative (without "low"): ["l", "o", "w"] -> P = P(l) × P(o) × P(w) = 0.05 × 0.08 × 0.10 = 0.0004
Loss impact = -log(0.0004) - (-log(0.03))
= 7.82 - 3.51
= 4.31 (high impact - keep this token!)

Example 2: Removing "newest" (appears 1 time, rare)
Current segmentation: ["newest"] -> P = 0.005
Alternative: ["new", "est"] -> P = P(new) × P(est) = 0.01 × 0.02 = 0.0002
Loss impact = -log(0.0002) - (-log(0.005))
= 8.52 - 5.30
= 3.22 (medium impact)

Example 3: Removing "xyz" (rare, appears once in a different word)
Current: ["xyz"] -> P = 0.0001
Alternative: ["x", "y", "z"] -> P = P(x) × P(y) × P(z) = 0.001 × 0.001 × 0.001 = 0.000000001
Loss impact = -log(0.000000001) - (-log(0.0001))
= 20.72 - 9.21
= 11.51 (but this token is so rare, removing it barely affects overall corpus likelihood)

Actually, we compute loss impact across the ENTIRE corpus:
L(token) = Σ [loss when removing token from word_i] for all words in corpus

Step 3: Remove Bottom Tokens
Sort all tokens by loss impact (ascending). Remove the bottom 20% (lowest impact tokens).
Tokens sorted by loss impact:
"low" -> 4.31 (keep - high impact)
"est" -> 3.50 (keep - high impact)
"new" -> 2.80 (keep - medium impact)
"newest" -> 3.22 (keep - medium impact)
"lowest" -> 2.50 (keep - medium impact)
...
"xyz" -> 0.01 (remove - very low impact)
"abc" -> 0.005 (remove - very low impact)
"qwerty" -> 0.001 (remove - very low impact)
Remove bottom 20%: ~2,000 tokens removed
Remaining vocabulary: ~8,000 tokens

Step 4: Re-estimate Probabilities
After pruning, we need to update probabilities because:
Some tokens are gone, so segmentations change
Remaining tokens might be used more/less frequently

Run EM algorithm again on the pruned vocabulary:
E-step: Find best segmentations using remaining tokens
M-step: Update probabilities based on new segmentations
New probabilities:
P(low) = 0.035 (increased - now more important)
P(est) = 0.025 (increased)
P(new) = 0.015 (increased)
... (probabilities rebalanced)

Step 5: Repeat Pruning
Go back to Step 2. Compute loss impacts again with updated probabilities.
Remove another 20% of lowest-impact tokens.
Re-estimate probabilities.
Repeat until vocabulary reaches target size (e.g., 8,000 tokens).
After several iterations:
Vocabulary: {l, o, w, e, s, t, n, lo, low, es, est, ne, new, lowest, ...}
Size: 8,000 tokens (down from 10,000+ candidate tokens)
```

Notice what happened:

- Top-down approach: Started large, pruned down (opposite of BPE/WordPiece)
- Probabilistic: Each token has a probability, not just a frequency
- Loss-based pruning: Removes tokens that don’t help compression
- Common patterns preserved: low, est, new remain because they’re useful

Encoding Phase: Tokenizing New Text

Unigram uses the Viterbi algorithm to find the most probable segmentation. Unlike BPE/WordPiece which have a single deterministic segmentation, Unigram can have multiple valid segmentations with different probabilities.

1. For each possible way to segment the word, compute the total probability
2. The probability of a segmentation is the product of individual token probabilities
3. Choose the segmentation with highest probability
4. Optionally, sample from the distribution (for data augmentation)

```
Example: encoding "lowest" (not in training data)

Let's say our trained Unigram tokenizer has these tokens with probabilities:
P(low) = 0.03
P(est) = 0.02
P(lo) = 0.01
P(we) = 0.005
P(st) = 0.008
P(l) = 0.05
P(o) = 0.08
P(w) = 0.10
P(e) = 0.06
P(s) = 0.04
P(t) = 0.04

Possible segmentations and their probabilities:
Option 1: ["low", "est"]
P = P(low) × P(est) = 0.03 × 0.02 = 0.0006

Option 2: ["lo", "we", "st"]
P = P(lo) × P(we) × P(st) = 0.01 × 0.005 × 0.008 = 0.0000004

Option 3: ["l", "o", "w", "e", "s", "t"]
P = P(l) × P(o) × P(w) × P(e) × P(s) × P(t)
P = 0.05 × 0.08 × 0.10 × 0.06 × 0.04 × 0.04 = 0.0000000384

Option 1 has the highest probability, so we choose: ["low", "est"]
```

The Viterbi algorithm efficiently finds this optimal segmentation using dynamic programming, avoiding the need to check all possible segmentations.

## Why Unigram Works

Unigram’s probabilistic approach offers several advantages:

1. Principled optimization: Directly optimizes for compression quality (likelihood) rather than greedy frequency based merging
2. Multiple valid segmentations: Can sample different tokenizations during training, acting as data augmentation
3. Better rare word handling: The loss-based pruning naturally keeps tokens that help with rare words
4. Flexible: Can trade off between compression and vocabulary size more explicitly

## Code Example

python

```
from transformers import AutoTokenizer

# T5 uses Unigram via SentencePiece
tokenizer = AutoTokenizer.from_pretrained("t5-base")

# Tokenize some text
text = "tokenization is fascinating"
tokens = tokenizer.tokenize(text)
ids = tokenizer.encode(text, add_special_tokens=False)

print(f"Text: {text}")
# Text: tokenization is fascinating
print(f"Tokens: {tokens}")
# Tokens: ['▁token', 'ization', '▁is', '▁fascinating']
print(f"Token IDs: {ids}")
# Token IDs: [14145, 1707, 19, 8899]

print("\nToken breakdown:")
for i, token in enumerate(tokens):
    token_id = tokenizer.convert_tokens_to_ids(token)
    print(f"  Token {i+1}: '{token}' -> ID={token_id}")

# Token breakdown:
#   Token 1: '▁token' -> ID=14145
#   Token 2: 'ization' -> ID=1707
#   Token 3: '▁is' -> ID=19
#   Token 4: '▁fascinating' -> ID=8899

# Decode back
print(f"\nDecoded: {tokenizer.decode(ids)}")
# Decoded: tokenization is fascinating

# Notice the ▁ prefix marks word boundaries
text2 = "I love tokenization"
print(f"\n'{text2}' tokenized:")
print(tokenizer.tokenize(text2))
# ['▁I', '▁love', '▁token', 'ization']
```

## Limitations of Unigram

While Unigram is powerful, it has some limitations:

- More complex to implement: Requires EM algorithm for probability estimation and Viterbi for encoding
- Slower training: The iterative pruning and probability re-estimation is computationally expensive
- Non-deterministic by default: Multiple valid segmentations can be confusing (though this is also a feature)
- Requires initial vocabulary: Need to generate candidate tokens upfront (though SentencePiece handles this)

## Who uses Unigram?

Unigram is primarily used via SentencePiece in multilingual and encoder-decoder models by T5, mT5 (text-to-text transfer transformer), ALBERT (A Lite BERT), XLNet (generalized autoregressive pretraining), mBART (multilingual BART), and most multilingual models (SentencePiece is language agnostic).

Note: Unigram is less common than BPE for decoder only models (GPT, LLaMA). It’s more popular for encoder-decoder architectures and multilingual applications where SentencePiece’s language-agnostic design shines.

# Real World Comparisons: BPE vs WordPiece vs Unigram

Theory is one thing, but seeing how different tokenizers handle real-world text reveals their practical differences. Let’s compare BPE, WordPiece, and Unigram on a complete short story to see the actual impact.

## Compression Ratio

We will use 

[“A Transgression”](https://www.libraryofshortstories.com/onlinereader/a-transgression)

 by Anton Chekhov as our test case.

python

```
from transformers import AutoTokenizer

# Load the story
with open("a-transgression.txt", "r", encoding="utf-8") as f:
    story = f.read()

print(f"Story length: {len(story):,} characters")
print(f"Story preview: {story[:200]}...")

# Initialize tokenizers
tokenizer_bpe = AutoTokenizer.from_pretrained("gpt2")
tokenizer_wp = AutoTokenizer.from_pretrained("bert-base-uncased")
tokenizer_uni = AutoTokenizer.from_pretrained("t5-base")

# Tokenize the entire story
tokens_bpe = tokenizer_bpe.tokenize(story)
tokens_wp = tokenizer_wp.tokenize(story)
tokens_uni = tokenizer_uni.tokenize(story)

ids_bpe = tokenizer_bpe.encode(story, add_special_tokens=False)
ids_wp = tokenizer_wp.encode(story, add_special_tokens=False)
ids_uni = tokenizer_uni.encode(story, add_special_tokens=False)

print(f"\n{'Tokenizer':<15} {'Tokens':<10} {'Characters/Token':<20} {'Compression Ratio'}")
print("-" * 70)
print(f"{'BPE (GPT-2)':<15} {len(tokens_bpe):<10,} {len(story)/len(tokens_bpe):<20.2f} {len(tokens_bpe)/len(story.split()):.2f}x")
print(f"{'WordPiece (BERT)':<15} {len(tokens_wp):<10,} {len(story)/len(tokens_wp):<20.2f} {len(tokens_wp)/len(story.split()):.2f}x")
print(f"{'Unigram (T5)':<15} {len(tokens_uni):<10,} {len(story)/len(tokens_uni):<20.2f} {len(tokens_uni)/len(story.split()):.2f}x")
```

Output:

```
Story length: 9,941 characters
Story preview: A collegiate assessor called Miguev stopped at a telegraph-post in the course of his evening walk and heaved a deep sigh. A week before, as he was returning home from his evening walk, he had been ove...

Tokenizer       Tokens     Characters/Token     Compression Ratio
----------------------------------------------------------------------
BPE (GPT-2)      2,829      3.51                 1.48x
WordPiece (BERT) 2,584      3.85                 1.35x
Unigram (T5)     3,068      3.24                 1.61x
```

Results:

- WordPiece produces the fewest tokens, 2,584 tokens (9% fewer than BPE)
- Unigram produces the most tokens, 3,068 tokens (19% more than WordPiece)
- BPE is in the middle, 2,829 tokens

This contradicts common assumptions! Let’s understand why.

## Why WordPiece Wins

WordPiece’s likelihood based merging creates more efficient merges for common English text. The ## convention might seem verbose, but it actually helps WordPiece create better subword units:

python

```
# Let's examine some specific words from the story
sample_words = ["collegiate", "assessor", "telegraph-post", "returning"]

print(f"{'Word':<20} {'BPE':<25} {'WordPiece':<25} {'Unigram':<25}")
print("-" * 100)

for word in sample_words:
    bpe_tokens = tokenizer_bpe.tokenize(word)
    wp_tokens = tokenizer_wp.tokenize(word)
    uni_tokens = tokenizer_uni.tokenize(word)
    
    print(f"{word:<20} {str(bpe_tokens):<25} {str(wp_tokens):<25} {str(uni_tokens):<25}")
```

Output:

```
Word                 BPE                              WordPiece                 Unigram                  
----------------------------------------------------------------------------------------------------
collegiate           ['col', 'leg', 'iate']          ['collegiate']             ['▁', 'collegiate']      
assessor             ['ass', 'essor']                ['assess', '##or']         ['▁assess', 'or']        
telegraph-post       ['te', 'legraph', '-', 'post']  ['telegraph', '-', 'post'] ['▁', 'tele', 'graph', '-', 'post']
returning            ['return', 'ing']               ['returning']              ['▁returning']
```

Key insight: For well-trained tokenizers on similar text, WordPiece’s likelihood based merging can create more efficient vocabulary for common English patterns.

## Why Unigram Produces More Tokens

Unigram’s probabilistic approach can be less efficient for this type of text because:

- It was trained on different data (T5’s training corpus)
- The vocabulary might not be optimized for literary/narrative text
- The pruning process may have removed tokens that would help with this specific domain

## Cost Impact

python

```
# API pricing: $0.002 per 1K tokens (example rate)
cost_per_1k = 0.002

# Scenario: Processing 10,000 stories per month
stories_per_month = 10_000

bpe_cost = (len(tokens_bpe) * cost_per_1k / 1000) * stories_per_month
wp_cost = (len(tokens_wp) * cost_per_1k / 1000) * stories_per_month
uni_cost = (len(tokens_uni) * cost_per_1k / 1000) * stories_per_month

print(f"\n{'Scenario':<30} {'BPE':<15} {'WordPiece':<15} {'Unigram':<15}")
print("-" * 75)
print(f"{'Cost per story':<30} ${bpe_cost/stories_per_month:.4f}      ${wp_cost/stories_per_month:.4f}      ${uni_cost/stories_per_month:.4f}")
print(f"{'Monthly cost (10K stories)':<30} ${bpe_cost:,.2f}      ${wp_cost:,.2f}      ${uni_cost:,.2f}")
print(f"{'Annual cost (10K stories)':<30} ${bpe_cost*12:,.2f}      ${wp_cost*12:,.2f}      ${uni_cost*12:,.2f}")

print(f"\nSavings:")
print(f"  WordPiece vs BPE:     ${bpe_cost - wp_cost:,.2f}/month (${(bpe_cost - wp_cost)*12:,.2f}/year)")
print(f"  WordPiece vs Unigram: ${uni_cost - wp_cost:,.2f}/month (${(uni_cost - wp_cost)*12:,.2f}/year)")
```

Output:

```
Scenario                       BPE             WordPiece       Unigram        
---------------------------------------------------------------------------
Cost per story                 $0.0057         $0.0052        $0.0061
Monthly cost (10K stories)     $56.58          $51.68         $61.36
Annual cost (10K stories)      $678.96         $620.16        $736.32

Savings:
  WordPiece vs BPE:     $4.90/month ($58.80/year)
  WordPiece vs Unigram: $9.68/month ($116.16/year)
```

The Key Lesson: Context Matters

These results show that the “best” tokenizer depends on your text:

1. WordPiece excels here because:

- BERT was trained on similar English text (Wikipedia, books)
- The vocabulary is well-suited for narrative prose
- Likelihood based merging works well for common English patterns

2. Unigram struggles here because:

- T5 was trained on different data (web text, C4 corpus)
- The vocabulary may not be optimized for literary text
- Different domain = different optimal vocabulary

3. BPE is middle ground because:

- GPT-2 was trained on web text (diverse but different from literature)
- Byte-level encoding is robust but not domain-optimized

# Real-World Tokenization Challenges

Understanding tokenization algorithms is one thing. Understanding how tokenization affects real-world model behavior is another. Let’s explore the challenges and quirks that come with tokenization.

## Why LLMs Struggle with Certain Tasks

Many of the “strange” behaviors of language models trace back to tokenization. When people say “GPT can’t do math” or “LLMs struggle with spelling,” tokenization is often the culprit.

The Arithmetic Problem

python

```
import tiktoken
enc = tiktoken.get_encoding("gpt2")

# How GPT-2 sees arithmetic
print(enc.encode("317+492=809"))
# [34125, 10, 40256, 28, 34583]

# Each number becomes multiple tokens with no consistent pattern:
for num in ["317", "492", "809"]:
    tokens = enc.encode(num)
    print(f"{num} -> {[enc.decode([t]) for t in tokens]}")
# 317 -> ['317']
# 492 -> ['492']  
# 809 -> ['809']
```

The model doesn’t see 317 + 492 = 809. It sees a messy sequence where some numbers are single tokens and others are split inconsistently. Learning addition becomes much harder when the representation varies.

The Spelling Problem

python

```
# Can a model spell "hello" backwards?
import tiktoken

tokenizer = tiktoken.get_encoding("gpt2")
print(tokenizer.encode("hello"))
# [31373]  - Just one token

# The model never sees individual letters, it sees "hello" as an atomic unit
# To spell backwards, it would need to decompose something it treats as indivisible
```

This is why LLMs struggle with:

- Counting letters in words
- Reversing strings
- Rhyming (which requires phoneme-level understanding)
- Anagram solving

The Trailing Whitespace Problem

python

```
import tiktoken

tokenizer = tiktoken.get_encoding("gpt2")
# These are DIFFERENT tokens
print(tokenizer.encode("hello"))   # [31373]
print(tokenizer.encode("hello "))  # [31373, 220]
print(tokenizer.encode(" hello"))  # [23748]

# " hello" (with leading space) is a completely different token than "hello"!
```

This is why prompts can be sensitive to whitespace. A trailing space changes the tokenization entirely.

Multilingual Tokenization: The Hidden Cost

Tokenizers trained primarily on English text create a tokenization tax for other languages:

python

```
from transformers import AutoTokenizer
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# Same meaning, vastly different token counts
texts = {
    "English": "Hello, how are you today?",
    "Chinese": "你好，今天你好吗？",
    "Arabic": "مرحبا، كيف حالك اليوم؟",
    "Hindi": "नमस्ते, आज आप कैसे हैं?",
}

for lang, text in texts.items():
    tokens = tokenizer.encode(text)
    chars_per_token = len(text) / len(tokens)
    print(f"{lang:10} | {len(tokens):3} tokens | {chars_per_token:.2f} chars/token | {text}")
```

Output:

```
English    |   7 tokens | 3.57 chars/token | Hello, how are you today?
Chinese    |  19 tokens | 0.47 chars/token | 你好，今天你好吗？
Arabic     |  24 tokens | 0.92 chars/token | مرحبا، كيف حالك اليوم؟
Hindi      |  36 tokens | 0.64 chars/token | नमस्ते, आज आप कैसे हैं?
```

The implications:

- API costs: Non-English users pay 3-5x more for the same semantic content
- Context window: Less information fits in the same context length
- Model quality: Rare tokens have weaker embeddings (less training data)

Solutions:

- Use multilingual tokenizers (SentencePiece, mT5)
- Train domain-specific tokenizers for your target language
- Consider byte-level models for universal coverage

Vocabulary Size Trade-offs

Why does GPT-2 use exactly 50,257 tokens? The number isn’t arbitrary, it’s 50,000 BPE merges + 256 byte tokens + 1 end-of-text token. But why 50,000 merges instead of 10,000 or 100,000?

Consider the sentence “The tokenization process is fascinating”:

- With a small vocabulary: ["The", "token", "ization", "pro", "cess", "is", "fas", "cin", "ating"] -> 9 tokens
- With a large vocabulary: ["The", "tokenization", "process", "is", "fascinating"] -> 5 tokens

Same text, different token counts. This difference compounds across millions of documents.

The core trade-off is between two costs:

[![Image](https://pbs.twimg.com/media/G-1swp2a8AAFyhU?format=png&name=small)](/manthanguptaa/article/2012388383642022127/media/2012384191628111872)

Where the costs come from:

1. Embedding matrix memory = vocab\_size × embedding\_dim × bytes\_per\_paramGPT-2 (50K vocab, 768-dim): ~150 MB
   GPT-3 (50K vocab, 12,288-dim): ~2.4 GB
   Double the vocab -> double the embedding memory
2. Softmax bottleneck: Every token prediction requires computing probabilities over the entire vocabulary. With 100K tokens, that’s 100K operations per generated token -> this becomes the inference bottleneck for large vocabularies.
3. Sequence length vs attention cost: Attention is O(n²) where n is sequence length. A smaller vocabulary means more tokens per sentence, which quadratically increases attention computation. This often outweighs the savings from a smaller embedding matrix.

The sweet spot (32K-64K):

- Keeps sequences reasonably short (good compression)
- Embedding matrix stays manageable
- Softmax doesn’t dominate inference time
- 32K specifically fits in 16-bit integers, simplifying implementation

## Special Tokens Explained

Every tokenizer has special tokens that control model behavior:

[![Image](https://pbs.twimg.com/media/G-1s7GaaUAEoKYS?format=png&name=small)](/manthanguptaa/article/2012388383642022127/media/2012384371093950465)

python

```
from transformers import AutoTokenizer

# BERT's special tokens
bert_tok = AutoTokenizer.from_pretrained("bert-base-uncased")
encoded = bert_tok.encode("Hello world")
print(bert_tok.convert_ids_to_tokens(encoded))
# ['[CLS]', 'hello', 'world', '[SEP]']

# GPT-2's special tokens
gpt2_tok = AutoTokenizer.from_pretrained("gpt2")
print(f"End of text token: {gpt2_tok.eos_token} (ID: {gpt2_tok.eos_token_id})")
# End of text token: <|endoftext|> (ID: 50256)
```

Why special tokens matter:

- [CLS] aggregates sentence meaning for classification tasks
- [SEP] tells the model where one segment ends and another begins
- [PAD] is ignored during attention (via attention masks)
- <|endoftext|> signals document boundaries during training

## Tokenization for Code

Code has unique tokenization challenges:

python

```
import tiktoken
enc = tiktoken.get_encoding("gpt2")

code = '''def hello_world():
    print("Hello, World!")
'''

tokens = enc.encode(code)
print(f"Token count: {len(tokens)}")
for t in tokens:
    print(f"  {t:5d} -> {repr(enc.decode([t]))}")
```

Output:

```
Token count: 17
   4299 -> 'def'
  23748 -> ' hello'
     62 -> '_'
   6894 -> 'world'
  33529 -> '():'
    198 -> '\n'
    220 -> ' '
    220 -> ' '
    220 -> ' '
   3601 -> ' print'
   7203 -> '("'
  15496 -> 'Hello'
     11 -> ','
   2159 -> ' World'
   2474 -> '!"'
      8 -> ')'
    198 -> '\n'
```

Key observations:

- Whitespace handling varies: Indentation may split into multiple space tokens rather than a single ' ' token
- Common patterns merge: '():' and '("' become single tokens
- Snake\_case splits: hello\_world becomes ['hello', '\_', 'world']
- CamelCase varies: Depends on frequency in training data

Code-specific challenges:

- Variable names often split unpredictably
- Rare function names decompose to characters
- Different languages (Python vs Rust vs Go) tokenize differently
- Comments in non-English get heavily penalized

Best practices for code:

- Use tokenizers trained on code (Codex, StarCoder tokenizers)
- Be aware that renaming variables can change token count
- Consider the tokenization when crafting prompts for code generation

# Building Your Own Tokenizer

Now that we have a better understanding of how different tokenizers work, let’s build our own tokenizer from scratch.

## Step 1: Setup and Data Preparation

First, let’s set up our environment and prepare the training data:

python

```
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import Whitespace
from tokenizers.processors import BertProcessing
from tokenizers.decoders import BPEDecoder
from transformers import PreTrainedTokenizerFast

with open("a-transgression.txt", "r", encoding="utf-8") as f:
    corpus = f.read()

print(f"Corpus size: {len(corpus):,} characters")
print(f"Corpus preview: {corpus[:200]}...")
# Output
# Corpus size: 9,941 characters
# Corpus preview: A collegiate assessor called Miguev stopped at a telegraph-post in the course of his evening walk and heaved a deep sigh. A week before, as he was returning home from his evening walk, he had been ove...
```

## Step 2: Initialize the Tokenizer

python

```
# Initialize a blank BPE tokenizer
tokenizer = Tokenizer(BPE(unk_token="<|unk|>"))

# Set pre-tokenizer (splits on whitespace)
tokenizer.pre_tokenizer = Whitespace()
```

What’s happening:

- BPE(unk\_token="<|unk|>") creates a BPE model with an unknown token
- Whitespace() pre-tokenizer splits text on whitespace before BPE
- This is the simplest setup - you can customize later

## Step 3: Configure the Trainer

python

```
# Define special tokens
special_tokens = [
    "<|unk|>",      # Unknown token
    "<|pad|>",      # Padding token
    "<|bos|>",      # Beginning of sequence
    "<|eos|>",      # End of sequence
]

# Create trainer with parameters
trainer = BpeTrainer(
    vocab_size=8000,           # Target vocabulary size
    min_frequency=2,            # Minimum frequency for a token to be included
    special_tokens=special_tokens,
    show_progress=True,         # Show training progress
)

print(f"Trainer configured: vocab_size={8000}, min_frequency={2}")
```

Key parameters:

- vocab\_size: Target vocabulary size (common: 8K, 16K, 32K, 50K)
- min\_frequency: Tokens must appear at least this many times
- special\_tokens: Control tokens for your model

## Step 4: Train the Tokenizer

python

```
# Prepare training data
# tokenizers expects a list of strings or file paths
training_files = ["a-transgression.txt"]

# Train the tokenizer
tokenizer.train(training_files, trainer)

print("Training complete!")
print(f"Vocabulary size: {len(tokenizer.get_vocab())}")
```

## Step 5: Add Post-Processing

python

```
# Add post-processor (adds special tokens)
# This adds <|bos|> at start and <|eos|> at end
tokenizer.post_processor = BertProcessing(
    ("<|eos|>", tokenizer.token_to_id("<|eos|>")),
    ("<|bos|>", tokenizer.token_to_id("<|bos|>")),
)

# Set decoder (converts tokens back to text)
tokenizer.decoder = BPEDecoder()

print("Post-processing configured")
```

## Step 6: Test The Tokenizer

python

```
# Test encoding
text = "The tokenization process converts text into numbers."
encoding = tokenizer.encode(text)

print(f"Original text: {text}")
print(f"Token IDs: {encoding.ids}")
print(f"Tokens: {encoding.tokens}")
print(f"Number of tokens: {len(encoding.ids)}")

# Test decoding
decoded = tokenizer.decode(encoding.ids)
print(f"Decoded text: {decoded}")
```

Output:

```
Original text: The tokenization process converts text into numbers.
Token IDs: [2, 181, 77, 40, 81, 197, 165, 125, 191, 205, 7, 3]
Tokens: ['<|bos|>', 'The', 'token', 'ization', 'process', 'converts', 'text', 'into', 'numbers', '.', '<|eos|>']
Number of tokens: 12
Decoded text: The tokenization process converts text into numbers.
```

Note: Token IDs and counts will vary based on your training corpus and vocabulary size.

## Step 7: Save and Load

python

```
# Save tokenizer
tokenizer.save("my_tokenizer.json")

print("Tokenizer saved to my_tokenizer.json")

# Load it back
loaded_tokenizer = Tokenizer.from_file("my_tokenizer.json")

# Test that it works
test_text = "Hello world"
encoding = loaded_tokenizer.encode(test_text)
print(f"Loaded tokenizer works: {loaded_tokenizer.decode(encoding.ids)}")
```

## Step 8: Use with Transformers

python

```
# Wrap in PreTrainedTokenizerFast for Transformers compatibility
wrapped_tokenizer = PreTrainedTokenizerFast(
    tokenizer_object=tokenizer,
    bos_token="<|bos|>",
    eos_token="<|eos|>",
    unk_token="<|unk|>",
    pad_token="<|pad|>",
)

# Now you can use it like any HuggingFace tokenizer
text = "The tokenization process"
tokens = wrapped_tokenizer.tokenize(text)
ids = wrapped_tokenizer.encode(text)

print(f"Tokens: {tokens}")
print(f"IDs: {ids}")

# Save for Transformers
wrapped_tokenizer.save_pretrained("./my_tokenizer")
```

## Complete Code

Here’s all the steps combined into a single script:

python

```
from tokenizers import Tokenizer
from tokenizers.models import BPE
from tokenizers.trainers import BpeTrainer
from tokenizers.pre_tokenizers import Whitespace
from tokenizers.processors import BertProcessing
from tokenizers.decoders import BPEDecoder
from transformers import PreTrainedTokenizerFast

# Step 1: Setup and Data Preparation
with open("a-transgression.txt", "r", encoding="utf-8") as f:
    corpus = f.read()

print(f"Corpus size: {len(corpus):,} characters")
print(f"Corpus preview: {corpus[:200]}...")

# Step 2: Initialize the Tokenizer
tokenizer = Tokenizer(BPE(unk_token="<|unk|>"))
tokenizer.pre_tokenizer = Whitespace()

# Step 3: Configure the Trainer
special_tokens = [
    "<|unk|>",      # Unknown token
    "<|pad|>",      # Padding token
    "<|bos|>",      # Beginning of sequence
    "<|eos|>",      # End of sequence
]

trainer = BpeTrainer(
    vocab_size=8000,
    min_frequency=2,
    special_tokens=special_tokens,
    show_progress=True,
)

print(f"Trainer configured: vocab_size={8000}, min_frequency={2}")

# Step 4: Train the Tokenizer
training_files = ["a-transgression.txt"]
tokenizer.train(training_files, trainer)

print("Training complete!")
print(f"Vocabulary size: {len(tokenizer.get_vocab())}")

# Step 5: Add Post-Processing
tokenizer.post_processor = BertProcessing(
    ("<|eos|>", tokenizer.token_to_id("<|eos|>")),
    ("<|bos|>", tokenizer.token_to_id("<|bos|>")),
)
tokenizer.decoder = BPEDecoder()

print("Post-processing configured")

# Step 6: Test The Tokenizer
text = "The tokenization process converts text into numbers."
encoding = tokenizer.encode(text)

print(f"Original text: {text}")
print(f"Token IDs: {encoding.ids}")
print(f"Tokens: {encoding.tokens}")
print(f"Number of tokens: {len(encoding.ids)}")

decoded = tokenizer.decode(encoding.ids)
print(f"Decoded text: {decoded}")

# Step 7: Save and Load
tokenizer.save("my_tokenizer.json")
print("Tokenizer saved to my_tokenizer.json")

loaded_tokenizer = Tokenizer.from_file("my_tokenizer.json")
test_text = "Hello world"
encoding = loaded_tokenizer.encode(test_text)
print(f"Loaded tokenizer works: {loaded_tokenizer.decode(encoding.ids)}")

# Step 8: Use with Transformers
wrapped_tokenizer = PreTrainedTokenizerFast(
    tokenizer_object=tokenizer,
    bos_token="<|bos|>",
    eos_token="<|eos|>",
    unk_token="<|unk|>",
    pad_token="<|pad|>",
)

text = "The tokenization process"
tokens = wrapped_tokenizer.tokenize(text)
ids = wrapped_tokenizer.encode(text)

print(f"Tokens: {tokens}")
print(f"IDs: {ids}")

wrapped_tokenizer.save_pretrained("./my_tokenizer")
```

Output:

```
Corpus size: 9,941 characters
Corpus preview: A collegiate assessor called Miguev stopped at a telegraph-post in the course of his evening walk and heaved a deep sigh. A week before, as he was returning home from his evening walk, he had been ove...
Trainer configured: vocab_size=8000, min_frequency=2
[00:00:00] Pre-processing files (0 Mo)    ████████████████████████████████                100%
[00:00:00] Tokenize words                 ████████████████████████████████ 684      /      684
[00:00:00] Count pairs                    ████████████████████████████████ 684      /      684
[00:00:00] Compute merges                 ████████████████████████████████ 603      /      603
Training complete!
Vocabulary size: 664
Post-processing configured
Original text: The tokenization process converts text into numbers.
Token IDs: [2, 181, 77, 40, 81, 38, 55, 76, 197, 507, 32, 84, 48, 165, 143, 49, 48, 125, 53, 49, 191, 43, 50, 318, 205, 7, 3]
Tokens: ['<|bos|>', 'The', 'to', 'k', 'en', 'i', 'z', 'at', 'ion', 'pro', 'c', 'es', 's', 'con', 'ver', 't', 's', 'te', 'x', 't', 'into', 'n', 'u', 'mb', 'ers', '.', '<|eos|>']
Number of tokens: 27
Decoded text: Thetokenizationprocessconvertstextintonumbers.
Tokenizer saved to my_tokenizer.json
Loaded tokenizer works: Helloworld
Tokens: ['The', 'to', 'k', 'en', 'i', 'z', 'at', 'ion', 'pro', 'c', 'es', 's']
IDs: [2, 181, 77, 40, 81, 38, 55, 76, 197, 507, 32, 84, 48, 3]
```

## Training WordPiece or Unigram Instead

The HuggingFace tokenizers library also supports WordPiece and Unigram. Here’s how to swap algorithms:

python

```
from tokenizers import Tokenizer
from tokenizers.models import WordPiece, Unigram
from tokenizers.trainers import WordPieceTrainer, UnigramTrainer

# For WordPiece (BERT-style)
tokenizer_wp = Tokenizer(WordPiece(unk_token="[UNK]"))
trainer_wp = WordPieceTrainer(
    vocab_size=8000,
    special_tokens=["[UNK]", "[PAD]", "[CLS]", "[SEP]", "[MASK]"]
)

# For Unigram (T5/SentencePiece-style)
tokenizer_uni = Tokenizer(Unigram())
trainer_uni = UnigramTrainer(
    vocab_size=8000,
    special_tokens=["<unk>", "<pad>", "<s>", "</s>"]
)

# Training works the same way
tokenizer_wp.train(["corpus.txt"], trainer_wp)
tokenizer_uni.train(["corpus.txt"], trainer_uni)
```

Choose based on your use case:

- BPE: Best for general-purpose, especially if extending GPT-family models
- WordPiece: Best for BERT-family models or encoder-only architectures
- Unigram: Best for multilingual models or when you want subword regularization

# Tokenization Tools Comparison

## Quick Comparison Table

[![Image](https://pbs.twimg.com/media/G-1ufXTa0AENvoK?format=png&name=small)](/manthanguptaa/article/2012388383642022127/media/2012386093614944257)

## Performance Benchmarks

python

```
import time
from transformers import AutoTokenizer
import tiktoken
from tokenizers import Tokenizer
import sentencepiece as spm

text = "The tokenization process converts text into numbers. " * 1000  # ~10K chars
iterations = 100

# HuggingFace Transformers (GPT-2)
start = time.time()
tokenizer_hf = AutoTokenizer.from_pretrained("gpt2")
for _ in range(iterations):
    tokenizer_hf.encode(text)
hf_time = time.time() - start
hf_speed = (len(text) * iterations) / hf_time / 1000  # chars per ms

# Tiktoken
start = time.time()
enc_tiktoken = tiktoken.get_encoding("gpt2")
for _ in range(iterations):
    enc_tiktoken.encode(text)
tiktoken_time = time.time() - start
tiktoken_speed = (len(text) * iterations) / tiktoken_time / 1000

# HuggingFace Tokenizers (low-level)
start = time.time()
tokenizer_low = Tokenizer.from_pretrained("gpt2")
for _ in range(iterations):
    tokenizer_low.encode(text)
low_time = time.time() - start
low_speed = (len(text) * iterations) / low_time / 1000

# SentencePiece (via T5 tokenizer)
start = time.time()
tokenizer_sp = AutoTokenizer.from_pretrained("t5-base")
for _ in range(iterations):
    tokenizer_sp.encode(text)
sp_time = time.time() - start
sp_speed = (len(text) * iterations) / sp_time / 1000

print(f"{'Tool':<30} {'Time (s)':<12} {'Speed (K chars/ms)':<20} {'Relative Speed'}")
print("-" * 75)
print(f"{'HuggingFace Transformers':<30} {hf_time:<12.3f} {hf_speed:<20.2f} 1.0x")
print(f"{'Tiktoken':<30} {tiktoken_time:<12.3f} {tiktoken_speed:<20.2f} {hf_time/tiktoken_time:.2f}x")
print(f"{'HuggingFace Tokenizers (low)':<30} {low_time:<12.3f} {low_speed:<20.2f} {hf_time/low_time:.2f}x")
print(f"{'SentencePiece (T5)':<30} {sp_time:<12.3f} {sp_speed:<20.2f} {hf_time/sp_time:.2f}x")
```

Output:

```
Tool                           Time (s)     Speed (K chars/ms)   Relative Speed
---------------------------------------------------------------------------
HuggingFace Transformers       1.963        2699.53              1.0x
Tiktoken                       0.224        23652.91             8.76x
HuggingFace Tokenizers (low)   0.947        5594.55              2.07x
SentencePiece (T5)             2.828        1874.37              0.69x
```

Insights:

- Tiktoken is fastest - 8.76x faster than Transformers wrapper for GPT tokenization
- Low-level tokenizers is 2.07x faster - Than Transformers wrapper
- SentencePiece is slower - Due to Python bindings and more complex processing (0.69x relative speed)
- Transformers has overhead but provides model aware convenience (1.0x relative speed)

## Decision Guide

Choose HuggingFace Tokenizers if:

- Building custom tokenizers
- Need multiple algorithms (BPE/WordPiece/Unigram)
- Want maximum flexibility

Choose Tiktoken if:

- Working with GPT models (GPT-2/3/4)
- Need fastest tokenization
- Counting tokens for API costs

Choose SentencePiece if:

- Building multilingual models
- Need Unigram tokenization
- Working with T5/ALBERT/XLNet
- Want language agnostic tokenization

Choose Transformers AutoTokenizer if:

- Using HuggingFace Transformers models
- Want pre-trained tokenizers
- Prefer convenience over raw speed

# Conclusion

Tokenization is the under appreciated part of language models. It’s the foundation that everything else sits on, and as we have seen, the choices you make here have real consequences for cost, performance, and model quality.

This is just part 1 and I hope to cover a lot more in the future ones.

What we covered:

- Why tokenization matters - It affects API costs, training time, and model performance
- The three major algorithms - BPE (merge frequent pairs), WordPiece (merge likely pairs), and Unigram (prune unlikely tokens)
- How the numbers work - Token IDs map to embeddings that models actually process
- Real-world challenges - Why LLMs struggle with math, spelling, and multilingual text
- Practical comparisons - Tokenizer choice depends on your text; always test on your data
- Building your own - You can train custom tokenizers (BPE, WordPiece, or Unigram) tailored to your domain
- Which tool to use - Tiktoken for speed, tokenizers for flexibility, SentencePiece for multilingual

Key takeaways:

1. Subword tokenization is the standard - It balances vocabulary size with compression
2. Tokenization shapes model capabilities - Many LLM “failures” (arithmetic, spelling) trace back to tokenization
3. There’s no universal “best” tokenizer - Performance depends on your text, language, and use case
4. Non-English pays a token tax - Multilingual considerations matter for global applications
5. Domain-specific tokenizers help - If your domain has specialized vocabulary, train your own
6. Test on your actual data - Don’t assume; measure token counts and compare

If you found this interesting, I would love to hear your thoughts. Share it on 

[Twitter](https://twitter.com/manthanguptaa)

, 

[LinkedIn](https://www.linkedin.com/in/manthanguptaa/)

, or reach out at guptaamanthan01[at]gmail[dot]com.

## References

Books:

- Raschka, Sebastian. Build a Large Language Model (From Scratch). Manning Publications, 2024.

Blog Posts & Tutorials:

- [Tokenization in Transformers v5: Simpler, Clearer, and More Modular](https://huggingface.co/blog/tokenizers)

   - HuggingFace, December 2025
- [Subword Tokenization Algorithms](https://luminary.blog/techs/08-subword-tokenization-algorithms/)

   - Luminary Blog

Research Papers:

- Sennrich, Rico, Barry Haddow, and Alexandra Birch. 

  [Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/abs/1508.07909)

   - Original BPE paper (2016)
- Kudo, Taku and John Richardson. 

  [SentencePiece: A simple and language independent subword tokenizer](https://arxiv.org/abs/1808.06226)

   - SentencePiece/Unigram paper (2018)
- [Context-Dependent Tokenizer Performance Analysis](https://eurasip.org/Proceedings/Eusipco/Eusipco2024/pdfs/0000141.pdf)

   - EUSIPCO 2024
- [Tokenization Strategies for Large Language Models](https://arxiv.org/abs/2411.17669)

   - arXiv 2024

Documentation:

- [HuggingFace Tokenizers Library](https://huggingface.co/docs/tokenizers/)
- [Tiktoken Documentation](https://github.com/openai/tiktoken)
- [SentencePiece Documentation](https://github.com/google/sentencepiece)

---

## Images

![Image](https://pbs.twimg.com/media/G-1wTeKbMAALp9C?format=jpg&name=900x900)

![Image](https://pbs.twimg.com/media/G-1o9PbaEAA2A4w?format=png&name=small)

![Image](https://pbs.twimg.com/media/G-1swp2a8AAFyhU?format=png&name=small)

![Image](https://pbs.twimg.com/media/G-1s7GaaUAEoKYS?format=png&name=small)

![Image](https://pbs.twimg.com/media/G-1ufXTa0AENvoK?format=png&name=small)

