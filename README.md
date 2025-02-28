# The Bible in JSON Format

📖 This project is a data set of the **Bible** in `JSON` format
(encoding `utf8`), obtained through web scraping. The data is structured in a
standard and consistent way, with the intention that it be easy to implement
any of the available versions (_translations_) here.

👨‍💻 In the **data** folder you will find the different versions, divided into
`JSON` files for each book of the Bible.

🙏 I hope to continue adding translations as long as possible, I hope it is
useful for someone who needs it; and, above all, I hope that it serves the
work of God.

## Data Structure

Each `JSON` file corresponds to a book of the Bible, and was typed as `Book`
(with **TypeScript**).
That is why I include the TypeScript types with which the data were structured,
so that its functioning is understood.

The important types here are `Book`, `Chapter`, `ChapterItem`, and
`ChapterItemType`.

```typescript
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

export type Chapter = {
  chapter_usfm: string
  is_chapter: boolean
  current: Current
  next: NextPrev | null
  previous: NextPrev | null
  chapter_text: string
  chapter_html: string
  items: ChapterItem[]
}

export type ChapterItem = {
  type: ChapterItemType
  verse_numbers: number[]
  lines: string[]
  rlw_lines: RedLetterWordsSection[][]
}

// Depending on the version, some ChapterItemTypes may appear more or less.
// The essential ChapterItemTypes are: 'heading1' and 'verse'.
// I have added comments that can be used as a reference for styles. 👇👇👇
// (This is only a reference; you can apply any styles you want.)
export type ChapterItemType =
  | 'section1' // rare        - weight: 900 - h1
  | 'section2' // rare        - weight: 800 - h2
  | 'heading1' // very common - weight: 700 - h3
  | 'heading2' // common      - weight: 600 - h4
  | 'label' //    common      - weight: 500 - italic
  | 'verse' //    very common - weight: 400 - regular text
```

Other types.

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

export type NextPrev = Current & {
  canonical: boolean
  toc: boolean
}

export type RedLetterWordsSection = {
  text: string
  rl: boolean
}
```

## Explanation

The data are mostly self-explanatory, but I leave some clarifications since,
depending on the version, some data may vary (_but not the structure, the
structure is the same for all versions_):

👉 Each **book** (`Book`) contains **chapters** (`Chapter[]`), and each chapter
contains **items** (`ChapterItem[]`).

👉 In some versions, some books have an introduction.
To verify that a **chapter** (`Chapter`) is truly a chapter and not an
introduction, you can use the `is_chapter` property.

👉 If a `Chapter` is an introduction, then its `items` property will be an
empty array. If you wish to use the content, it will be available in the
`chapter_text` and `chapter_html` properties.

👉 A `ChapterItem` will almost always be of type **verse** (`verse`) or
**heading1** (`heading1`). However, there are several other types: `section1`,
`section2`, `heading1`, `heading2`, `label`, `verse`.

👉 If a `ChapterItem` is **not of type verse** (`verse`), then its
`verse_numbers` property will always be an empty array `[]`.

👉 The reason why `verse_numbers` is an array is because some versions group
several verses into a single paragraph of text, without being able to know
where each verse begins or ends: `"verse_numbers":[5,6,7]`.

👉 But the most common scenario is that `verse_numbers` contains only one item:
`"verse_numbers":[7]`.

👉 The verses (`ChapterItem` _of type_ `verse`) are divided into lines, so
`lines` is an array (`string[]`).
Sometimes it has only one element, and other times several, depending on how
the verse is structured (_this was not decided by me, but rather as it comes
from the data source, and it can change from one chapter to another_).

👉 On the other hand, in `ChapterItem`s of a type **different from** `verse`,
such as `heading1`, its `lines` property will always be an array with a single
element: `"lines":["Hello world"]`.

👉 Sometimes you can find a title (_or another element_) in the middle of a
verse. In these cases, the verse continues after the element. Therefore, the
same `verse_numbers` may be repeated in more than one `ChapterItem`.

- This should be taken into account when searching for a verse, for example,
  since **although it is not common**, you may find several `ChapterItem`s with
  the same array in `verse_numbers`.

- Also, because generally, you only want to display the verse number the first
  time, at the beginning of the verse.

👉 Each chapter (`Chapter`) also contains the complete text of the chapter in
plain format in `chapter_text`, with newline characters (`\n`), and the
original HTML in `chapter_html`.

👉 **rlw** means **red letter words**, that is, the words attributed to Jesus.
For this reason, `ChapterItem` has a property called `rlw_lines`.

👉 To save space, `rlw_lines` will almost always be an empty array, since most
verses of the Bible do not contain **red letter words** (_and in not all
versions are they marked_).

👉 The `rlw_lines` array will only have items when the verse contains **red
letter words**. You can use this data as a validation.

👉 You should also know that if the verse contains **red letter words**, then
for each element in the `lines` array there is an element in the `rlw_lines`
array.

- **if** `rlw_lines.length > 0` **then** `rlw_lines.length === lines.length`

👉 However, each element of `rlw_lines` is also an array, because sometimes
only part of a line is attributed to Jesus.

## Code Example

🤯 To understand all of the above, it is best to look at the code and review
the data and its structure.

🧠 Therefore, to better illustrate the **different cases that can be
encountered** in the data, I made an example of code in which I generate a
`README.md` file by reading some special chapters that have **different levels
of complexity**.

🤔 I am sure that it can help guide you and give you ideas.

🚀 Check out the code in the
[example repository here](https://github.com/mrk214/reading-json-files).

## Calculated Data

I believe that the data are quite complete; however, **to avoid some redundancy
and to maintain certain flexibility** and freedom, there are pieces of data
that are not explicitly provided, because **they can be calculated** in
different ways.

Thus, if you do not find a particular piece of data explicitly, it is very
likely that it can be calculated with code (_you just need to program it_ 👨‍💻).

## Hosted files on GitHub

You can clone the repository and use it in the usual ways, or you can
directly access the `JSON files hosted on GitHub`.

Inside each version's folder, there is a `README.md` file that includes:

- The **number of books** in that version.
- And **direct links to the JSON files hosted on GitHub**.

| usfm       | name                        | link                                                                                                   |
| ---------- | --------------------------- | ------------------------------------------------------------------------------------------------------ |
| `CSB`      | Christian Standard Bible    | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/CSB/README.md)      |
| `ESV`      | English Standard Version    | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/ESV/README.md)      |
| `KJV`      | King James Version          | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/KJV/README.md)      |
| `NASB2020` | New American Standard Bible | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/NASB2020/README.md) |
| `NIV`      | New International Version   | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/NIV/README.md)      |
| `NKJV`     | New King James Version      | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/NKJV/README.md)     |
| `NLT`      | New Living Translation      | [readme](https://github.com/mrk214/bible-data-en-eng/blob/main/data/en___eng___eng/NLT/README.md)      |

## Special Chapters

In some versions, there are special cases of certain chapters (`Chapter`) that
have a `chapter_usfm` in a different format from the others.

For example, `PSA.42_1`, when the common format is `PSA.42`.

To better understand their structure and how to handle them (_for example,
in a search_), I recommend reviewing the following fields in each of those
chapters:
`chapter_usfm`, `next?.usfm?.[0]`, `chapter_text`, `chapter_html`, `items`.

🔎 **CSB**

All the chapters in Psalms (`PSA`) follow this format in their `chapter_usfm`:

From `PSA.1_1`, `PSA.2_1`, `PSA.3_1` up to `PSA.148_1`, `PSA.149_1`,
`PSA.150_1`.

Except for these chapters:

- `PSA.1`
- `PSA.42`
- `PSA.73`
- `PSA.90`
- `PSA.107`
