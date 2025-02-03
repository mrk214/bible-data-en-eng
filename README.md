# The Bible in JSON format

📖 This project is a **Bible** dataset in `JSON` format, obtained via web scraping, to spread the Word of God. The data has been restructured to make it easy to implement in any project.

👨‍💻 The **data** folder contains Bible translations, with each book of the Bible stored in a separate `JSON` file.

🙏 I hope to continue adding translations as long as possible. I also hope this is useful for those who need it and, most importantly, that it serves God's work among humanity.

## Data structure

Each `JSON` file corresponds to a book of the Bible and is typed as `Book` (in **TypeScript**).  
For better understanding, I’ve included the TypeScript types used to structure the data.

The key **types** here are `Book`, `Chapter`, and `ChapterItem`.

```typescript
export type Publisher = {
  name: string
}

export type Copyright = {
  html: string
  text: string
}

export type Language = {
  iso_639_1: string
  iso_639_3: string
  language_tag: string
  local_name: string
  text_direction: string
}

export type Current = {
  usfm: string[]
  human: string
}

export type NextPrev =
  | null
  | (Current & {
      canonical: boolean
      toc: boolean
    })

export type RedLetterWordsSection = {
  text: string
  rl: boolean
}

export type ChapterItemType = 'heading' | 'verse'

export type ChapterItem = {
  type: ChapterItemType
  verse_number: number
  lines: string[]
  rlw_lines: RedLetterWordsSection[][]
}

export type Chapter = {
  chapter_usfm: string
  current: Current
  next: NextPrev
  previous: NextPrev
  chapter_text: string
  chapter_html: string
  items: ChapterItem[]
}

export type Book = {
  book_usfm: string
  name: string
  local_title: string
  local_abbreviation: string
  version_id: number
  publisher: Publisher
  copyright: Copyright
  language: Language
  repository: string
  chapters: Chapter[]
}
```

## Explanation

The data is mostly self-explanatory, but here are a few clarifications:

👉 Each **book** (`Book`) contains **chapters** (`Chapter[]`), and each chapter contains **items** (`ChapterItem[]`).

👉 A `ChapterItem` can be either a **heading** (`heading`) or a **verse** (`verse`). If it’s a **heading**, `verse_number` will always be `-1`.

👉 Verses are split into lines, so `lines` is an array (`string[]`). Sometimes it has a single item, and other times multiple, depending on how the verse is structured.

👉 Each chapter (`Chapter`) also contains the full chapter text in plain format under `chapter_text`, with newline characters (`\n`).

👉 **rlw** stands for **red letter words**, meaning words attributed to Jesus. This is why the property is called `rlw_lines`.

👉 To save space, `rlw_lines` is usually an empty array since most Bible verses do not contain **red letter words**.

👉 Only when a verse contains **red letter words** will `rlw_lines` have content.

👉 For each item in the `lines` array, there is a corresponding item in `rlw_lines`.

👉 However, each `rlw_lines` item is also an array, because sometimes only part of a line is attributed to Jesus.

## Computed data

Some data is omitted for efficiency but can be easily derived. For example:

✅ To determine how many verses a chapter (`Chapter`) has, count the number of `ChapterItem` elements of type `verse`.

✅ To check if a verse (`ChapterItem`) has **red letter words**, simply verify if `rlw_lines` contains any items (`rlw_lines.length > 0`). Otherwise, just use `lines`.

## Raw files by translation

You can also access static files using the following links.

### NLT

| Name            | ID (usfm) | Link                                                                                   |
| --------------- | --------- | -------------------------------------------------------------------------------------- |
| Genesis         | `GEN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/GEN.json) |
| Exodus          | `EXO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EXO.json) |
| Leviticus       | `LEV`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/LEV.json) |
| Numbers         | `NUM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/NUM.json) |
| Deuteronomy     | `DEU`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/DEU.json) |
| Joshua          | `JOS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JOS.json) |
| Judges          | `JDG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JDG.json) |
| Ruth            | `RUT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/RUT.json) |
| 1 Samuel        | `1SA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1SA.json) |
| 2 Samuel        | `2SA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2SA.json) |
| 1 Kings         | `1KI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1KI.json) |
| 2 Kings         | `2KI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2KI.json) |
| 1 Chronicles    | `1CH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1CH.json) |
| 2 Chronicles    | `2CH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2CH.json) |
| Ezra            | `EZR`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EZR.json) |
| Nehemiah        | `NEH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/NEH.json) |
| Esther          | `EST`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EST.json) |
| Job             | `JOB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JOB.json) |
| Psalms          | `PSA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PSA.json) |
| Proverbs        | `PRO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PRO.json) |
| Ecclesiastes    | `ECC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ECC.json) |
| Song of Songs   | `SNG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/SNG.json) |
| Isaiah          | `ISA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ISA.json) |
| Jeremiah        | `JER`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JER.json) |
| Lamentations    | `LAM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/LAM.json) |
| Ezekiel         | `EZK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EZK.json) |
| Daniel          | `DAN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/DAN.json) |
| Hosea           | `HOS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HOS.json) |
| Joel            | `JOL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JOL.json) |
| Amos            | `AMO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/AMO.json) |
| Obadiah         | `OBA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/OBA.json) |
| Jonah           | `JON`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JON.json) |
| Micah           | `MIC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MIC.json) |
| Nahum           | `NAM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/NAM.json) |
| Habakkuk        | `HAB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HAB.json) |
| Zephaniah       | `ZEP`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ZEP.json) |
| Haggai          | `HAG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HAG.json) |
| Zechariah       | `ZEC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ZEC.json) |
| Malachi         | `MAL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MAL.json) |
| Matthew         | `MAT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MAT.json) |
| Mark            | `MRK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MRK.json) |
| Luke            | `LUK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/LUK.json) |
| John            | `JHN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JHN.json) |
| Acts            | `ACT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ACT.json) |
| Romans          | `ROM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ROM.json) |
| 1 Corinthians   | `1CO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1CO.json) |
| 2 Corinthians   | `2CO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2CO.json) |
| Galatians       | `GAL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/GAL.json) |
| Ephesians       | `EPH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EPH.json) |
| Philippians     | `PHP`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PHP.json) |
| Colossians      | `COL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/COL.json) |
| 1 Thessalonians | `1TH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1TH.json) |
| 2 Thessalonians | `2TH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2TH.json) |
| 1 Timothy       | `1TI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1TI.json) |
| 2 Timothy       | `2TI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2TI.json) |
| Titus           | `TIT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/TIT.json) |
| Philemon        | `PHM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PHM.json) |
| Hebrews         | `HEB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HEB.json) |
| James           | `JAS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JAS.json) |
| 1 Peter         | `1PE`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1PE.json) |
| 2 Peter         | `2PE`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2PE.json) |
| 1 John          | `1JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1JN.json) |
| 2 John          | `2JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2JN.json) |
| 3 John          | `3JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/3JN.json) |
| Jude            | `JUD`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JUD.json) |
| Revelation      | `REV`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/REV.json) |
