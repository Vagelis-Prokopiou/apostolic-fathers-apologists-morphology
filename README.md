# Apostolic Fathers and Apologists — lemmas and morphology

Word-level lemmas and morphological tags for the **Apostolic Fathers** (Lake's text) and the
**second-century Greek Apologists** (Justin, Tatian, Athenagoras, Theophilus), derived from the
[GLAUx](https://github.com/alekkeersmaekers/glaux) treebank and attached to openly licensed reading texts.

Neither corpus has an openly licensed, hand-made morphological edition. GLAUx tags most of these works,
but on its own copy of the texts, divided and numbered in its own way. This dataset puts a lemma and a
tag on every word of the reading texts people cite: Lake's Apostolic Fathers as published by the
[Open Apostolic Fathers](https://github.com/jtauber/apostolic-fathers), and the
[First1KGreek](https://github.com/OpenGreekAndLatin/First1KGreek) editions of the Apologists. Each word
then comes with its book, chapter and verse. It gets there in one of two ways:

- **Transferred** (Apostolic Fathers, Tatian, Athenagoras, Theophilus): GLAUx's own analysis of the same
  word, carried across.
- **Predicted** (Justin Martyr, whom GLAUx does not contain): a model trained on GLAUx's analysis of
  related Greek proposes each word's analysis. `books.tsv` says which method each book uses.

**This is machine-made data.** GLAUx is itself automatically annotated. The transfer adds a second
automatic step, and the prediction adds a model. Treat a tag as a strong default, not a hand-checked
reading.

## Files

| File | Rows | Content |
|---|---|---|
| `data/apostolic-fathers.tsv` | 64,873 words | 1–2 Clement, Ignatius (7 letters), Polycarp, Didache, Barnabas, Shepherd, Martyrdom of Polycarp, Diognetus |
| `data/apologists.tsv` | 121,690 words | Justin (1–2 Apology, Dialogue), Tatian, Athenagoras (Embassy, Resurrection), Theophilus (To Autolycus I–III) |
| `books.tsv` | 24 | the key to the `book` column: name, file, **method** (`transferred` / `predicted`), per-book coverage |

Both data files are UTF-8, tab-separated, one row per word in reading order, with a header row:

| Column | Meaning |
|---|---|
| `book` | book number, 88–102 (Apostolic Fathers) or 103–111 (Apologists); names in `books.tsv` |
| `chapter` | chapter; **0** is a prescript or opening address |
| `verse` | verse (see *Reference scheme*) |
| `surface` | the word as printed in the reading text, punctuation stripped, NFC |
| `lemma` | the lemma, NFC-normalised (Koine headwords, matching MorphGNT); empty if the word was not tagged |
| `postag` | the AGDT/Perseus 9-slot tag (part of speech, person, number, tense, mood, voice, gender, case, degree), e.g. `v3saia---`; empty if not tagged |

A few rows carry a tag without a lemma: GLAUx gives some words no headword (`_`) or marks a
letter-numeral (`κε#` = 25). Their morphology is kept, but a non-word is never offered as a lemma.

## Coverage

| | Method | Words | With lemma + tag |
|---|---|---|---|
| Apostolic Fathers | transferred | 64,873 | 62,862 (**96.9%**) |
| Tatian | transferred | 10,227 | 99.3% |
| Athenagoras, Embassy / Resurrection | transferred | 11,294 / 8,818 | 99.0% / 98.1% |
| Theophilus, To Autolycus I / II / III | transferred | 3,113 / 11,538 / 6,949 | 97.2% / 95.8% / 92.7% |
| Justin, 1 Apology / 2 Apology / Dialogue | **predicted** | 14,437 / 3,293 / 52,021 | 95.4% / 94.3% / 96.3% (**96.0%** overall) |

Most of the Apostolic Fathers' residue is Polycarp, *Philippians* 10–12, which survives only in Latin
(70.4% for the letter). Justin's untagged words are those the model never met in training (see below).
Per-book figures are in `books.tsv`.

## Method

### Transferred books

For each work, GLAUx's tokens and the reading text's tokens are aligned with a Myers diff over
diacritic-insensitive forms (combining marks, elision apostrophes and final sigma folded). Each matched
pair passes GLAUx's lemma and tag to the reading-text word. Words the diff leaves unmatched — textual
differences between the editions, and Latin — keep an empty lemma and tag rather than a guess. Ignatius'
seven letters share one GLAUx file and are split by its letter divisions; Theophilus' three books are
split the same way.

### Predicted books (Justin Martyr)

- **The model.** A lexicon of every analysis GLAUx gives each written form, looked up by the exact form
  and then without diacritics. An averaged perceptron, decoded left to right, picks among a form's
  analyses using the word, its affixes, its neighbours and the two tags before it.
- **What it was trained on.** 1.9M words of GLAUx annotation: the other Apologists, the Apostolic Fathers,
  and 109 further GLAUx texts (Septuagint, Philo, Josephus, Epictetus, Marcus Aurelius, Clement of
  Alexandria, Origen, Hippolytus, Methodius), pinned to GLAUx commit `b077d8f`. Only texts GLAUx lists as
  CC BY-SA were used. GLAUx's New Testament and Josephus' *Jewish War* were left out, because their
  manual annotation comes from non-commercial treebanks.
- **What it ships.** Only words seen in training, and only when the best analysis clearly beats the
  runner-up. Words never seen in training are left untagged: the model is right on only about two-thirds
  of them, and its confidence cannot tell which.
- **How good it is.** Measured by holding out an author GLAUx does cover, predicting his text, and
  comparing with GLAUx's analysis of the same words:

  | Held out | Coverage | Full tag | Lemma |
  |---|---|---|---|
  | Tatian | 91.7% | 93.98% | 98.70% |
  | Athenagoras | 94.2% | 93.37% | 98.88% |

  Person, number, tense, mood and voice are each above 99.5%. Almost all disagreement is gender and case,
  on forms whose ending is itself ambiguous. Weighted to Justin's mix of words, the expected agreement is
  about **93% full tag and 98.7% lemma**. These figures measure agreement with GLAUx, which is itself
  machine-annotated (it reports 97.2% for morphology and 98.8% for lemmas), not with a hand-checked
  edition.

## Reference scheme

- **Apostolic Fathers:** chapter and verse as in Lake. The Shepherd's (division, chapter) pairs are
  flattened, in file order, to the modern continuous chapters 1–114. The Martyrdom's epilogue is
  chapter 23.
- **Apologists:** the First1KGreek editions divide the text differently, so `verse` means:
  - Justin's Apologies and Athenagoras: the paragraph's position within the chapter.
  - Justin's Dialogue: the section.
  - Tatian: the segment's position within the chapter. The edition's own `n` is Otto's continuous
    numbering, which runs across chapter breaks.
  - Theophilus: each book's sections are its chapters.
- Justin's inline apparatus (folio markers, bracketed scripture references) is removed. Genuine
  editorial supplements are kept.

## Licence and attribution

**CC BY-SA 4.0** (see `LICENSE`). Every source used is ShareAlike, so this derivative is too:

- **Annotation:** GLAUx — Alek Keersmaekers et al., KU Leuven, <https://github.com/alekkeersmaekers/glaux>.
  GLAUx licenses its data per source text, in its `metadata.txt`; every text used here, for transfer or
  for training, is listed there as CC BY-SA (3.0 or 4.0). CC BY-SA 3.0 adaptations may be shared under 4.0.
- **Apostolic Fathers text:** Kirsopp Lake's edition, via the Open Apostolic Fathers — James K. Tauber,
  <https://github.com/jtauber/apostolic-fathers>, CC BY-SA 4.0.
- **Apologists text:** First1KGreek — Open Greek and Latin,
  <https://github.com/OpenGreekAndLatin/First1KGreek>, CC BY-SA 4.0.

If you use the data, credit GLAUx and the text sources as above, and this dataset as:
*Vagelis Prokopiou, Apostolic Fathers and Apologists — lemmas and morphology (2026), CC BY-SA 4.0.*

## Versions

- **v2 (2026-10-07):** Justin Martyr's three works are tagged by prediction (66,983 of 69,751 words);
  `books.tsv` gains a `method` column. All other rows are unchanged.
- **v1 (2026-10-07):** Apostolic Fathers, Tatian, Athenagoras and Theophilus, transferred from GLAUx;
  Justin untagged.

## Where it comes from

This data was produced for [Koine Advanced Indexer](https://play.google.com/store/apps/details?id=eu.esxata.koine),
an offline Android reader and search engine for the Septuagint, the Greek New Testament, the Apostolic
Fathers and the Apologists, which uses it for lemma and grammar search. The app is a paid product; this
dataset is free under the licence above. Corrections are welcome as issues.
