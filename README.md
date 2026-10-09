# Immigration and Media Representation

This project investigates three questions concerning the relationship between immigration and the representation of foreign communities in the Italian media, using ISTAT data and newspaper articles from the daily newspaper *la Repubblica*.

The analysis, written in Python, focuses on **five foreign communities** (Morocco, Romania, China, Nigeria, France) and covers the period 2014-2018. It compares their **media representation** with their **actual demographic presence** as recorded by ISTAT (the Italian national statistics institute).

> **Note on the data.** The article corpus (the CHANGE-it dataset) contains the full text of newspaper articles protected by copyright and is **not included in this repository**. The notebook is therefore shared for demonstration purposes and **cannot be run without the original data**. The results (tables and charts) are, however, saved in the notebook and in the [`output/`](output/) folder, so they can be browsed directly. See the [Data](#data) section.
>
> **Language.** The notebook, its comments and the labels in the charts and tables are written in Italian.

## Authors

- [First Last](https://github.com/flavfr)
- [First Last](https://github.com/username-b)
- [First Last](https://github.com/username-c)

## Research questions

The project is built around three questions:

1. **Question 1.** Does the media over-represent or under-represent specific communities compared with their real presence in the country?
2. **Question 2.** How does the representation of the five nationalities in the articles change around a few major events between 2014 and 2018?
3. **Question 3.** When specific nationalities are mentioned, which contexts and keywords are most frequently associated with them?

### The five nationalities

| Nationality | Why it was chosen |
|---|---|
| Morocco | One of the largest communities in Italy, strongly linked by the media to security and migration topics. |
| Romania | A numerically very large community. |
| Nigeria | One of the communities that best match the typical perception of immigration tied to boat arrivals and asylum. |
| China | A highly present group, but often associated with very different media narratives (work, economy, "closed" communities). |
| France | The **control group**, used to check whether Italian media show a discrepancy in racialization. |

## Method

**Source data.** The corpus of *la Repubblica* articles (CHANGE-it) and ISTAT data on the resident foreign population by citizenship. We chose **citizenship** rather than place of birth because it matches the way journalists refer to nationality, and because it includes second-generation residents, who are often identified by the media with their parents' nationality. The ISTAT data were restricted to 2014-2018 so that both sources cover the same period.

**Analysis pipeline.**

1. **Dating the articles.** The date (year, month, day) is extracted from each article's URL with a regular expression, handling missing URLs and URLs without a date.
2. **Pre-processing and tokenization.** Headline and body text are merged, lowercased and split into words (tokens); Italian stopwords and very short tokens are removed.
3. **Selecting immigration-related articles.** An article is considered relevant if it contains at least one word from a hand-built immigration vocabulary (a set intersection).
4. **Heuristic for assigning articles to nationalities.** An article is linked to a nationality if it contains one of its morphological variants (e.g. *marocchino, marocchina, marocchini, marocchine*), unless the preceding word signals a non-migration context (e.g. *"corso di francese"*, a French course, or *"ristorante cinese"*, a Chinese restaurant). The list of these false positives was compiled by hand.
5. **Representation Index.** The ratio between a nationality's share of media presence and its share of the foreign population according to ISTAT. A value above 1 means media over-representation, below 1 means under-representation.
6. **Event analysis.** Monthly media presence (60 months) of the five nationalities, compared with three events: the Paris attacks (November 2015), the Macerata shooting (February 2018) and the Italian general election (March 2018). The effect of each event is measured as the ratio between the value in the month of the event and the average of the previous six months (excluding Macerata from the baseline used for the election).
7. **Lexical analysis.** Frequency of the words associated with each nationality, and presence of five hand-defined lexical categories: explicit racism and objectification, securitization and criminalization, culturalism and essentialism, utilitarianism and paternalism, dehumanization and biological metaphors.

## Main results

- **Representation versus real presence.** France is strongly over-represented (average index of about 17) even though it is the smallest community among the five. Romania and Morocco are strongly under-represented (average index of about 0.07 and 0.23), China moderately so (about 0.70), and Nigeria is close to parity (about 1.19). The case of France, chosen as the control group, suggests that its over-exposure depends on its political and institutional role rather than on the presence of a migrant community.
- **Events.** After the Paris attacks the presence of France and Morocco rises (about 2.5 and 2.2 times their baseline level). After the Macerata shooting the presence of Nigeria goes from 1.77% to 12.08% (about 6.8 times), the strongest signal in the analysis. The general election shows no clear pattern.
- **Vocabulary.** The language of securitization and criminalization is the most present category for all nationalities (about 38-44% of articles), alongside specific associations: security and terrorism for Morocco, social and work-related topics for Romania, migration for Nigeria, and international politics for China and France.

![Media presence of the nationalities, by month and with events](output/domanda_2/grafici/presenza_mesi_eventi.png)

<!-- Add here more images from output/domanda_1/grafici and output/domanda_3/grafici -->

## Limitations

- The lists of nationalities, immigration-related words and false positives were **defined manually** and are not exhaustive: they partly reflect the choices of whoever compiled them.
- The heuristic does not perform semantic analysis of the text, so it can produce both false positives and false negatives.
- The co-occurrence of a word and a nationality does not by itself imply a negative frame: the meaning depends on the context in which the word appears.

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

The folders in `output/` contain only **aggregated tables and charts** produced by the analysis, not the text of the articles.

## Data

The original data are **not included** in the repository (the `data/` folder is excluded through `.gitignore`).

- **CHANGE-it** (De Mattei, Cafagna, Dell'Orletta, Nissim, Gatt, *CHANGE-IT @ EVALITA 2020: Change Headlines, Adapt News, GEnerate*). It contains the full text of articles from *la Repubblica* and *Il Giornale*, which are protected by copyright and therefore cannot be redistributed. It is released under a CC BY-NC-SA 4.0 licence. Official version: [task page](https://sites.google.com/view/change-it/home) and [repository](https://github.com/michelecafagna26/CHANGE-IT). We worked on the *la Repubblica* articles.
- **ISTAT.** Data on the resident foreign population by citizenship and the list of Italian municipalities, available from the [ISTAT](https://www.istat.it) website.

To rerun the notebook, the files must be placed as follows:

```
data/
├── CHANGE-it/repubblica.csv
└── istat/
    ├── comuni/elenco_comuni.csv
    └── Dati_RCS/                # ISTAT files (those with "cittadinanza" in the name are used)
```

## Requirements

```
pandas
numpy
matplotlib
seaborn
```
