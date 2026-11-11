# MedAustron Publication Valuation

Excel model that puts a monetary value on the scientific publications of **MedAustron**, a particle therapy and research centre in Wiener Neustadt, Austria. It covers 524 publications from 2005 to 2026 and the literature that cites them.

Built for the *Economics of Big Science* course (University of Milan, in cooperation with CERN), 2026. This repository covers the publications part of a larger course project.

> Preliminary student project. Results are estimates built on explicit assumptions, not audited figures.

## What the model does

| Tier | Definition | Valuation method |
|------|-----------|------------------|
| **T0** | Publications produced by or with MedAustron (524 papers) | Production cost: researcher labour per paper plus publication cost |
| **T1** | Papers that cite at least one T0 paper | Read-time value: researcher time spent reading a cited paper |

**T0 production cost per paper**

```
Cost = (AvgSalary x Engagement / Productivity) x N_coauthors + PublicationCost
```

**T1 read-time value**

```
Value = Citations x AvgPages x ReadTime_per_page x (HourlySalary / 60)
```

Every value is then discounted to the base year 2026 at three social discount rates (2%, 3%, 5%), following the European Commission's *Guide to Cost-Benefit Analysis*.

## Key parameters

| Parameter | Value |
|-----------|-------|
| Annual gross salary | €70,000 |
| Working hours per year | 1,760 (hourly rate €39.77) |
| Engagement in publication-relevant research | 55% |
| Productivity | 6 papers / researcher / year |
| Publication cost | €310 / paper |
| Pages per T1 paper | 10 (median of a 42-paper sample; mean 13.3) |
| Reading time | 5 min / page |
| Social discount rates | 2% / 3% / 5% |

All justifications and references are in the **Input Table** sheet.

## Results

| | Undiscounted | SDR 2% | SDR 3% | SDR 5% |
|---|---:|---:|---:|---:|
| **T0** total value (524 papers) | €23,463,823 | €26,397,047 | €28,047,264 | €31,777,443 |
| **T1** read-time value | €166,615 | €194,609 | €210,483 | €246,614 |

T1 uses 5,027 citation events as an upper bound. The 3,371 unique citing papers are reported as a lower-bound indicator.

## Workbook structure

| Sheet | Purpose |
|-------|---------|
| `Input Table` | All parameters with units, justifications and sources |
| `Assumptions` | Assumptions behind the estimates |
| `T0 Calculations` | Per-paper production cost, citation value and discounting |
| `T1 Calculations` | Citing papers retrieved from OpenAlex |
| `T1 Page Sample` | Sample used to estimate average pages per T1 paper |
| `Yearly_Summary` | Annual aggregation of publications, authors, citations and value |
| `Results` | Totals across the three discount-rate scenarios |
| `Raw_*`, `Master_All`, `Source_Counts` | Source data and the deduplicated master list |

## Excel techniques used

- Parameter-driven model: every calculation reads from the Input Table, with no hardcoded constants.
- Discount factors built from a base year and a rate, applied per publication year.
- Multi-condition statistics (`AVERAGEIFS`, `COUNTIFS`) and a helper-column median to exclude invalid page ranges.
- Per-year aggregation (`SUMIFS`) feeding the summary and results sheets.
- Multi-source data consolidation and deduplication by DOI.

## Data sources

- BibTeX export provided by CERN
- [Lens.org](https://www.lens.org)
- [PubMed](https://pubmed.ncbi.nlm.nih.gov)
- [OpenAlex](https://openalex.org): metadata, citation counts and T1 retrieval

T1 papers were retrieved with the OpenAlex API `cites:` filter on the OpenAlex IDs of the T0 papers (queries run April 2026), then deduplicated by DOI.

## Method references

- Giffoni, F., Colnot, L., Sirtori, E. (2024). *The Link Between Large Scientific Collaboration and Productivity. Rethinking How to Estimate the Monetary Value of Publications.* https://doi.org/10.5281/zenodo.14141946
- Brysbaert, M. (2019). How many words do we read per minute? A review and meta-analysis of reading rate. *Journal of Memory and Language*, 109, 104047. https://doi.org/10.1016/j.jml.2019.104047
- European Commission (2008). *Guide to Cost-Benefit Analysis of Investment Projects.*

## Limitations

- The production-cost approach attributes the full cost of each T0 paper to MedAustron, including co-authored work.
- OpenAlex may under-represent conference proceedings, grey literature and non-English publications.
- The salary figure is a European average, not specific to each author's country.
- The T1 citation-event count assumes each citation is a separate reading act.

## Usage

Open the `.xlsx` in Excel. To test a scenario, change values in the `Input Table`; all other sheets recalculate.

