+++
categories = ["r"]
coders = ["jrn1989"]
date = 2022-10-02T17:30:00Z
description = "An income model from socio-demographic variables"
github = ["https://github.com/jrn1989/income_model_r_version"]
image = "https://s3.us-east-2.amazonaws.com/portfolio.images.jrn/R_logo.png"
title = "An Income Model (R version)"
type = "post"
[[tech]]
logo = "https://s3.us-east-2.amazonaws.com/portfolio.images.jrn/R_logo.png"
name = "R"
url = "https://www.r-project.org/"
#[[tech]]
#logo = "https://res.cloudinary.com/samrobbins/image/upload/v1591793279/logos/logos_uikit_irc5gp.svg"
#name = "UIkit"
#url = "https://getuikit.com/"
#[[tech]]
#logo = "https://res.cloudinary.com/samrobbins/image/upload/q_auto/v1593603175/forestry-pos-full_lid6rs.svg"
#name = "Forestry CMS"
#url = "https://forestry.io/"

+++



## Introduction
<!--
When offering a credit to a new customer, we would like to do it at a minimal risk so getting to know the income becomes crucial, but sometimes this sensitive information is not available to us.

I remember back in the day when I was working at a financial institution, there was an income model that we used to select potential new customers and offer them a credit according their estimated income (among other business related rules that I won't discuss here). I never knew how this model came to be, but I gotta say it was way off. Some other models were proposed but they never took off, mostly because the data was hard to get or it was not interpretable.

Here I propose a model based on some socio-demographic variables at a household level from INEGI using linear regression and then we improve this model using expenditure information, which in a real case scenario would be helpful to adjust the credit of even offer a new one.

I remember back in the day when I was working at a financial institution, there was an income model that we used to select potential new customers and offer them a credit according their estimated income (among other business related rules that I won't discuss here). I never knew how this model came to be, but I gotta say it was way off. Some other models were proposed but they never took off, mostly because the data was hard to get or it was not interpretable.
-->

Back in the day I used to work at a financial institution where my main focus was to select potential new customers for credit cards joining many sources of information and applying business rules. One of this sources was an *income model*: when offering a credit card to a new customer we would like to do it at a minimal risk so getting to know the income becomes crucial, but sometimes this sensitive information is not available to us. Another source was socio-demographic information.

I never knew how this income model came to be, but I gotta say it was way off. Some other models were proposed but they never really took off mostly because they were not interpretable.

Here I propose a model based on some socio-demographic variables at a household level from INEGI using linear regression where we can interpret the resulting coefficients. Next we improve this model using expenditure information, which in a business scenario would be helpful to adjust the credit or to offer a new one.

## Data

The data comes from the **ENIGH**: Encuesta Nacional de Ingresos y Gastos de los Hogares 2020 (National Survey of Household Income and Expenses 2020) taken from INEGI^[The National Institute of Statistics and Geography is an autonomous agency of the Mexican Government dedicated to coordinate the National System of Statistical and Geographical Information of Mexico]


The data will be taken from four .csv files:

**1. conjunto_de_datos_concentradohogar_enigh_2020_ns.csv**

Household variables:
<!--folioviv, tam_loc, est_socio, clase_hog, sexo_jefe, edad_jefe, tot_integ, estim_alqu-->

* folioviv: Household identifier

* tam_loc: Population size in which the household is located
  + 1 - 100,000 or more population
  + 2 - 15,000 to 99,999 population
  + 3 - 2,500 to 14,999 population
  + 4 - 2,500 or less population

* est_socio: Socioeconomic status
  + 1 - Low
  + 2 - Medium-low
  + 3 - Medium-high
  + 4 - High

* clase_hog: Type of home
  + 1 - Unipersonal
  + 2 - Nuclear
  + 3 - Extended
  + 4 - Composite  
  + 5 - Co-resident

* sexo_jefe: Head of family gender
  + 1 - Male
  + 2 - Family

* edad_jefe: Head of family age

* tot_integ: Total number of members

* estim_alqu: Rent estimate

**2. conjunto_de_datos_hogares_enigh_2020_ns.csv**

<!--folioviv, acc_alim2, alim17_2, alim17_3, alim17_4, alim17_5, alim17_6, alim17_7, alim17_8, alim17_9, alim17_11,
                telefono, celular, tv_paga, conex_inte-->

Household characteristics

* folioviv: Household identifier

* acc_alim2: No food
  + 1 - Yes
  + 2 - No

* alim17_2: Days in which tubers were eaten

* alim17_3: Days in which vegetables were eaten

* alim17_4: Days in which fruits were eaten

* alim17_5: Days in which meat was eaten

* alim17_6: Days in which egg was eaten

* alim17_7: Days in which fish was eaten

* alim17_8: Days in which legumes were eaten

* alim17_9: Days in which dairy products were eaten

* alim17_11: Days in which sugar was eaten

* telefono: It has landline
  + 1 - Yes
  + 2 - No

* cellphone: It has cellphone
  + 1 - Yes
  + 2 - No

* tv_paga: It has cable
  + 1 - Yes
  + 2 - No

* conex_inte: It has internet
  + 1 - Yes
  + 2 - No

**3. conjunto_de_datos_gastoshogar_enigh_2020_ns.csv**

<!--folioviv, lugar_comp, gasto_tri-->

Household expenditure.

* folioviv: Household identifier

* lugar_comp: Type of establishment
  + 0 - Non-applicable
  + 1 - Market
  + 2 - Flea market
  + 3 - Street vendor
  + 4 - Grocery store
  + 5 - Specialty store
  + 6 - Supermarkets
  + 7 - Department store
  + 8 - Purchases outside the country
  + 9 - Wholesale club
  + 10 - Corner store
  + 11 - Restaurants
  + 12 - Other food places
  + 13 - Coffee shops
  + 14 - Bars
  + 15 - Diconsa
  + 16 - Liconsa store
  + 17 - Individual sale
  + 18 - Internet

* gasto_tri: Quartely expenditure


**4. conjunto_de_datos_ingresos_enigh_2020_ns.csv**

Income of each member in the household.

* folioviv: Household identifier

* ing_tri: Quaterly income

## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


```r
summary(cars)
```

```
##      speed           dist       
##  Min.   : 4.0   Min.   :  2.00  
##  1st Qu.:12.0   1st Qu.: 26.00  
##  Median :15.0   Median : 36.00  
##  Mean   :15.4   Mean   : 42.98  
##  3rd Qu.:19.0   3rd Qu.: 56.00  
##  Max.   :25.0   Max.   :120.00
```

## Including Plots

You can also embed plots, for example:

![](example_files/figure-html/pressure-1.png)<!-- -->

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
