# Introduction to Tidyverse

## Getting started
If you do not already have R, download it [here](https://cran.rstudio.com/) <br>
Download RStudio [here](https://posit.co/download/rstudio-desktop/)

A helpful (and free) resource is ["R for Data Science" (2nd edition)](https://r4ds.hadley.nz/)

R is a "programming environment for statistics and graphics". One of its strengths is that we can easily expand R's functionally by installing additional code and data (R packages).

We'll be working in RStudio, an integrated development environment (IDE) that among other things, has places to write and run code.
<p align="left">
  <img src="assets/tidyverse/Rstudio.png" width="100%">
</p>


### R projects
An R project keeps all the files associated with a project together (e.g., scripts, data, output figures). Projects have a project-specific folder with an .Rproj file that automatically launches RStudio, sets the working directory to the project folder, and reopens the environment to where you left off (including variables and any open files).

**_Task:_** Make a new project by following the steps below:
<p align="left">
  <img src="assets/tidyverse/Rstudio_Rproject_1.png" width="39%">
  <img src="assets/tidyverse/Rstudio_Rproject_2.png" width="40%">
  <img src="assets/tidyverse/Rstudio_Rproject_3.png" width="39%">
  <img src="assets/tidyverse/Rstudio_Rproject_4.png" width="40%">
</p>

Launch the project by going to the project directory and clicking on the .Rproj file. Your RStudio screen should look like this:
<p align="left">
  <img src="assets/tidyverse/Rstudio_Rproject_5.png" width="40%">
</p>


### R Notebooks
R Notebooks are a type of R Markdown file. From ["R for Data Science" (1st edition)](https://r4ds.had.co.nz/r-markdown.html):
<p align="left">
  <img src="assets/tidyverse/Rmarkdown_description.png" width="100%">
</p>

Basically, it's a convenient way to document and share your work.

**_Task:_** Create a new notebook:
<p align="left">
  <img src="assets/tidyverse/Rstudio_Rnotebook_1.png" width="60%">
</p>

Notebook structure:
<p align="left">
  <img src="assets/tidyverse/Rstudio_Rnotebook_2.png" width="100%">
</p>


Code chunks start with `` ```{r}  `` and end with `` ``` ``. To run a code chunk, press the green arrow at the right end of the chunk or place the cursor anywhere in the chunk and press Ctrl/Cmd + Shift + Enter. To run a specific line of code, place the cursor on the desired line and press Ctrl/Cmd + Enter.

**_Task:_** Run the default code chunk in your notebook. A graph should appear below the code chunk.


By default, an HTML version of the notebook is saved with the .Rmd notebook file.

**_Task:_**  View your notebook in HTML. Save the notebook and then open the project folder (hint: use the "Files" tab in the lower right of the RStudio screen). The folder should have a file with the same name as your notebook but ending in `nb.html`. Click on the nb.html file and it will open in a web browser.

Why do I care about an HTML version of my notebook? Because everyone can access a web browser. This makes it easy to share your results and furthermore, the notebook can be downloaded from the HTML file.
<p align="left">
  <img src="assets/tidyverse/Rnotebook_html.png" width="100%">
</p>

Text in an .Rmd file text is plain text formatted, however, we can write the text in [markdown format](https://www.markdownguide.org/basic-syntax/) which adds formatting (bold, italics, tables, links, etc.) when the .Rmd file is rendered in HTML.
<p align="left">
  <img src="assets/tidyverse/markdown.png" width="100%">
</p>


### R Packages
R packages are another way to share code and data. How a package is installed depends on where it is available.

First, is the package on CRAN (Comprehensive R Archive Network)?

**_Task:_**  Install the Tidyverse R package from CRAN. <br>
* To use the RStudio interface, in the lower right of the RStudio screen, click on the "Packages" tab, click on the "Install" tab, search for "Tidyverse", then click "Install"
<p align="left">
  <img src="assets/tidyverse/install.png" width="70%">
  <br>
  <img src="assets/tidyverse/install_tidyverse.png" width="50%">
</p>

* Alternatively, to install packages from the console, run:
```
install.packages("tidyverse")
```
<br>

Second, is the package not on CRAN but is on Github? This will require the `devtools` package which you may need to install first (hint: this package is on CRAN).

```
load(devtools)
install_github("DeveloperName/PackageName")
```

NB: When installing a package from the console, the package name must be in quotes. Once installed, the package can be called without quotes. 


Packages are installed once, but they need to be loaded prior to use. In an R Notebook, it is good practice to have the first code chunk be the one that loads all necessary packages.

**_Task:_** Load Tidyverse into your environment.
```
library(tidyverse)
```
<br>

Packages typically come with documentation. To learn more about a package, search the `help` tab in the lower right of the RStudio screen or run `?packageName` in the console.

___

## Tidy data with tidyr
The [Tidyverse](https://www.tidyverse.org/) is “an opinionated collection of R packages designed for data science. All packages share an underlying design philosophy, grammar, and data structures.”

Practically, it is a group of R packages for data manipulation, exploration, and visualization.

Key to the Tidyverse system is the tidy data format, where:

* Each variable has its own column.
* Each observation has its own row.
* Each value has its own cell.

<p align="left">
  <img src="assets/tidyverse/tidy_elements_1.png" width="100%">
  <img src="assets/tidyverse/tidy_elements_2.png" width="100%">
</p>


Let's read in some data and see what this means. Copy and paste the following into a code chunk and run the chunk:
<!--https://dcl-wrangle.stanford.edu/pivot-basic.html-->

```
eagle_nest_data <- data.frame(
  "region" = c("Pacific", "Southwest", "Rockies"),
  "2007" = c(1039, 51, 200),
  "2009" = c(2587, 176, 338),
  check.names=FALSE
)

print(eagle_nest_data)
```

![Untidy data](assets/tidyverse/eagle_untidy.png)

The variables in this dataset are region, year, and nests.

* Region is tidy because all of the data is in a single column. 
* Year is not tidy because the values (2007, 2009) are in two different columns.
* Nests is not tidy because the values are stored as values of 2007 and 2009.

Now we'll make the data tidy with the pivot_longer( ) function.

```
eagle_nest_data %>%             # Step 1 
  pivot_longer(
    cols = c(`2007`, `2009`),   # Step 2
    names_to = "year",          # Step 3
    values_to = "num_nests"     # Step 4
  )
```

* Step 1: `%>%` (magrittr) is a pipe that forwards a value into an expression or function. Think of it as saying "AND THEN".
* Step 2: Select the columns to pivot. We are going to take these columns and split the column names and data into two separate columns. 
* Step 3: Make a new column that will store the names of the selected columns.
* Step 4: Make a new column for the values of the selected columns.

![Methods](assets/tidyverse/eagle_methods.png)

![Tidy data](assets/tidyverse/eagle_tidy.png)

The table now has more rows but each variable (region, year, nests) has a column and each data point has its own row. 

<br>

`tidyr` is the Tidyverse package for wranging data into a tidy format. Two important tools are the `pivot_longer()` and `pivot_wider()` functions.

`pivot_longer()` moves data from column names to cell values.

`pivot_wider()` moves data from cell values into column names, creating a wider data frame.

<br>

**_Task:_** `relig_income` is a dataset that comes with `tidyr`. Make it tidy.
* First, view the first few rows of the data by running `head(relig_income)`.

* Second, complete the following code by filling in the quotation marks. Hint: Income is a variable. Are the income values column names or cell values? Also, `cols = -religion` means "all columns that are not named 'religion'".

```
relig_income %>%
  pivot_longer(cols = -religion, names_to = "   ", values_to = "   ")
```
<!--pivot_longer(cols = -religion, names_to = "income", values_to = "frequency")-->
___

## Data manipulation with dplyr

<p align="left">
  <img src="assets/tidyverse/penguins_logo.png" width="30%">
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;
  <img src="assets/tidyverse/penguins_ind.png" width="60%">
</p>

For some reason R logos are hexagon-shaped.

<br>

Now let's see what we can do with tidy data. Within the `palmerpenguins` R package is the `penguins` dataset, a collection of size measurements for adult foraging penguins near Palmer Station, Antarctica.

**_Task:_** Install and load the necessary data and analysis packages:
```
install.packages("palmerpenguins")
library(palmerpenguins)
library(tidyverse)
```

Run `names(penguins)` to see the column names. To learn more about the variables, run `?penguins`.

FYI
<p align="left">
  <img src="assets/tidyverse/penguin_beak.png" width="60%">
</p>


Let's answer the question "What is the mean body mass for female and male Adelie penguins on Dream island?"

```
penguins %>%
  select(species, island, body_mass_g, sex) %>%         # Step 1
  filter(species == "Adelie" & island == "Dream") %>%   # Step 2
  group_by(sex) %>%                                     # Step 3
  summarise(mean_body_mass = mean(body_mass_g)) %>%     # Step 4
  drop_na()                                             # Step 5
```

* Step 1: Select the desired variables (columns).
* Step 2: Filter the `species` data to only have Adelie and filter the `island` data to only include Dream island.
* Step 3: Group the `sex` data by categories (male and female).
* Step 4: Summarize the `body_mass_g` data which we already grouped by sex.
* Step 5: Drop NA values.

The output indicates that on Dream island, the mean body mass for Adelie penguins is 3344 grams for females and 4046 grams for males


`dplyr` is the Tidyverse package for data manipulation. All of the functions we used to answer our question about Adelie penguins came from this package.

___


## Data visualization with ggplot
 In terms of coding, ggplot has a highly systematic syntax. The ggplot() function is supplemented with other functions that control the objects and appearance of the plot. 
 
 Any figure made with ggplot has three elements:
 * Data
 * Aesthetics (`aes`)
 * Geometry (`geom`)
 
 <p align="left">
  <img src="assets/tidyverse/ggplot_overview.png" width="100%">
</p>

<br>

Let's visualize the relationship between `flipper_length_mm` and `body_mass_g`.

The first step is to call the ggplot() function and specify the dataset.
```
ggplot(data = penguins)
```

 Geometry refers to the geometric objects of a data visualization. They are the things that get drawn, e.g., lines, points, and bars. In ggplot the object determines the plot type, so for a line graph you call `geom_line`, for a scatter plot call `geom_point`.

```
ggplot(data = penguins) +
  geom_point()
```
 
 NB: Unlike Python, R does not care about code indentation.
 
 But geometric objects have attributes, e.g., color, size, position. These attributes can be linked to the data, e.g., a point size that is proportional to the number of individuals. The `aes()` function connects data to objects.

```
ggplot(data = penguins, aes(x = flipper_length_mm, y = body_mass_g)) +
  geom_point()
```

Here we specified which variables to use for our scatter plot. Note that aes() always goes inside another function.
 
 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_1.png" width="100%">
</p>

We know that the data observations include species; that's information that we can feature in the plot.

```
ggplot(data = penguins, aes(x = flipper_length_mm, y = body_mass_g)) +
  geom_point(aes(color = species, shape = species))
```

By default, a legend is created when objects are connected to data.

 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_2.png" width="100%">
</p>


These points are hard to see. Let's make them bigger and transparent. We'll add appearance information to geom_point(), however, because size and transparency are not related to data (here they are just settings), note that they are listed *outside* of the aes() function.

```
ggplot(data = penguins, aes(x = flipper_length_mm, y = body_mass_g)) +
  geom_point(aes(color = species, shape = species), size = 3, alpha = 0.8)
```

 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_3.png" width="100%">
</p>

Hmm, I don't like these colors. Let's change them.
```
ggplot(data = penguins, aes(x = flipper_length_mm, y = body_mass_g)) +
  geom_point(aes(color = species, shape = species), size = 3, alpha = 0.8) +
  scale_color_manual(values = c("darkorange","purple","cyan4"))
```
 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_4.png" width="100%">
</p>


And of course we'll need some labels. 
```
ggplot(data = penguins, aes(x = flipper_length_mm, y = body_mass_g)) +
  geom_point(aes(color = species, shape = species), size = 3, alpha = 0.8) +
  scale_color_manual(values = c("darkorange","purple","cyan4")) +
  labs(title = "Penguin size, Palmer Station LTER", 
    subtitle = "Flipper length and body mass for Adelie, Chinstrap and Gentoo Penguins", 
    x = "Flipper length (mm)",
    y = "Body mass (g)",
    color = "Penguin species",
    shape = "Penguin species")
  ```
 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_5.png" width="100%">
</p>


ggplot has [themes](https://ggplot2.tidyverse.org/reference/ggtheme.html) that control non-data plot aspects like background color, grid appearance, etc.

```
ggplot(data = penguins, aes(x = flipper_length_mm, y = body_mass_g)) +
  geom_point(aes(color = species, shape = species), size = 3, alpha = 0.8) +
  scale_color_manual(values = c("darkorange","purple","cyan4")) +
  labs(title = "Penguin size, Palmer Station", 
    subtitle = "Flipper length and body mass for Adelie, Chinstrap and Gentoo Penguins", 
    x = "Flipper length (mm)",
    y = "Body mass (g)",
    color = "Penguin species",
    shape = "Penguin species") +
  theme_minimal()
  ```
 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_6.png" width="100%">
</p>

Note that there are [many other ggplot functions](https://rstudio.github.io/cheatsheets/html/data-visualization.html) besides `geom()` and `aes()`. We called `scale_color_manual()` to change the point colors and `labs()` to add plot labels. Almost every aspect of a plot can be edited.

<br>

To demonstrate the flexibility of ggplot, let's make a different figure. Note how similar the code from the last plot is with this one:

```
species_count_data <- penguins %>% count(species)

ggplot(data = species_count_data, aes(x = species, y = n)) + 
  geom_col(aes(x = species, y = n, fill = species)) +
  geom_label(aes(x = species, y = n, label = n)) +
  scale_fill_manual(values = c("darkorange","purple","cyan4")) +
  labs(title = 'Penguins Species & Count') +
  theme_minimal()
  ```
 
 <p align="left">
  <img src="assets/tidyverse/ggplot_penguins_7.png" width="100%">
</p>