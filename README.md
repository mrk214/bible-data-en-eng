# The Bible in JSON format

📖 This project is a **Bible** dataset in `JSON` format (encoding `utf8`), obtained via web scraping, to spread the Word of God. The data has been restructured to make it easy to implement in any project.

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

// For clarity, I have assigned a 'weight' to each type.
// Depending on the translation version, some types may appear more or less
// frequently. However, the essential types are: 'heading1' and 'verse'.
// I recommend using and styling all options.
// The 'weight' can be used as a reference for styling the text font.
export type ChapterItemType =
  | 'section1' // very rare   - can be ignored        - weight: 900
  | 'section2' // very rare   - can be ignored        - weight: 800
  | 'heading1' // very common - should not be ignored - weight: 700
  | 'heading2' // common      - can be ignored        - weight: 600
  | 'label' //    rare        - can be ignored        - weight: 500
  | 'verse' //    very common - should not be ignored - weight: 400

export type ChapterItem = {
  type: ChapterItemType
  verse_number: number
  lines: string[]
  rlw_lines: RedLetterWordsSection[][]
}

export type Chapter = {
  chapter_usfm: string
  is_chapter: boolean
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

👉 In some versions, some books have an introduction. To verify that a **chapter** (`Chapter`) is actually a chapter and not an introduction, you can use the `is_chapter` parameter.

👉 If a `Chapter` is actually an introduction, then its `items` property will be an empty array. If you want to use the content, it will be available in `chapter_text` and `chapter_html`.

👉 A `ChapterItem` will almost always be of type **verse** (`verse`) or **heading** (`heading1`). However, there are several other types that can be styled accordingly: `section1`, `section2`, `heading1`, `heading2`, `label`, `verse`.

👉 If a `ChapterItem` is NOT of type **verse** (`verse`), then its `verse_number` property will always be `-1`.

👉 Verses are split into lines, so `lines` is an array (`string[]`). Sometimes it has a single item, and other times multiple, depending on how the verse is structured.

👉 Some verses may contain a title (or another element) in the middle. In such cases, the verse continues after the element. Therefore, the `verse_number` may be repeated.

```json
{
  "type": "verse",
  "verse_number": 3,
  "lines": [
    "Pleasing is the fragrance of your perfumes;",
    "your name is like perfume poured out.",
    "No wonder the young women love you!"
  ],
  "rlw_lines": []
},
{
  "type": "verse",
  "verse_number": 4, // 👈 you should display only this first 'verse_number'
  "lines": [
    "Take me away with you—let us hurry!",
    "Let the king bring me into his chambers."
  ],
  "rlw_lines": []
},
{
  "type": "heading1",
  "verse_number": -1,
  "lines": ["Friends"],
  "rlw_lines": []
},
{
  "type": "verse",
  "verse_number": 4, // 👈 and NOT display this repeated one
  "lines": [
    "We rejoice and delight in you;",
    "we will praise your love more than wine."
  ],
  "rlw_lines": []
}
```

👉 Because of this, you should implement a method to display the `verse_number` only the first time (if that is your goal). For example:

```typescript
let lastPrintedNumber: number = -1
// Condition to print 'verse_number' only once
if (
  chapterItem.verse_number !== -1 &&
  chapterItem.verse_number !== lastPrintedNumber
) {
  lastPrintedNumber = chapterItem.verse_number
  print(chapterItem.verse_number)
}
```

👉 Each chapter (`Chapter`) also contains the full chapter text in plain format under `chapter_text`, with newline characters (`\n`); and the original html in `chapter_html`.

👉 **rlw** stands for **red letter words**, meaning words attributed to Jesus. This is why the property is called `rlw_lines`.

👉 To save space, `rlw_lines` is usually an empty array since most Bible verses do not contain **red letter words**.

👉 Only when a verse contains **red letter words** will `rlw_lines` have content.

👉 For each item in the `lines` array, there is a corresponding item in `rlw_lines`.

👉 However, each `rlw_lines` item is also an array, because sometimes only part of a line is attributed to Jesus.

## Computed data

Some data is omitted for efficiency but can be easily derived. For example:

✅ To know how many verses are in a chapter (`Chapter`), you can search for the highest `verse_number` in the `items: ChapterItem[]` array.

✅ To check if a verse (`ChapterItem`) has **red letter words**, simply verify if `rlw_lines` contains any items (`rlw_lines.length > 0`). Otherwise, just use `lines`.

## Raw files by translation

You can also access static files using the following links:

### New International Version (NIV)

| Name                 | ID (usfm) | Link                                                                                   |
| -------------------- | --------- | -------------------------------------------------------------------------------------- |
| Genesis              | `GEN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/GEN.json) |
| Exodus               | `EXO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/EXO.json) |
| Leviticus            | `LEV`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/LEV.json) |
| Numbers              | `NUM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/NUM.json) |
| Deuteronomy          | `DEU`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/DEU.json) |
| Joshua               | `JOS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JOS.json) |
| Judges               | `JDG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JDG.json) |
| Ruth                 | `RUT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/RUT.json) |
| 1 Samuel             | `1SA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1SA.json) |
| 2 Samuel             | `2SA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2SA.json) |
| 1 Kings              | `1KI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1KI.json) |
| 2 Kings              | `2KI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2KI.json) |
| 1 Chronicles         | `1CH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1CH.json) |
| 2 Chronicles         | `2CH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2CH.json) |
| Ezra                 | `EZR`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/EZR.json) |
| Nehemiah             | `NEH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/NEH.json) |
| Esther               | `EST`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/EST.json) |
| Job                  | `JOB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JOB.json) |
| Psalms               | `PSA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/PSA.json) |
| Proverbs             | `PRO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/PRO.json) |
| Ecclesiastes         | `ECC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/ECC.json) |
| Song of Songs        | `SNG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/SNG.json) |
| Isaiah               | `ISA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/ISA.json) |
| Jeremiah             | `JER`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JER.json) |
| Lamentations         | `LAM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/LAM.json) |
| Ezekiel              | `EZK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/EZK.json) |
| Daniel               | `DAN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/DAN.json) |
| Hosea                | `HOS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/HOS.json) |
| Joel                 | `JOL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JOL.json) |
| Amos                 | `AMO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/AMO.json) |
| Obadiah              | `OBA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/OBA.json) |
| Jonah                | `JON`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JON.json) |
| Micah                | `MIC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/MIC.json) |
| Nahum                | `NAM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/NAM.json) |
| Habakkuk             | `HAB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/HAB.json) |
| Zephaniah            | `ZEP`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/ZEP.json) |
| Haggai               | `HAG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/HAG.json) |
| Zechariah            | `ZEC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/ZEC.json) |
| Malachi              | `MAL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/MAL.json) |
| Matthew              | `MAT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/MAT.json) |
| Mark                 | `MRK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/MRK.json) |
| Luke                 | `LUK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/LUK.json) |
| John                 | `JHN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JHN.json) |
| Acts of the Apostles | `ACT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/ACT.json) |
| Romans               | `ROM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/ROM.json) |
| 1 Corinthians        | `1CO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1CO.json) |
| 2 Corinthians        | `2CO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2CO.json) |
| Galatians            | `GAL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/GAL.json) |
| Ephesians            | `EPH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/EPH.json) |
| Philippians          | `PHP`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/PHP.json) |
| Colossians           | `COL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/COL.json) |
| 1 Thessalonians      | `1TH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1TH.json) |
| 2 Thessalonians      | `2TH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2TH.json) |
| 1 Timothy            | `1TI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1TI.json) |
| 2 Timothy            | `2TI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2TI.json) |
| Titus                | `TIT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/TIT.json) |
| Philemon             | `PHM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/PHM.json) |
| Hebrews              | `HEB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/HEB.json) |
| James                | `JAS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JAS.json) |
| 1 Peter              | `1PE`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1PE.json) |
| 2 Peter              | `2PE`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2PE.json) |
| 1 John               | `1JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/1JN.json) |
| 2 John               | `2JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/2JN.json) |
| 3 John               | `3JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/3JN.json) |
| Jude                 | `JUD`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/JUD.json) |
| Revelation           | `REV`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NIV/REV.json) |

### New Living Translation (NLT)

| Name                 | ID (usfm) | Link                                                                                   |
| -------------------- | --------- | -------------------------------------------------------------------------------------- |
| Genesis              | `GEN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/GEN.json) |
| Exodus               | `EXO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EXO.json) |
| Leviticus            | `LEV`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/LEV.json) |
| Numbers              | `NUM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/NUM.json) |
| Deuteronomy          | `DEU`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/DEU.json) |
| Joshua               | `JOS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JOS.json) |
| Judges               | `JDG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JDG.json) |
| Ruth                 | `RUT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/RUT.json) |
| 1 Samuel             | `1SA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1SA.json) |
| 2 Samuel             | `2SA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2SA.json) |
| 1 Kings              | `1KI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1KI.json) |
| 2 Kings              | `2KI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2KI.json) |
| 1 Chronicles         | `1CH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1CH.json) |
| 2 Chronicles         | `2CH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2CH.json) |
| Ezra                 | `EZR`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EZR.json) |
| Nehemiah             | `NEH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/NEH.json) |
| Esther               | `EST`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EST.json) |
| Job                  | `JOB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JOB.json) |
| Psalms               | `PSA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PSA.json) |
| Proverbs             | `PRO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PRO.json) |
| Ecclesiastes         | `ECC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ECC.json) |
| Song of Songs        | `SNG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/SNG.json) |
| Isaiah               | `ISA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ISA.json) |
| Jeremiah             | `JER`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JER.json) |
| Lamentations         | `LAM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/LAM.json) |
| Ezekiel              | `EZK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EZK.json) |
| Daniel               | `DAN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/DAN.json) |
| Hosea                | `HOS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HOS.json) |
| Joel                 | `JOL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JOL.json) |
| Amos                 | `AMO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/AMO.json) |
| Obadiah              | `OBA`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/OBA.json) |
| Jonah                | `JON`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JON.json) |
| Micah                | `MIC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MIC.json) |
| Nahum                | `NAM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/NAM.json) |
| Habakkuk             | `HAB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HAB.json) |
| Zephaniah            | `ZEP`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ZEP.json) |
| Haggai               | `HAG`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HAG.json) |
| Zechariah            | `ZEC`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ZEC.json) |
| Malachi              | `MAL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MAL.json) |
| Matthew              | `MAT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MAT.json) |
| Mark                 | `MRK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/MRK.json) |
| Luke                 | `LUK`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/LUK.json) |
| John                 | `JHN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JHN.json) |
| Acts of the Apostles | `ACT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ACT.json) |
| Romans               | `ROM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/ROM.json) |
| 1 Corinthians        | `1CO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1CO.json) |
| 2 Corinthians        | `2CO`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2CO.json) |
| Galatians            | `GAL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/GAL.json) |
| Ephesians            | `EPH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/EPH.json) |
| Philippians          | `PHP`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PHP.json) |
| Colossians           | `COL`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/COL.json) |
| 1 Thessalonians      | `1TH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1TH.json) |
| 2 Thessalonians      | `2TH`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2TH.json) |
| 1 Timothy            | `1TI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1TI.json) |
| 2 Timothy            | `2TI`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2TI.json) |
| Titus                | `TIT`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/TIT.json) |
| Philemon             | `PHM`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/PHM.json) |
| Hebrews              | `HEB`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/HEB.json) |
| James                | `JAS`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JAS.json) |
| 1 Peter              | `1PE`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1PE.json) |
| 2 Peter              | `2PE`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2PE.json) |
| 1 John               | `1JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/1JN.json) |
| 2 John               | `2JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/2JN.json) |
| 3 John               | `3JN`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/3JN.json) |
| Jude                 | `JUD`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/JUD.json) |
| Revelation           | `REV`     | [raw file](https://jsckdm.github.io/bible-data-en-eng/data/en___en___eng/NLT/REV.json) |
