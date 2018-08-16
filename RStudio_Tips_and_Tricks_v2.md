RStudio Tips and Tricks
================

The purpose of this tutorial is to share some "hidden" tricks in RStudio that can improve coding efficiency and overall user experience.

### Customizing your Console

You can customize your RStudio setup and layout in the Global Options. You can control everything from the arrangement of your windows, to the font color, to the default working directory and more. Here are just a few examples of settings you can change that may improve the look of your RStudio sessions. Go into Tools -&gt; Global Options and start on the General tab. This is where you can change the default working directory, and control what shows up when you start a new session or saves at the end of a session.

![](docs/General%20Preferences.png)

<br> Under the next tab, Code, we can specify formatting styles we want to use. For example, wrap text helps make code more readable by automatically moving text to the next line when it reaches the edge of the window. This also where you can specify autocompletion functions that make coding faster.

![](docs/Code%20Preferences.png)

<br> The next tab, Appearance, is where you can change the color scheme of your script.

![](docs/Format%20Preferences.png)

<br> Click through some of the other tabs and familiarize yourself with some of the other things you can customize.

### Tools in RStudio

The history pane shows a searchable list of previously run commands. You can also access them in the console by hitting the `Up` arrow until you find it. The arrow key is good for code that was recently run, while the history pane is better for code that was run earlier in the session.

![](docs/History%20tab.png)

<br> Names of functions, arguments, objects, and files can be autofilled using tab. To autofill a function, begin typing the name (ie fun) and when you see a drop down box of functions hit tab. This is a snippet and it provides the skeleton for commonly used functions. To import a file, the tab button can help find the file. Begin typing the filepath and hit `Tab` to see a drop down list of files. Scroll through the options using the `Up` and `Down` arrows and hit `Enter` to select the file or folder you want.

![](docs/tab-autofill-file-search.png)

<br>
If you want to rename a variable, you can use Rename in Scope. Just highlight the name you want to change, go to Code -&gt; Rename in Scope and it will highlight all occurrences of that exact name so you can edit them all at once.

![](docs/Rename%20in%20Scope.png)

<br> Sections of code can be folded, or hid, to make it easier to navigate through the script and focus on one section at a time. Braced code (ie functions or loops), code chunks (RMarkdown style), text sections between headers within RMarkdown documents, and code sections are foldable. Sections can be defined in three ways:

``` r
# Style one ---------------------------

# Style two ===========================

### Style three #######################
```

You can use the Jump To menu at the bottom of the editor window or the outline view at top of the editor window. This shows the section names and you can quickly go to any one you choose.

![](docs/Jump%20to%20box.png)

### Keyboard Shortcuts

`Alt` + `-` Assignment arrow
`Ctrl` + `Alt` + `Shift` + `M` Highlights all occurrences of a word
`Ctrl` + `Alt` + `Shift` + `Arrow` Creates a second cursor that you can move to lines above or below original `Ctrl` + `Shift` + `R` Inserts new section
`Ctrl` + `Shift` + `m` Pipping symbol
`Ctrl` + `Shift` + `F` search for text in all files in a specified folder
`Ctrl` + `Shift` + `A` Reformats highlighted section to improve readability
`Ctrl` + `.` Go to file
`Ctrl` + `Shift` + `C` Comments or uncomments a line `Alt` + `Shift` + `Down Arrow` Copies entire line or mulitple lines (highlighted) below

For Macs, replace `Ctrl` with `Cmd`

We can practice the keyboard shortcuts we've learned with this code.

``` r
#Quick history
#Run this line then use the up arrow to change the code and rerun.
x <- seq(1,20,1)


#Try using tab autofill
#fun, tab:

#if tab:

#for tab:

#use tab to find a file to read in
x.df <- read.csv("")

#Rename in scope
x.df_1 <- x.df[,1]
x.df_2 <- x.df[,2]
x.df_3 <- x.df[,3]

#Reformat these lines to make them easier to read
dat <- list(A = x.df, B = x.df_1, C = x.df_2, D = x.df_3)
plot(x = dat$A[,2], y = dat$A[,3], xlim = c(30,75), xlab = "Husband's Age", ylab = "Wife's Age", pch = 16, col = "purple")

#Try using the piping function
library(dplyr)
data("iris")
head(iris)
iris ___ select(Species, Sepal.Length, Sepal.Width) ____ subset(Sepal.Length > 5)

#Use multiple cursors to change the ylims to be the same
X <- seq(1,10,1)
Y <- rnorm(10, 4, 4)
plot(x = X, y = Y, ylim = c(-1,15), pch = 16)
plot(x = X, y = Y, ylim = c(-1,7), pch = 16)
```

### Organizing Your Script

It is important to keep your scripts organized and annotated to help you and others remember and understand what lines of code are doing. Well organized scripts save time by making it easy to find what you need and prevent you from having to things repeatedly. Here are a few guidelines for writing nice script.

-   Use comments to explain what you are doing in a line or section.

-   Use sections for chunks of code. They can be run as a group, are searchable in the outline view, and can be hidden when not needed to make scrolling through the script eaiser.

-   Use descriptive names when naming scripts, nothing generic or vague like code\_01.R. This makes it much easier to go back and find later.

-   Put functions into individual scripts so you can easily source them later.

-   Keep everything organized and together by projects.

### How to Use .RProjects

RProjects are a tool to organize your work and make it completely self-contained and transportable. This is ideal for when you are collaborating with others and working on code together. When a project is created, a file is created that holds the files associated with that project, including temporary files and the environment. When you open a project, the working directory is set to the project directory, the .RHistory file is loaded, previously loaded scripts are in the editor window, and the global environment is loaded. One way to open a new project is to open RStudio and click the create new project button.

![](docs/New%20Project.png)

You can work with multiple projects at a time in different windows. Another way to create a project is with the package ProjectTemplate. It creates a project directory with pre-defined subfolders to help organize your materials.

#### Advantages of using ProjectTemplate package

When you load a project, it searches through the data directory for files and automatically loads any .csv or .sql files it finds. There is a libraries function that will automatically load the necessary libraries when you load the project. First you need to make sure in the 'config/global.dcf' file the 'load\_libraries: TRUE'. Then check the 'libraries' it will print out a list of the ones that will be loaded.

``` r
install.packages("ProjectTemplate")
library(ProjectTemplate)
create.project("SWT_RStudio")
setwd("./SWT_RStudio") #current working directory /SWT_RStudio
load.project()
```

For this example we will be using the mtcars dataset.

``` r
data("mtcars")
head(mtcars)
```

    ##                    mpg cyl disp  hp drat    wt  qsec vs am gear carb
    ## Mazda RX4         21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
    ## Mazda RX4 Wag     21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
    ## Datsun 710        22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
    ## Hornet 4 Drive    21.4   6  258 110 3.08 3.215 19.44  1  0    3    1
    ## Hornet Sportabout 18.7   8  360 175 3.15 3.440 17.02  0  0    3    2
    ## Valiant           18.1   6  225 105 2.76 3.460 20.22  1  0    3    1

``` r
save(mtcars, file = "./data/mtcars.csv")
```

We saved the dataframe in the data folder. Now when we load the project, it will automatically be read into our environment. Now any scripts that we write with code associated with this project can be saved in the src folder. You can also load libraries automatically when the project opens. First, open the config/global.dcf document in a texteditor and set load\_libraries to TRUE. If there are specific libraries you need for your analysis, you can add them to the next section, libraries.
Let's try creating a new dataframe of any three variables you want.

``` r
mtcars_sub <- mtcars[,c(1,2,4)]
head(mtcars_sub)
```

    ##                    mpg cyl  hp
    ## Mazda RX4         21.0   6 110
    ## Mazda RX4 Wag     21.0   6 110
    ## Datsun 710        22.8   4  93
    ## Hornet 4 Drive    21.4   6 110
    ## Hornet Sportabout 18.7   8 175
    ## Valiant           18.1   6 105

``` r
save(mtcars_sub, file = "./data/mtcars_sub.csv")
```

These tricks are small things that can make your coding life easier and more efficient. Projects are really great for work that you are sharing or collaborating on, big projects that involve multiple scripts and files, and a good way to practice organization.

###### This workshop was taught as part of the 2018 Student Workshop Training Series at USM GCRL.
