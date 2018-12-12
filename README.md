TL;DR
------------
Script for processing wikipedia dumps (in any language) and extracting useful metadata (inter-language links, how often a string refers to a wikipage etc.) from it.

Install the requirements, modify the makefile appropriately, and run. 

Description
------------
This repository contains scripts to perform the following preprocessing steps.

1. Download the relevant files from the wikipedia dump (target `dumps` in `makefile`). Specifically, it downloads

````
*-pages-articles.xml.bz2
*-page.sql.gz
*-pagelinks.sql.gz
*-redirect.sql.gz
*-categorylinks.sql.gz
*-langlinks.sql.gz
````

2. Extract text with hyperlinks from the *pages-articles.xml.bz2 file (target `text` in `makefile`), using the [wikiextractor](https://github.com/attardi/wikiextractor).

3. Create Wikipedia page id to page title map using *page.sql.gz (target `id2title` in `makefile`).

4. Redirects map using *redirect.sql.gz (target `redirects` in `makefile`).

5. Create a inter-language link mapping from Wikipedia titles to English Wikipedia titles using *langlinks.sql.gz (target `langlinks` in `makefile`).

6. Compute hyperlink counts (how many hyperlinks point to a certain title) for wikipedia titles (target `countsmap` in `makefile`).

7. Compute probability indices using which we can compute the probability for a string (e.g., Berlin) referring a Wikipedia title (e.g., Berlin_(Band)) (target `probmap` in `makefile`).

Requirements
-----------------
You need python >=3.5. Also install the following two packages.

````python
pip3 install bs4
pip3 install hanziconv # (for chinese traditional to simplified conversion)
````

Running
-------
For ease of use, we provide a `makefile` that specifies targets to automatically run all processing scripts. To use the makefile, you need to

1. Download/Clone [wikiextractor](https://github.com/attardi/wikiextractor). Modify path `WIKIEXTRACTOR` in makefile to point to it.

2. Create a download directory for wikipedia dumps (say `/path/to/dumpdir`) and set `DUMPDIR` accordingly. The wikipedia dumps will be downloaded under `DUMPDIR` (for instance the Turkish Wikipedia dumps will be downloaded under `DUMPDIR/trwiki/`)

**For Cogcomp Internal Use**: 
Wikipedia dumps are already available under `/shared/corpora/wikipedia_dumps`, so simply set the `DUMPDIR` to `/shared/corpora/wikipedia_dumps`. For instance, the Turkish wikipedia resources are in `/shared/corpora/wikipedia_dumps/trwiki`.

3. Specify a `OUTDIR`. This is the directory where the resources will be generated (eg. `path/to/my/resources/trwiki` for Turkish Wikipedia). To keep the code generic, you may want to use the `lang` variable to define the `OUTDIR` (e.g., `path/to/my/resources/${lang}wiki`).

4. (Optional) Modify the `DATE` variable to identify the timestamp of the Wikipedia dump to download.

5. Make sure `PYTHONBIN` points to the correct python binary.

6. Run the command `lang=CODE make all`, where `CODE` is the two-letter language code used by Wikipedia to identify the language (eg. `tr` for Turkish, `es` for Spanish etc.). This should perform all the preprocessing steps above by following the build dependencies specified in the makefile.

Sanity Check
------

After `lang=CODE make all` completes successfully (takes ~18 mins on single-core machine for Turkish Wikipedia), you should have files with following line counts (for 20180720 dump of Turkish Wikipedia),

````
222367  idmap/fr2entitles   
559553  idmap/trwiki-20180720.id2t
247338  idmap/trwiki-20180720.r2t
559552  trwiki-20180720.counts
2941652 surface_links
936100  probmap/trwiki-20180720.p2t2prob
936100  probmap/trwiki-20180720.t2p2prob
1426771 probmap/trwiki-20180720.t2w2prob
745829  probmap/trwiki-20180720.tnr.p2t2prob
745829  probmap/trwiki-20180720.tnr.t2p2prob
1273216 probmap/trwiki-20180720.tnr.t2w2prob
1273216 probmap/trwiki-20180720.tnr.w2t2prob
1426771 probmap/trwiki-20180720.w2t2prob
````