# NLP Bahasa Indonesia
![Python 3.6](https://img.shields.io/badge/python-3.6-3776AB.svg?style=flat-square&logo=python) [![spaCy](https://img.shields.io/badge/built%20with-spaCy-09a3d5.svg?style=flat-square&logo=python)](https://spacy.io) ![Python 3.6](https://img.shields.io/badge/spacy-v2.0.18-09a3d5.svg?style=flat-square&logo=python)   ![JSON](https://img.shields.io/badge/output-JSON-000000.svg?style=flat-square&logo=json) 

Pembuatan proyek ini guna membantu pengembangan NLP di Indonesia, khususnya penggunaan Bahasa Indonesia yang sementara ini masih sangat jarang.
Proyek ini saya kembangkan menggunakan bahasa Python dengan library Spacy. Pembuatan model menggunakan **Corpus** dari [Universal Dependencies](https://github.com/UniversalDependencies/UD_Indonesian-GSD) untuk mempermudah dan mempercepat pembuatan proyek ini. Serta mengikuti tutorial sederhana dari Spacy agar dapat secara langsung dilihat hasilnya.

### Langkah Awal
Download Corpus
- Corpus Training [id_gsd-ud-train.conllu](https://raw.githubusercontent.com/UniversalDependencies/UD_Indonesian-GSD/master/id_gsd-ud-train.conllu)
- Corpus Dev [id_gsd-ud-dev.conllu](https://raw.githubusercontent.com/UniversalDependencies/UD_Indonesian-GSD/master/id_gsd-ud-dev.conllu)
- Corpus Test [id_gsd-ud-test.conllu](https://raw.githubusercontent.com/UniversalDependencies/UD_Indonesian-GSD/master/id_gsd-ud-test.conllu)

Persiapkan beberapa `Directory` untuk menampung file yang diperlukan.
**corpus** untuk directory `file input`
**corpus_json** untuk directory `file output`
**model** untuk directory `model`
**package** untuk directory `package` hasil compile dari model
```bash
$ mkdir corpus
$ mkdir corpus_json
$ mkdir model
```
Contoh Script Download menggunakan `wget`
```bash
$ cd corpus
$ wget https://raw.githubusercontent.com/UniversalDependencies/UD_Indonesian-GSD/master/id_gsd-ud-train.conllu
$ wget https://raw.githubusercontent.com/UniversalDependencies/UD_Indonesian-GSD/master/id_gsd-ud-dev.conllu
$ wget https://raw.githubusercontent.com/UniversalDependencies/UD_Indonesian-GSD/master/id_gsd-ud-test.conllu
$ cd ..
```

### Install
Install `VirtualEnv` khusus untuk proyek ini.
```bash
$ virtualenv -p $(which python3.6) .env
$ source .env/bin/activate
$ (.env)
```
Install library
```bash
(.env) pip install -r requirements.txt
```

### Convert Corpus
Convert dari file conllu yang sudah kita download menjadi format JSON seperti yang disarankan oleh [Spacy](https://spacy.io/api/cli#convert)
```bash
$ (.env) python -m spacy convert corpus/id_gsd-ud-train.conllu corpus_json
    Generated output file corpus_json/id_gsd-ud-train.json
    Created 4477 documents
$ (.env) python -m spacy convert corpus/id_gsd-ud-dev.conllu corpus_json
    Generated output file corpus_json/id_gsd-ud-dev.json
    Created 559 documents
$ (.env) python -m spacy convert corpus/id_gsd-ud-test.conllu corpus_json
    Generated output file corpus_json/id_gsd-ud-test.json
    Created 557 documents
```
### Train
Proses training silakan dilihat di [Spacy](https://spacy.io/api/cli#train)
```bash
$ (.env) python -m spacy train id model corpus_json/id_gsd-ud-train.json corpus_json/id_gsd-ud-dev.json --n-iter 10

Itn.  Dep Loss  NER Loss  UAS     NER P.  NER R.  NER F.  Tag %   Token %  CPU WPS  GPU WPS
0     22441.825 0.000     80.374  0.000   0.000   0.000   90.771  100.000  4939.6   0.0 
1     952.093   0.000     81.562  0.000   0.000   0.000   91.984  100.000  5037.7   0.0
2     329.678   0.000     83.043  0.000   0.000   0.000   92.356  100.000  5042.8   0.0
3     188.739   0.000     83.246  0.000   0.000   0.000   92.547  100.000  5284.3   0.0 
.....
8     122.875   0.000     83.135  0.000   0.000   0.000   92.904  100.000  3876.8   0.0
9     115.220   0.000     82.605  0.000   0.000   0.000   92.856  100.000  4696.3   0.0
Saving model...
```
Karena corpus dari [Universal Dependencies](https://github.com/UniversalDependencies/UD_Indonesian-GSD) hanya berupa `POSTAG` saja, maka model yang sekarang kita punya hanya sekadar **POSTAG Bahasa Indonesia** saja.
### Package
Untuk membuat package, silakan dilihat di [Spacy](https://spacy.io/api/cli#package)
```bash
$ (.env) python -m spacy package model/model-final package --create-meta 

Generating meta.json
Enter the package settings for your model. The following information
will be read from your model data: pipeline, vectors.

Model language (default: id): 
Model name (default: unnamed): model
Model version (default: 0.0.0): 1.0.0 
Required spaCy version (default: >=2.0.18,<3.0.0): 
Model description: Model Bahasa Indonesia sederhana menggunakan corpus Universal Dependencies
Author: Kun Budiharta
Author email: kun@beritagar.id
Author website: https://beritagar.id
License: MIT

Successfully created package 'id_model-1.0.0'
package/id_model-1.0.0

To build the package, run `python setup.py sdist` in this directory.
```
Compile model menjadi package yang bisa di-install
```bash
$ (.env) cd package/id_model-1.0.0
$ (.env) python setup.py sdist
$ (.env) pip install dist/id_model-1.0.0.tar.gz
```

### Check Meta Data Model
```bash
Python 3.6.5 (default, Apr 25 2018, 14:23:58) 
[GCC 4.2.1 Compatible Apple LLVM 9.1.0 (clang-902.0.39.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import spacy
>>> import json
>>> nlp = spacy.load('id_model')
>>> print(json.dumps(nlp.meta, indent=4))
{
    "lang": "id",
    "name": "model",
    "version": "1.0.0",
    "spacy_version": ">=2.0.18,<3.0.0",
    "description": "Model Bahasa Indonesia sederhana menggunakan corpus Universal Dependencies",
    "author": "Kun Budiharta",
    "email": "kun@beritagar.id",
    "url": "https://beritagar.id",
    "license": "MIT",
    "vectors": {
        "width": 0,
        "vectors": 0,
        "keys": 0,
        "name": null
    },
    "pipeline": [
        "tagger",
        "parser",
        "ner"
    ]
}
>>> 
```

### Penggunaan
```bash
$ (.env) python
Python 3.6.5 (default, Apr 25 2018, 14:23:58) 
[GCC 4.2.1 Compatible Apple LLVM 9.1.0 (clang-902.0.39.1)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import spacy
>>> nlp = spacy.load('id_model')
>>> text = '''"Sekarang kalau ketinggalan dompet sudah enggak begitu pengaruh sih, uangnya di hape semua," ucap Mutia saat berbincang dengan Beritagar.id.'''
>>> doc = nlp(text)
>>> for token in doc:
...     print(token.text, token.lemma_, token.pos_, token.tag_, token.dep_,
...           token.shape_, token.is_alpha, token.is_stop)
... 
" " X Z-- punct " False False
Sekarang Sekarang X D-- advmod Xxxxx True False
kalau kalau X S-- mark xxxx True True
ketinggalan tinggal X NSD ROOT xxxx True False
dompet dompet X X-- compound xxxx True False
sudah sudah X VSA advmod xxxx True True
enggak enggak X G-- advmod xxxx True True
begitu begitu X B-- advmod xxxx True True
pengaruh pengaruh X NSD nmod xxxx True False
sih sih X X-- compound xxx True False
, , X Z-- punct , False False
uangnya uangnya X NSD+PS3 conj xxxx True False
di di X R-- case xx True True
hape hape X ASP nmod xxxx True False
semua semua X B-- det xxxx True True
, , X Z-- punct , False False
" " X Z-- punct " False False
ucap ucap X NSD conj xxxx True True
Mutia Mutia X NSD flat Xxxxx True False
saat saat X NSD mark xxxx True True
berbincang bincang X VSA advcl xxxx True False
dengan dengan X R-- case xxxx True True
Beritagar.id Beritagar.id X X-- obl Xxxxx.xx False False
. . X Z-- punct . False False
>>> 
```
### Todos

- [ ] Named-entity recognition (NER) 

License
----
MIT


