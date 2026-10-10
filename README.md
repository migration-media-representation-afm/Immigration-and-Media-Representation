# Immigration and Media Representation

This project investigates three questions concerning the relationship between immigration and the representation of foreign communities in the Italian media, using ISTAT data and newspaper articles from the daily newspaper *la Repubblica*.

The analysis, written in Python, focuses on **five foreign communities** (Morocco, Romania, China, Nigeria, France) and covers the period 2014-2018. It compares their **media representation** with their **actual demographic presence** as recorded by ISTAT (the Italian national statistics institute).

> **Note on the data.** The article corpus (the CHANGE-it dataset) contains the full text of newspaper articles protected by copyright and is **not included in this repository**. The notebook is therefore shared for demonstration purposes and **cannot be run without the original data**. The results (tables and charts) are, however, saved in the notebook. See the [Data](#data) section.

## Authors

- [Alice Somma](https://github.com/alice-somma)
- [Flavia Frezzini](https://github.com/flavfr)
- [Micol Mancinelli](https://github.com/username)

## Research questions

The project is built around three questions:

1. **Question 1.** Does the media over-represent or under-represent specific communities compared with their real presence in the country?
2. **Question 2.** How does the representation of the five nationalities in the articles change around a few major events between 2014 and 2018?
3. **Question 3.** When specific nationalities are mentioned, which contexts and keywords are most frequently associated with them?

### The five nationalities

| Nationality | Why it was chosen |
|---|---|
| Morocco | One of the largest communities in Italy, strongly linked by the media to security and migration topics. |
| Romania | A numerically large community. |
| Nigeria | One of the communities that best match the typical perception of immigration tied to sea arrivals and asylum. |
| China | A highly present group, but often associated with very different media narratives (work, economy, closed communities). |
| France | The **control group**, used to check whether Italian media show a discrepancy in racialization. |

## Method

**Source data.** The corpus of *la Repubblica* articles (CHANGE-it) and ISTAT data on the resident foreign population by citizenship. We chose **citizenship** rather than place of birth because it matches the way journalists refer to nationality, and because it includes second-generation residents, who are often identified by the media with their parents' nationality. The ISTAT data were restricted to 2014-2018 so that both sources cover the same period.

**Analysis pipeline.**

1. **Dating the articles.** The date (year, month, day) is extracted from each article's URL with a regular expression, handling missing URLs and URLs without a date.
2. **Pre-processing and tokenization.** Headline and body text are merged, lowercased and split into words (tokens); Italian stopwords and very short tokens are removed.
3. **Selecting immigration-related articles.** An article is considered relevant if it contains at least one word from a hand-built immigration vocabulary (a set intersection).
4. **Heuristic for assigning articles to nationalities.** An article is linked to a nationality if it contains one of its morphological variants (e.g. *marocchino, marocchina, marocchini, marocchine*), unless the preceding word signals a non-migration context (e.g. *"corso di francese"*, a French course, or *"ristorante cinese"*, a Chinese restaurant).
5. **Representation Index.** The ratio between a nationality's share of media presence and its share of the foreign population according to ISTAT. A value above 1 means media over-representation, below 1 means under-representation.
6. **Event analysis.** Monthly media presence (60 months) of the five nationalities, compared with three events: the Paris attacks (November 2015), the Macerata shooting (February 2018) and the Italian general election (March 2018). The effect of each event is measured as the ratio between the value in the month of the event and the average of the previous six months.
7. **Lexical analysis.** Frequency of the words associated with each nationality, and presence of five hand-defined lexical categories: explicit racism and objectification, securitization and criminalization, culturalism and essentialism, utilitarianism and paternalism, dehumanization and biological metaphors.

## Main results

- **Representation versus real presence.** France is strongly over-represented (average index of about 17) even though it is the smallest community among the five. Romania and Morocco are strongly under-represented (average index of about 0.07 and 0.23), China moderately so (about 0.70), and Nigeria is close to parity (about 1.19). The case of France, chosen as the control group, suggests that its over-exposure depends on its political and institutional role rather than on the presence of a migrant community.
- **Events.** After the Paris attacks the presence of France and Morocco rises (about 2.5 and 2.2 times their baseline level). After the Macerata shooting the presence of Nigeria goes from 1.77% to 12.08% (about 6.8 times), the strongest signal in the analysis. The general election shows no clear pattern.
- **Vocabulary.** The language of securitization and criminalization is the most present category for all nationalities (about 38-44% of articles), alongside specific associations: security and terrorism for Morocco, social and work-related topics for Romania, migration for Nigeria, and international politics for China and France.

## Skills and Python constructs used

The project uses the `pandas`, `numpy`, `matplotlib` and `seaborn` libraries, together with the standard-library modules `re` and `os`.

| Area | Main constructs and functions |
|---|---|
| Data structures | lists, dictionaries (`.items()`), sets and set intersection, lists of dictionaries converted into a `DataFrame` |
| Control flow | nested `for` loops, `range`, `enumerate`, `continue`, `if/else`, custom functions, `isinstance` to handle missing values |
| Strings and regex | `re.search`, capture groups, raw strings, `str.lower()`, `str.findall(r"\w+")`, f-strings, `join` |
| NumPy | `to_numpy`, `np.vectorize` |
| Pandas, reading and combining | `read_csv` (`sep` and `dtype` parameters), `concat`, `merge`, `join` |
| Pandas, selection and transformation | boolean indexing with multiple conditions (`&`), `.loc`, `.iloc`, `.isin`, `.map`, `.rename`, `.copy`, `.fillna`, `.sort_values`, `.round` |
| Pandas, aggregation and reshaping | `groupby`, `.mean`, `.sum`, `pivot`, `melt` |
| Files and folders | `os.listdir`, `os.path.join`, `os.path.isfile`, `os.makedirs`, `to_csv` (with `index=False` and `encoding="utf-8-sig"`) |
| Visualization | Matplotlib: `plt.subplots`, `ax.plot`, `set_xticks`, `axhline`, `set_yscale("log")`, `ticker.ScalarFormatter`, `savefig`. Seaborn: `catplot`, `barplot`, the `hue` parameter |
| Calculation | the `//` and `%` operators (to rebuild year and month from a running index), percentages and ratios |

## Repository structure

```
.
├── notebook_progetto.ipynb      # full notebook, with saved outputs
├── output/
│   ├── domanda_1/               # tables and charts for question 1
│   ├── domanda_2/               # tables and charts for question 2
│   ├── domanda_3/               # tables and charts for question 3
│   └── tabella_riassuntiva.csv  # final summary table
└── README.md
```

## Data

The repository does not contain any raw data; this section therefore describes the datasets the notebook works on.

### 1. Article corpus: CHANGE-it (*la Repubblica*)

**What it is.** CHANGE-it is a large collection of articles from the Italian newspaper *la Repubblica*, extracted for the CHANGE-IT task of the EVALITA 2020 evaluation campaign. The file provided, `repubblica.csv`, contains the headline, the full text and the URL of each article.

**Structure.** Each row is one article, described by three columns:

| Column | Content | How we use it |
|---|---|---|
| `headline` | The headline of the article. | Merged with the body text before tokenization. |
| `full_text` | The full text of the article. | Merged with the headline, then tokenized and searched for nationality terms and keywords. |
| `url` | The web address of the article, which contains its publication date in the form `.../YYYY/MM/DD/...`. | A regular expression extracts year, month and day, which become the columns `Anno`, `Mese` and `Giorno`. The file has no separate date column, so the URL is the only source of the publication date. |

**Columns we derive.** `testo_completo` (headline and body merged), `tokens` (the words of the text), `token_filtrati` (the tokens left after removing stopwords and very short words) and `immigrazione` (true if the article contains at least one word of our immigration vocabulary). All the later analyses are computed from these columns.

**Period.** The analysis covers 2014-2018, i.e. 60 months.

### 2. ISTAT data on foreign residents

**What it is.** An ISTAT dataset with the number of residents who were born abroad or hold a foreign citizenship, for every Italian municipality and for the larger territorial divisions (provinces, regions, macro-areas), provided on a yearly basis from 2002 to 2025. The data come as semicolon-separated CSV files in the folder `data/istat/Dati_RCS/`, in two series: one by citizenship and one by country of birth. We use only the citizenship files, i.e. those with "cittadinanza" in their name (24 files, which fits one file per year from 2002 to 2025), and concatenate them into a single table, `df_cittadinanza`. We chose citizenship rather than country of birth because it better matches how newspapers refer to nationality, and because it includes second-generation residents.

**Structure.** Each row gives the number of residents for one territory, one year and one citizenship:

| Column | Content |
|---|---|
| `Anno` | The reference year. |
| `Denominazione`, `Codice Istat` | The name and ISTAT code of the territory. The row `Italia` holds the national totals. |
| `Stato di cittadinanza`, `Codice stato di cittadinanza/nascita` | The citizenship (country) and its code. |
| `Maschi`, `Femmine`, `Totale` | Number of male residents, female residents and total. |

**What we keep.** Only the years 2014-2018 (to match the period covered by the articles), only the national level (`Denominazione` equal to `Italia`) and the five countries of interest, using the total counts. The finer territorial breakdown (municipalities, provinces, regions) is not used. The total foreign population of each year is the sum of `Totale` over all citizenships except `Italia` (i.e. excluding Italian citizens). It is the denominator used to compute each nationality's share of the foreign population, which is then compared with its share of media coverage.

### 3. List of municipalities

`elenco_comuni.csv`, provided together with the ISTAT data, contains the identification codes and further information on every Italian municipality, including its province, region and geographic division. It is loaded as a geographic reference, but the analyses reported in the notebook are carried out at national level, so it is not needed to reproduce the figures.

### Expected folder layout

To run the notebook, the files must be placed as follows:

```
data/
├── CHANGE-it/repubblica.csv
└── istat/
    ├── comuni/elenco_comuni.csv
    └── Dati_RCS/                # ISTAT files (those with "cittadinanza" in the name are used)
```

