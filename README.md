# sentence_embedding
A sentence embedding method based on entropy-weighted series. The entropy weights are estimated by using TFIDF transform.

# Usage

You can convert your word embeddings binary to a much more memory friendly format that uses the OS index instead of loading 
the whole embeddings into memory. This is useful for accelerating several tests with sentence embeddings or when your memory
is limited.

```python
import wisse
from gensim.models.keyedvectors import KeyedVectors as vDB
load_vectors = vDB.load_word2vec_format

embedding = load_vectors("/path/to/the/embeddings.bin", binary = True)

wisse.keyed2indexed(embedding, "/path/for/saving/the/embeddings")
embedding = wisse.vector_space("/path/for/saving/the/embeddings")

# Print a word representation:

embedding["word"]
# array([ 0.31054 ,  0.56376 ,  0.024153,  0.33126 , -0.079045,  1.0207  ,
#        0.10248 ,  0.90402 ,  0.27232 ,  0.81331 ,  0.23089 ,  0.64988 ,
#       -0.16569 ,  1.3422  , -0.33309 ,  0.58445 ,  1.0079  ,  0.42946 ,
#        0.79254 ,  0.10515 ], dtype=float32)

```

Either you have been converted the embeddings to abovementioned new format or not the use of wisse is simple:

```python
# Loading a pretrained sklearn IDF model saved with pickle

with open("/path/to/the/idf/model", 'rb') as f:
            idf_model = pickle.load(f)

# Fit the wisse model:
series = wisse.wisse(embedding, idf_model, tf_tfidf = True)

# Print a sentence representation:
series.transform("this is a separable inclusion sentence")
# ([],
# ['this', 'is', 'sentence'],
# array([-1.58484   , -1.23394   ,  1.2498801 ,  1.04618   , -0.33089   ,
#         2.3042998 , -0.64029104, -0.61197007, -0.28983003, -0.37576   ,
#         0.269243  ,  2.41799   , -0.9898    , -0.34486997,  0.2799    ,
#         0.09885   , -0.22021998,  1.1462    ,  1.3328199 , -3.2394    ],
#       dtype=float32))
#
# It gives you two lists containing missing words in the vocabulary of the model

# You can fit a sentence representation generator from a list of sentences:

sents = series.fit_transform(["this is a separable inclusion", "trade regarding cause"])
# <wisse.wisse at 0x7fa479bffc10>

[s for s in sents]
#[([],
#  ['this', 'is'],
#  array([-1.58484   , -1.23394   ,  1.2498801 ,  1.04618   , -0.33089   ,
#          2.3042998 , -0.64029104, -0.61197007, -0.28983003, -0.37576   ,
#          0.269243  ,  2.41799   , -0.9898    , -0.34486997,  0.2799    ,
#          0.09885   , -0.22021998,  1.1462    ,  1.3328199 , -3.2394    ],
#        dtype=float32)),
# ([],
#  [],
#  array([ 0.44693702, -1.7455599 ,  0.63352   , -0.07516798,  0.14190999,
#          2.23659   , -0.56567   , -0.10897   , -1.011733  ,  1.17031   ,
#         -1.85232   ,  3.3530402 , -1.6981599 ,  1.80976   , -0.533846  ,
#          0.98503006,  0.9467    ,  1.15892   ,  0.79427004, -0.97222   ],
#        dtype=float32))]

``` 
An example of use can be feed with a file containing a sentence by line:

```bash
$ python wisse_example.py --input /path/to/sentences.txt --idfmodel local --embedmodel /path/to/pretrained_idf.pk --localw binary --output test.vec
```

# Paper for citing
```bibtex
@article{arroyo2017unsupervised,
  title={Unsupervised Sentence Representations as Word Information Series: Revisiting TF--IDF},
  author={Arroyo-Fern{\'a}ndez, Ignacio and M{\'e}ndez-Cruz, Carlos-Francisco and Sierra, Gerardo and Torres-Moreno, Juan-Manuel and Sidorov, Grigori},
  journal={arXiv preprint arXiv:1710.06524},
  year={2017}
}
```
