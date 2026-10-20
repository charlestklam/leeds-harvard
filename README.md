# Leeds Harvard for biblatex

A biblatex/Biber style for the University of Leeds **Leeds Harvard** referencing style.
It follows the Library's guidance:

- [Leeds Harvard referencing examples](https://library.leeds.ac.uk/referencing-examples/9/leeds-harvard)
- [Journal article](https://library.leeds.ac.uk/referencing-examples/9/leeds-harvard/11/journal-article)
- [Common issues](https://library.leeds.ac.uk/info/1402/referencing/50/leeds-harvard-introduction/3)

The style doesn't depend on any engine. It gives the same output on **pdfLaTeX, XeLaTeX and LuaLaTeX**, and the test script checks this.

| File | Purpose |
| --- | --- |
| `leeds-harvard.bbx` | Reference list (bibliography) style |
| `leeds-harvard.cbx` | In-text citation style (author–date) |
| `test/main.tex` | Demo and regression document (uses `iftex` to load fonts safely on each engine) |
| `test/leeds-harvard-test.bib` | Fixtures copied from the Leeds guide's examples |
| `test/expected-*.txt` | Expected output strings |
| `test_all_engines.ps1` | Builds and checks the demo on all three engines |

## Usage

Put `leeds-harvard.bbx` and `leeds-harvard.cbx` next to your `.tex` file (or in your local `texmf` tree, or upload them to Overleaf). Then:

```latex
\usepackage[british]{babel}
\usepackage{csquotes}
\usepackage[style=leeds-harvard]{biblatex}
\addbibresource{references.bib}
...
\parencite{key}            % (Shannon, 1948)
\textcite{key}             % Shannon (1948)
\parencite[24]{key}        % (Shannon, 1948, p.24)
\parencite[20--29]{key}    % (Shannon, 1948, pp.20-29)
\parencite[iv]{key}        % (Dirac, 1958, iv)
\parencite{a,b}            % (MacNaughton et al., 1999; Ahmed and Meehan, 2012)
\parencite[Shannon, 1948, cited in][9]{tanenbaum}  % (Shannon, 1948, cited in Tanenbaum, 2003, p.9)
\printbibliography            % automatically titled "References"
```

Compile with `<engine> → biber → <engine> → <engine>`.

### Options

| Option | Default | Effect |
| --- | --- | --- |
| `no-place` (or `noplace`) | `false` | Omits `[no place]` when `location` is missing. Set to `true` (strict Leeds) to print `[no place]: Publisher`. |
| `hyphenranges` | `true` | Uses a hyphen in page and date ranges, as the guide does (`pp.652-669`). Set it to `false` for an en dash. |
| `url`, `doi` | `true` | Standard biblatex switches. Turn them off to hide `[Online]`, the access date and the link. |
| `uniquename` | `false` | Set to `init` to cite *(R.F. Jones, 1998)* when two authors share a surname. |

## Entering data

| Leeds source type | Entry type / fields |
| --- | --- |
| Journal article | `@article`: `journaltitle`, `volume`, `number`, `pages` |
| Article number | `@article` with `eid` and no `pages` (or `eid` = `pages`, as ADS exports it) gives *article no: 25809 [no pagination]* |
| Supplement | `number = {Supp. 3}` |
| Pre-/post-print, forthcoming | `pubstate = {preprint}` / `{postprint}` / `{forthcoming}` |
| Newspaper | `@article` + `entrysubtype = {newspaper}`, full `date` (gives *24 May, p.1*) |
| Book / e-book | `@book`: `edition`, `series` + `number`, `editor`, `translator`, `location`, `publisher` |
| E-book reader | `howpublished = {Kindle DX e-book}` gives *[Kindle DX e-book]* |
| Edited book | `@collection` with `editor` gives *Wheeler, J.A. and Zurek, W.H. eds.* |
| Chapter in edited book | `@incollection`: `editor`, `booktitle`, `pages` |
| Conference paper | `@inproceedings`: `booktitle`, `eventdate`, `venue` |
| Thesis | `@thesis`, `type = {phdthesis}`, `institution` |
| Report / working paper | `@report`: `institution` or `publisher`, optional `type` + `number` |
| Website | `@online` |
| Blog post | `@online` with `maintitle` (the blog's name) and a full `date` |
| Dataset | `@dataset`: `publisher` (e.g. *Figshare*) |
| Software | `@software`: `version` |
| Unpublished | `@unpublished` |
| Corporate author | `author = {{Royal Academy of Engineering}}` |
| Corporate abbreviation | add `shortauthor = {NASA}`. The first citation gives *(National Aeronautics and Space Administration [NASA], 2011)*, later ones *(NASA, 2011)* |
| No author | `author = {Anon}` |

The style adds these automatically:

- **[Online]**, *[Accessed D Month YYYY]* (from `urldate`) and *Available from:* whenever the entry has a `doi` or `url`. It prints `https://doi.org/…` in preference to the URL.
- **[no date]** in the reference list and *no date* in citations.
- **[no publisher]** for books when publisher is missing (or **[no place]: [no publisher]** if `no-place=true` is set). When publisher is present but `location` is omitted, the publisher alone is printed by default (no `[no place]`).
- **[no pagination]** for articles that have a volume or issue but no pages.

Enter titles in sentence case. The style doesn't change the case of titles.

## Physics and astronomy: NASA ADS exports

You can paste BibTeX from the [SAO/NASA Astrophysics Data System](https://ui.adsabs.harvard.edu/) into your `.bib` file unchanged. The bibcode works as the citation key: `\parencite{2026ApJ..1008L..60C}` gives *(Chen et al., 2026)*. A source map in the style fixes the rest as Biber reads the file:

| ADS export | What the style does |
| --- | --- |
| `journal = {\apjl}`, `{\mnras}`, `{\aap}`, … | Prints the full journal title (*The Astrophysical Journal Letters*). Leeds Harvard doesn't allow abbreviated titles. You don't need `aas_macros.sty`. |
| `eid = {L60}`, `pages = {L60}` | *article no: L60 [no pagination]*, not *p.L60* |
| `doi` | Links to `https://doi.org/…` |
| No `doi` | Links to your own `url` (the publisher or journal page) if there is one. Otherwise it links to `adsurl` (the ADS abstract page). |
| `journal = {arXiv e-prints}` | Prints *[Pre-print]. … arXiv. [Online]. Available from: https://doi.org/10.48550/arXiv.1501.02297*. The arXiv DOI is built from `eprint` when ADS doesn't give one. |

For example, the ADS record [2026ApJ..1008L..60C](https://ui.adsabs.harvard.edu/abs/2026ApJ..1008L..60C/abstract) prints as:

> Chen, C., Armitage, P.J., Nixon, C.J., Lubow, S.H. and Martin, R.G. 2026. On the Formation of Retrograde S-type Planets in Binaries with a Polar Circumbinary Disk. *The Astrophysical Journal Letters*. [Online]. **1008**(2), article no: L60 [no pagination]. Available from: https://doi.org/10.3847/2041-8213/ae9d4d

To build the `.bib` straight from the bibcodes, arXiv IDs or DOIs used as keys in your `.tex`, you can use [adstex](https://github.com/yymao/adstex) (`adstex paper.tex -o refs.bib`).

ADS can't supply everything, so check these yourself:

- **Access date.** ADS has no `urldate`. Add `urldate = {YYYY-MM-DD}` with the date you actually read the item, and the style prints *[Accessed …]*.
- **Title case.** ADS locks title case with `"{…}"`. To follow the Leeds sentence-case examples, edit the title by hand. The style won't lower-case it for you, because that would break names like *S-type* and *AGN*.
- **Journals not on the list.** If ADS gives a journal macro the style doesn't know, LaTeX reports an undefined command. Replace it with the full title. See `\lh@adsjournals` in [leeds-harvard.bbx](leeds-harvard.bbx) for the list.
- **A&A** prints as *Astronomy and Astrophysics*, because an ampersand can't pass through Biber's control file. Write `journal = {Astronomy \& Astrophysics}` if you want the ampersand.

## Testing

```powershell
powershell -ExecutionPolicy Bypass -File .\test_all_engines.ps1
```

For each engine the script:

1. runs the full compile sequence;
2. fails on a non-zero exit code, an undefined citation, any biblatex warning or a Biber warning;
3. pulls the text out of the PDF and checks it against `test/expected-references.txt` and `test/expected-citations.txt`;
4. checks that all three engines produce identical text.

## Where the output differs from individual guide examples

A few of the guide's examples don't match its own templates. In those cases the style follows the **template**:

| Guide example | Style output | Why |
| --- | --- | --- |
| `pp. S236-S242` | `pp.S236-S242` | Everywhere else the guide writes `pp.` with no space |
| `Bonacin, R., Nabuca, O.F., and Pierozzi, I.` | `…, Nabuco, O.F. and Pierozzi, I.` | No serial comma, as in every other example |
| Andersson Cederholm *et al.* e-book has no `[Online]` | `[Online]` included | The e-book template and the "Online items" rule both require it |
| Blog date `06 May` | `6 May` | Other dates in the guide (e.g. `3 April 2024`) have no leading zero |
| `paragraph?.` | `paragraph?` | biblatex doesn't add a full stop after `?` |
| `Jones 1998a` = *How to write…* | `1998a` = *Advanced writing tips* | Biber gives same-author, same-year letters in title order |

Citation order inside one bracket isn't sorted automatically (`sortcites=false`). The guide wants the oldest source first, so write keys in that order.


## Beta Notice & Reporting Discrepancies (v0.1.0)

> **Important:** This package is currently in **v0.1.0 (Public Beta)**. While core entry types (`@article`, `@book`, `@incollection`, `@online`) match the University of Leeds Harvard specification, rare entry types and edge cases may still fall back to standard `biblatex-authoryear` behavior.

If you encounter formatting that differs from the official university library guidelines, please report it via the [GitHub Issue Tracker](https://github.com/charlestklam/leeds-harvard/issues).

To help us fix the issue quickly, please include:
1. **The `.bib` entry:** The exact BibTeX snippet causing the issue.
2. **Current output:** How the citation and bibliography entry currently render in your document.
3. **Expected output:** The exact text/punctuation required by the official Leeds Library Harvard guide.
4. **Reference link:** A direct link to the relevant section of the [Leeds Harvard Referencing Guide](https://library.leeds.ac.uk/referencing).

---

### Need an Immediate Fix for a Deadline? (Escape Hatch)

If a specific reference is not rendering according to Leeds guidelines and you need to submit immediately without waiting for a package update:

* **Override specific fields in your `.bib` file:**  
  You can bypass field formatters by passing raw text or using curly braces:
  ```bibtex
  @online{sample2026,
    author = {{World Health Organization}},
    title  = {Title of the Report},
    year   = {2026},
    note   = {[Online]. Available at: \url{https://...} [Accessed 14 May 2026]}
  }
  ```