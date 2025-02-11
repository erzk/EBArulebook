
# EBArulebook

<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![CRAN
status](https://www.r-pkg.org/badges/version/EBArulebook)](https://cran.r-project.org/package=EBArulebook)

<!-- badges: end -->

`EBArulebook` is a package to scrape the [EBA Single
Rulebook](https://www.eba.europa.eu/regulation-and-policy/single-rulebook/).

The input to this package is the Single Rulebook website. Outputs from
this package are the rules and
[Q&As](https://www.eba.europa.eu/single-rule-book-qa) published on the
Single Rulebook website in a format more amenable to text and network
analysis.

The goal of `EBArulebook` is to scrape the [Single
Rulebook](https://eba.europa.eu/regulation-and-policy/single-rulebook)
and [Q&As](https://eba.europa.eu/single-rule-book-qa) published by the
European Banking Authority (EBA). This package was developed while
working on a Staff Working Paper:

**Amadxarif, Z., Brookes, J., Garbarino, N., Patel, R., Walczak, E.
(2019) *[The Language of Rules: Textual Complexity in Banking
Reforms.](https://www.bankofengland.co.uk/working-paper/2019/the-language-of-rules-textual-complexity-in-banking-reforms)*
Staff Working Paper No. 834. Bank of England.**

If you use this package, then please cite the paper.

Any use of this package with the Single Rulebook must comply with the
Single Rulebook’s [Terms of
Use](https://www.eba.europa.eu/legal-notice).

## Installation

You can install the development version of EBArulebook from
[GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("erzk/EBArulebook")
```

## Examples

This packages makes it easy to obtain EBA data in a tabular form

``` r
library(EBArulebook)
```

To get **EBA single rulebook** (tested only on Capital Requirements
Regulation (*CRR*))

``` r
all_eba_rules <- scrape_EBA()

dplyr::glimpse(all_eba_rules)
```

To get **EBA single rulebook Q&As**

``` r
qa_df <- scrape_EBA_QA()

dplyr::glimpse(qa_df)
```

## European Banking Authority - The Single Rulebook

Code in this repository was used to acquire and analyse data used in the
forthcoming Staff Working Paper.

Check the
[vignettes](https://github.com/erzk/EBArulebook/tree/master/vignettes)
for more details and examples.

### Get the full website

-   Install [phantomjs](https://github.com/ariya/phantomjs)

-   Write a simple
    [scraper](https://www.thedataschool.co.uk/brian-scally/web-scraping-javascript-content/)
    `scrape_EBA.js`:

This scraper was developed on Ubuntu 16.04. It was tested only on the
Capital Requirements Regulation (CRR) but it should also work on other
parts of the EBA rulebook.

The EBA rulebook is displayed dynamically so the first step is to scrape
the text using a headless browser (phantomjs).

``` r
var url = 'https://eba.europa.eu/regulation-and-policy/single-rulebook/interactive-single-rulebook/-/interactive-single-rulebook/toc/504'; 

var page = new WebPage(); 
var fs = require('fs'); 

page.open(url, function (status) { 
just_wait(); }); 

function just_wait() { 
setTimeout(function() { 
fs.write('website_phantom.html', 
page.content, 'w'); 
phantom.exit(); }, 2500); 
}
```

-   Run the scraper from the command line with

``` bash
phantomjs scrape_EBA.js
```

This downloads the entire page which then needs to be cleaned.

### Parse downloaded html pages

-   Analyse the output `website_phantom.html`

-   Use `rvest` to extract key data

The rulebook is using html to display the rules and is constructed in a
simple way:

<https://eba.europa.eu/regulation-and-policy/single-rulebook/interactive-single-rulebook/-/interactive-single-rulebook/article-id/2002>

where the last number after ‘/’ is an ID.

To solve this problem I extracted the relevant IDs by running regex on
the scraped html file. Look for the IDs: ‘article-id/\[DIGIT\]’. See
`parse_EBA_page()` for details.

## Q&As

### Scraper

Search for questions. There are 1757 Q&As (1652 Final and 105 Rejected)
as of 8 June 2019. Status: use ‘All’: both ‘Final’ and ‘Rejected’.

Maximum displayed Q&As per page is 200 so 9 pages in total (see the
final part the URL: ‘cur=2’).

Use `scrape_EBA_QA.js` from the command line

``` bash
phantomjs scrape_EBA.js
```

Edit the .js file updating pages to scrape:

-   Page 1

<https://eba.europa.eu/single-rule-book-qa?p_p_id=questions_and_answers_WAR_questions_and_answersportlet&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&p_p_col_id=column-1&p_p_col_pos=1&p_p_col_count=2&_questions_and_answers_WAR_questions_and_answersportlet_keywords=&_questions_and_answers_WAR_questions_and_answersportlet_advancedSearch=false&_questions_and_answers_WAR_questions_and_answersportlet_andOperator=true&_questions_and_answers_WAR_questions_and_answersportlet_jspPage=%2Fhtml%2Fview.jsp&_questions_and_answers_WAR_questions_and_answersportlet_statusSearch=All&_questions_and_answers_WAR_questions_and_answersportlet_viewTab=1&_questions_and_answers_WAR_questions_and_answersportlet_keyword=&_questions_and_answers_WAR_questions_and_answersportlet_articleSearch=&_questions_and_answers_WAR_questions_and_answersportlet_typeOfSubmitterSearch=&_questions_and_answers_WAR_questions_and_answersportlet_publicIdSearch=&_questions_and_answers_WAR_questions_and_answersportlet_startingDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_endingDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_applicableFromDate=&_questions_and_answers_WAR_questions_and_answersportlet_applicableUntilDate=&_questions_and_answers_WAR_questions_and_answersportlet_publicationFromDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_publicationToDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_currentTab=All&_questions_and_answers_WAR_questions_and_answersportlet_resetCur=false&_questions_and_answers_WAR_questions_and_answersportlet_delta=200>

-   Page 2

<https://eba.europa.eu/single-rule-book-qa?p_p_id=questions_and_answers_WAR_questions_and_answersportlet&p_p_lifecycle=0&p_p_state=normal&p_p_mode=view&p_p_col_id=column-1&p_p_col_pos=1&p_p_col_count=2&_questions_and_answers_WAR_questions_and_answersportlet_delta=200&_questions_and_answers_WAR_questions_and_answersportlet_keywords=&_questions_and_answers_WAR_questions_and_answersportlet_advancedSearch=false&_questions_and_answers_WAR_questions_and_answersportlet_andOperator=true&_questions_and_answers_WAR_questions_and_answersportlet_jspPage=%2Fhtml%2Fview.jsp&_questions_and_answers_WAR_questions_and_answersportlet_statusSearch=All&_questions_and_answers_WAR_questions_and_answersportlet_viewTab=1&_questions_and_answers_WAR_questions_and_answersportlet_keyword=&_questions_and_answers_WAR_questions_and_answersportlet_articleSearch=&_questions_and_answers_WAR_questions_and_answersportlet_typeOfSubmitterSearch=&_questions_and_answers_WAR_questions_and_answersportlet_publicIdSearch=&_questions_and_answers_WAR_questions_and_answersportlet_startingDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_endingDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_applicableFromDate=&_questions_and_answers_WAR_questions_and_answersportlet_applicableUntilDate=&_questions_and_answers_WAR_questions_and_answersportlet_publicationFromDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_publicationToDateSearch=&_questions_and_answers_WAR_questions_and_answersportlet_currentTab=All&_questions_and_answers_WAR_questions_and_answersportlet_resetCur=false&_questions_and_answers_WAR_questions_and_answersportlet_cur=2>

This will generate 9 html pages with max `200` Q&As. These scraped pages
need to be then processed.

### Parsing

Extract URL to the actual questions from the downloaded website using
`parse_EBA_QA_page()`

### Scraping

Scrape the actual Q&As into a tabular form with `scrape_EBA_QA()`

### Disclaimer

This package is an outcome of a research project. All errors are mine.
All views expressed are personal views, not those of any employer. The
package is provided as-is and is not updated. Data provided here was
scraped in 2019 so obtaining the latest data might require running the
scraper again.
