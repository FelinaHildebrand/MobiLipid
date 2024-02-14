# MobiLipid

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
where you have to choose the following files (be aware that these might
open behind the main window of R studio):

1.  **Select the R markdown which should be used for data processing.**
    This needs to be a .Rmd file. “MobiLipid_CCS-bias-calculation.Rmd”
    can be used to calculate the CCS bias betweent measured CCS values
    and <sup>DT</sup>CCS<sub>N2</sub> library values of U13C labeled
    yeast lipids or “MobiLipid_CCS-bias-calculation_CCS-correction.Rmd”
    to additionally perform a CCS correction based on linear correction
    functions using the <sup>DT</sup>CCS<sub>N2</sub> library.
2.  **Data import (measured data as .csv file)**. This needs to be a
    .csv file containing the measured data. The .csv file has to have
    the following headers: File, LipidClass, LipidSpecies, Adduct,
    Label, IMS_value, CCS (an examplary file es provided:
    “Example_data_import.csv”)
    -   **File**: File name of the measurement file
    -   **LipidClass**: Lipid class (ensure to use the same nomenclature
        as used for the DT^CCS<sub>N2</sub> library)
    -   **LipidSpecies**: Lipid species on lipid species level (ensure
        to use the same nomenclature as used for the DT^CCS<sub>N2</sub>
        library)
    -   **Adduct**: The following adducts are possible: \[M+H\],
        \[M+NH<sub>4</sub>\], \[M+Na\], \[M-H\], and \[M+HCOO\]. Not all
        adducts can be used for each lipid class. The table below shows
        the possible lipid class - adduct - combinations:

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
    -   **IMS_value**: Measured mobility (e.g. 1/k<sub>0</sub> for TIMS)
    -   **CCS**: Measured CCS value
3.  **Import .csv file of U<sup>13</sup>C labeled lipid CCS library**:
    This needs to be a .csv file containing the
    <sup>DT</sup>CCS<sub>N2</sub> library. It is provided with the code
    and called “U13C_DT_CCS_library.csv”.

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