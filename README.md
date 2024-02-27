# MobiLipid

MobiLipid aims to streamline lipidomics workflows by offering a fully
automated solution for assessing and correcting collision cross section
(CCS) bias in ion mobility-mass spectrometry (IM-MS) analyses. Employing
a newly established <sup>DT</sup>CCS<sub>N2</sub> library for
U<sup>13</sup>C labeled lipids, which is provided together with the
code, MobiLipid eliminates the need to measure additional external
calibration besides vendor specific calibration requirements by internal
standardization. Our tool enhances CCS quality control by providing a R
Markdown that integrates into IM-MS lipidomics workflows, requiring a
low number of lipids detected per lipid class for effective
implementation of CCS bias calculation and correction.

## Introduction to MobiLipid

MobiLipid is a R markdown enabling CCS quality control for IM-MS
lipidomics by internal standardization. For utilizing the MobiLipid
workflow samples measured with (LC-)IM-MS have to be spiked with
U<sup>13</sup>C labeled internal standards (fully labeled yeast extract
([Neubauer et al. 2012](#ref-neubauer13CellExtract2012)))

## Necessary installations

To run MobiLipid it is necessary to install
[R](https://cran.r-project.org/) and [R
studio](https://www.rstudio.com/products/rstudio/download/).

Afterwards, it has to be ensured that all necessary packages, which are
needed to run MobiLipid, are installed. Therefore, paste the following
code into the console of R studio and press enter.

``` r
# List of packages to check/install
packages <- c("htmltools" ,"rmarkdown", "data.table", "ggplot2", "DT", "webshot", "tcltk", "knitr", "ggbeeswarm")

# Check if packages are installed, if not, install them
for (package in packages) {
  if (!requireNamespace(package, quietly = TRUE)) {
    install.packages(package)
  }
}
```

## Download of MobiLipid

Furthermore, it has to be ensured that the .Rmd file containing
MobiLipid (“MobiLipid_CCS-bias-calculation.Rmd” and/or
“MobiLipid_CCS-bias-calculation_CCS-correction.Rmd”) as well as the .csv
file of the <sup>DT</sup>CCS<sub>N2</sub> library for U<sup>13</sup>C
labeled lipids of yeast (“U13C_DT_CCS_library.csv”) is downloaded. This
can be done by downloading the entire GitHup repository as a .zip file
and unzip the files.

## Running MobiLipid

Finally, MobiLipid can be run. Therefore, paste the code below into the
console of R studio and press enter. The code will open 3 pop-up windows
where you have to choose the following files (be aware that the windows
might open behind the main window of R studio):

1.  **Select the R markdown which should be used for data processing:**

    This needs to be a .Rmd file.

    -   “*MobiLipid_CCS-bias-calculation.Rmd*” can be used to calculate
        the CCS bias between measured CCS values and
        <sup>DT</sup>CCS<sub>N2</sub> library values of U<sup>13</sup>C
        labeled yeast lipids
    -   “*MobiLipid_CCS-bias-calculation_CCS-correction.Rmd*” to
        additionally perform a CCS correction based on linear correction
        functions using the <sup>DT</sup>CCS<sub>N2</sub> library.

2.  **Data import (measured data as .csv file):**

    This needs to be a .csv file containing the measured data. The .csv
    file has to have the following headers: “File”, “LipidClass”,
    “LipidSpecies”, “Adduct”, “Label”, “IMS_value”, “CCS” (an exemplary
    file is provided: “*Example_data_import.csv*”)

    -   **File**: File name of the measurement file
    -   **LipidClass**: Lipid class (ensure to use the same nomenclature
        as used for the <sup>DT</sup>CCS<sub>N2</sub> library)
    -   **LipidSpecies**: Lipid species on lipid species level (ensure
        to use the same nomenclature as used for the
        <sup>DT</sup>CCS<sub>N2</sub> library)
    -   **Adduct**: The following adducts are possible: \[M+H\],
        \[M+NH<sub>4</sub>\], \[M+Na\], \[M-H\], and \[M+HCOO\]. Not all
        adducts can be used for each lipid class. The table below shows
        the possible lipid class-adduct combinations:

    | Lipid class | Adduct                        |
    |:------------|:------------------------------|
    | AcCa        | \[M+H\], \[M-H\]              |
    | Cer         | \[M+H\], \[M+Na\], \[M+HCOO\] |
    | Co          | \[M+NH4\], \[M+Na\]           |
    | DG          | \[M+NH4\], \[M+Na\]           |
    | HexCer      | \[M+H\], \[M+Na\], \[M+HCOO\] |
    | LPC         | \[M+H\], \[M+Na\], \[M+HCOO\] |
    | LPE         | \[M+H\], \[M-H\]              |
    | PA          | \[M+NH4\], \[M+Na\], \[M-H\]  |
    | PC          | \[M+H\], \[M+Na\], \[M+HCOO\] |
    | PE          | \[M+H\], \[M+Na\], \[M-H\]    |
    | PG          | \[M+NH4\], \[M-H\]            |
    | PI          | \[M+NH4\], \[M+Na\], \[M-H\]  |
    | PS          | \[M+H\], \[M+Na\], \[M-H\]    |
    | SPH         | \[M+H\]                       |
    | TG          | \[M+NH4\], \[M+Na\]           |

    -   **Label**: “light” for natural lipids and “heavy” for
        U<sup>13</sup>C labeled lipids
    -   **IMS_value**: Measured mobility (e.g. 1/K<sub>0</sub> for TIMS)
    -   **CCS**: Measured CCS value

3.  **Import .csv file of U<sup>13</sup>C labeled lipid CCS library**:

    This needs to be a .csv file containing the
    <sup>DT</sup>CCS<sub>N2</sub> library. It is provided with the code
    and called “*U13C_DT_CCS_library.csv*”.

**Code to run MobiLipid:**

``` r
# Choose R markdown which should be processed
Rmd <- tcltk::tk_choose.files(caption = "Select R markdown (.Rmd file) which should be used for data processing:",
                              filters = matrix(c("Rmd files", "Rmd"), 1, 2),
                              multi = FALSE)
Rmd_name <- basename(Rmd)
Rmd_name <- gsub("\\.Rmd$", "", Rmd_name)

# Get file path where the markdown is saved to save results in the same folder
folder_path <- dirname(Rmd)

# Import path of data which should be processed
data_import <- tcltk::tk_choose.files(caption = "Data import (measured data as CSV file):", 
                                      filters = matrix(c("csv files", "csv"), 1, 2),
                                      multi = FALSE)
filename <- basename(data_import)
filename <- gsub("\\.csv$", "", filename)

# Run R markdown
rmarkdown::render(
  paste0(Rmd),
  output_format = c("html_document", "pdf_document"),
  output_file = c(paste0(Rmd_name,"_",filename,".html"), paste0(Rmd_name,"_",filename,".pdf")))
```

## Output

As output MobiLipid generates a .html and a.pdf file with all data.
Tables have to be accessed via the .html file (in the .pdf file only the
first 10 rows for all tables are displayed). Additionally, the following
.csv files are generated:

-   **CCS bias calculation:**

    Table with CCS bias (%) before correction (starting with:
    “CCS_bias_no_correction”)

-   **CCS bias calculation and correction:**

1.  Table with CCS bias (%) before correction (starting with:
    “CCS_bias_no_correction”)
2.  Table with all generated CCS correction functions (starting with:
    “Correction_functions”)
3.  Table with the mean CCS bias for each lipid class-adduct combination
    for each correction function (starting with:
    “CCS_bias_mean_by_function”)
4.  Table with the mean CCS bias for each lipid class-adduct combination
    over all correction functions with the same number of lipids used to
    generate the function (starting with: “CCS_bias_mean_all_functions”)
5.  Table with corrected CCS values and CCS bias after correction for
    each lipid and each correction function (starting with:
    “Corrected_CCS_values”)
6.  Table with mean corrected CCS values for each lipid and all
    correction functions with the same number of lipids used to generate
    the function (starting with: “Corrected_CCS_values_mean”)

## Citation

Please cite the following publication if you use MobiLipid in your
workfolw:

Neubauer, Stefan, Christina Haberhauer‐Troyer, Kristaps Klavins, Hannes
Russmayer, Matthias G. Steiger, Brigitte Gasser, Michael Sauer, Diethard
Mattanovich, Stephan Hann, and Gunda Koellensperger. 2012.
“<span class="smallcaps">U</span> <sup>13</sup>
<span class="smallcaps">C</span> Cell Extract of
<span class="smallcaps">P</span> Ichia Pastoris – a Powerful Tool for
Evaluation of Sample Preparation in Metabolomics.” *Journal of
Separation Science* 35 (22): 3091–3105.
<https://doi.org/10.1002/jssc.201200447>.
