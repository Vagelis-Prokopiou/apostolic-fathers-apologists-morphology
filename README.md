# Apostolic Fathers and Apologists — lemmas and morphology

Word-level lemmas and morphological tags for the **Apostolic Fathers** (Lake's text) and the
**second-century Greek Apologists** (Justin, Tatian, Athenagoras, Theophilus), produced by
transferring the annotation of the [GLAUx](https://github.com/alekkeersmaekers/glaux) treebank onto
openly licensed reading texts.

Neither corpus has an openly licensed, hand-made morphological edition. GLAUx tags these works, but on
its own copy of the texts, divided and numbered in its own way. This dataset carries GLAUx's lemma and
tag over to every word of the reading texts people cite: Lake's Apostolic Fathers as published by the
[Open Apostolic Fathers](https://github.com/jtauber/apostolic-fathers), and the
[First1KGreek](https://github.com/OpenGreekAndLatin/First1KGreek) editions of the Apologists. Each word
then comes with its book, chapter and verse.

**This is machine-tagged data.** GLAUx is itself automatically annotated, and the transfer adds a
second automatic step. Treat a tag as a strong default, not a hand-checked reading.

## Files

| File | Rows | Content |
|---|---|---|
| `data/apostolic-fathers.tsv` | 64,873 words | 1–2 Clement, Ignatius (7 letters), Polycarp, Didache, Barnabas, Shepherd, Martyrdom of Polycarp, Diognetus |
| `data/apologists.tsv` | 121,690 words | Justin (1–2 Apology, Dialogue), Tatian, Athenagoras (Embassy, Resurrection), Theophilus (To Autolycus I–III) |
| `books.tsv` | 24 | the key to the `book` column, with per-book coverage |

Both data files are UTF-8, tab-separated, one row per word in reading order, with a header row:

| Column | Meaning |
|---|---|
| `book` | book number, 88–102 (Apostolic Fathers) or 103–111 (Apologists); names in `books.tsv` |
| `chapter` | chapter; **0** is a prescript or opening address |
| `verse` | verse (see *Reference scheme*) |
| `surface` | the word as printed in the reading text, punctuation stripped, NFC |
| `lemma` | GLAUx's lemma, NFC-normalised (Koine headwords, matching MorphGNT); empty if the word was not matched |
| `postag` | the AGDT/Perseus 9-slot tag (part of speech, person, number, tense, mood, voice, gender, case, degree), e.g. `v3saia---`; empty if not matched |

Two rows carry a tag without a lemma: GLAUx gives some words no headword (`_`) or marks a
letter-numeral (`κε#` = 25). Their morphology transfers, but a non-word is never offered as a lemma.

## Coverage

| | Words | With lemma + tag |
|---|---|---|
| Apostolic Fathers | 64,873 | 62,862 (**96.9%**) |
| Tatian | 10,227 | 99.3% |
| Athenagoras, Embassy / Resurrection | 11,294 / 8,818 | 99.0% / 98.1% |
| Theophilus, To Autolycus I / II / III | 3,113 / 11,538 / 6,949 | 97.2% / 95.8% / 92.7% |
| Justin (1–2 Apology, Dialogue) | 69,751 | **0%** — GLAUx does not include Justin |

Justin's rows are included anyway, with empty lemma and tag, so the text and its reference scheme are
complete. Most of the Apostolic Fathers' residue is Polycarp, *Philippians* 10–12, which survives only
in Latin (70.4% for the letter). Per-book figures are in `books.tsv`.

## Method

For each work, GLAUx's tokens and the reading text's tokens are aligned with a Myers diff over
diacritic-insensitive forms (combining marks, elision apostrophes and final sigma folded). Each matched
pair passes GLAUx's lemma and tag to the reading-text word. Words the diff leaves unmatched — textual
differences between the editions, Latin, and Justin — keep an empty lemma and tag rather than a guess.
Ignatius' seven letters share one GLAUx file and are split by its letter divisions. Theophilus' three
books are split the same way.

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

**CC BY-SA 4.0** (see `LICENSE`). Every source is ShareAlike, so this derivative is too:

- **Annotation:** GLAUx — Alek Keersmaekers et al., KU Leuven,
  <https://github.com/alekkeersmaekers/glaux>. CC BY-SA 4.0. Theophilus is CC BY-SA 3.0, whose
  adaptations may be shared under 4.0.
- **Apostolic Fathers text:** Kirsopp Lake's edition, via the Open Apostolic Fathers — James K. Tauber,
  <https://github.com/jtauber/apostolic-fathers>, CC BY-SA 4.0.
- **Apologists text:** First1KGreek — Open Greek and Latin,
  <https://github.com/OpenGreekAndLatin/First1KGreek>, CC BY-SA 4.0.

If you use the data, credit GLAUx and the text sources as above, and this dataset as:
*Vagelis Prokopiou, Apostolic Fathers and Apologists — lemmas and morphology (2026), CC BY-SA 4.0.*

## Where it comes from

This data was produced for [Koine Advanced Indexer](https://play.google.com/store/apps/details?id=eu.esxata.koine),
an offline Android reader and search engine for the Septuagint, the Greek New Testament, the Apostolic
Fathers and the Apologists, which uses it for lemma and grammar search. The app is a paid product; this
dataset is free under the licence above. Corrections are welcome as issues.
