---
output:
  pdf_document: default
  html_document: default
---
# Character data: stringr {#stringr}

Resources:

- [Homepage](https://stringr.tidyverse.org/)
- [Book Chapter in R4DS](https://r4ds.had.co.nz/strings.html)
- [Cheatsheet](https://github.com/rstudio/cheatsheets/raw/master/strings.pdf)
- [Vignette Into](https://cran.r-project.org/web/packages/stringr/vignettes/stringr.html)
- [Vignette Regular Expressions](https://cran.r-project.org/web/packages/stringr/vignettes/regular-expressions.html)

Suggested data: recipes

Advice: Many of the examples in the Vignettes just refer to vectors. How can we use stringr to create/modify character columns of a data frame?







## Introduction

### Use of stringR in the R environment


StringR is a package that deals with measuring strings, search in strings and altering strings. StringR is thus mostly involved with data preprocessing, but it can also be used for text analysis (e.g. in text mining for determining the frequency of words). In the following, we will list most of the functions available in an encyclopedic style and describe their usage mostly within a single database, based on cooking recipes.

To give an overall orientation of the functions, we want to present a short overview over some of the most commonly used functions.

If you're interested in counting the words within a text, go to detect matches and especially str_detect. str_subset could be interesting, if you have a collection of documents and you want to have a subset of these documents dealing with a certain topic. You can therefore choose a list of words related to the topic. Keep in mind that topic modelling might be better. The mutate-functions str_sub or str_replace can replace substrings or patterns in a text. This might be useful, if a word or expression is repeatedly used wrong and needs to be changed. Join several strings in order to create a long text can be done using str_c whereas the opposite, the splitting of string can be performed with the str_split function. Another useful tool in stringr are the str_sort and str_order functions. As their name indicates, they sort or order strings according to your conditions. 

Probably the use cases in real-life are rather technical, but hopefully the uses cases above provide some clarity of why stringR might be useful.


### Types of data, which could be used with stringR

The way you use the functions of stringR is dependent on which kind of data you want to analyze. 

The simplest example is having one word as an entry in every box, e.g. the in the column "job", there might be an entry "cook". Here stringR is best for formatting the words. StringR can be used on number entries as well.

Regarding our example, there could also be more than one word per box, e.g. the tags for a computer game: "singleplayer, action, medieval". stringR can help formatting these multiple entries into a desired form.

Researching the package, we were under the impression that stringR is mostly designed for the purpose of managing and analyzing full texts or longer strings.

We want to point out that full text analysis as done in text mining, e.g. the novel The Time Machine by H.G. Wells, doesn't operate on a long text as a whole string. Instead, it'd rather tokenize the text first (Silge Robinson 2017). As a result every word is put in another row, as you can see here:



```
## # A tibble: 32,653 x 2
##    gutenberg_id word   
##           <int> <chr>  
##  1           35 the    
##  2           35 time   
##  3           35 machine
##  4           35 by     
##  5           35 h      
##  6           35 g      
##  7           35 wells  
##  8           35 1898   
##  9           35 i      
## 10           35 the    
## # ... with 32,643 more rows
```

This format will set restrictions on your ability to use the stringR functions, because stringR seems to be designed to operate on full texts and not on single worded strings. However, some stringR methods like str_detect are used frequently in text mining.




## Data Set for examples

As a data set for most of our examples we use the recipes data set, which contains recipes from various recipe websites from the USA. The dataset contains the recipe name, tags (such as "vegetarian"), the estimated time to cook it, the nutrition score, the ingredients, the steps needed to cook the recipe itself and a description of the recipe from the author.


## Regular Expressions


Regular expressions (regex) are used in almost every programming language. They are a way to normalize the syntax of strings. More importantly, they can not only be used to express a static word, but also to generally refer to defined group of words. For example, the set of words, which end on "r" contain the words "letter", "bear", "beer" etc. So, regular expressions are often used to match a set of words instead of a single word.


For regular expressions, the following holds:


* For the regular expression "a", you use the character "a"
* For all punctuation marks a backslash is put in front. E.g. "\\\?" is the regular expression for the question mark: "?". But to write "?" in R, you must use two backslashes: "\\\\?"
* For any digit, you can use the regular expression "\d" or also "[:digit:]". For word characters, you use "\w" or "[:alpha:]" as a regular expression. 
* If you have finite list of alternatives, you can use the regular expression for one of those, like "[abc]". So a word is just matched, if it either contains a, b or c.
* Another powerful regex might be "a[n]", which indicates, that you just want to have those strings, which contain a n times. "a[2]" would match the string "banana", because it contains two "n" s.
* You can group regular expressions with (). If you have the regex "(a|b)c", you have three groups. First "(a|b)", then "c" and the whole term as a group. Even if you have just set one pair of parenthesis. The intention of grouping a regex within a regex is to gain the possibility of calling each group by an assigned number. Moreover, some of the functions in stringr make use of the group structure, e.g. by providing a match for every group.


Regex provide almost limitless possibilities for Data Scientists and are - when handled professionally - a powerful language for describing pattern in strings. Since giving credit to every regex would require the space of an own chapter in this book, we'd like to redirect you to the stringr cheat sheet of stringr. Please visit http://edrub.in/CheatSheets/cheatSheetStringr.pdf for more information about regex.

Keep in mind that it might be difficult to find regular expressions, which define the members of the set you want to match. Take a look at the string "love". If you want to match every conjugation or word related to "love", like "loved", "lover" etc, you'll need a regular expression to define this set. If you use the regular expression love\\\\w" , most matches will be given back correctly. For example, when matching "loved", "loved" is given back. However, if matching "lovely" just "lovel" is given back, because you only have accounted for one additional character.

Always look out for caveats like this in regular expressions.



## Functions of stringR explained



### Manage lengths

#### str_length

str_length(string) shows the number of characters (!not words) in the given argument string. This includes both white spaces and punctuation marks.

This could be used to compare the length of two strings, especially if they have to have the same length. This could also be used to determine the frequency of characters in a string.


<u>Example</u>

To show that punctuation marks and white spaces are also counted, we use here a simple example.


```r
str_length("Hello, world!")
```

```
## [1] 13
```

In the above string there are 13 characters and 10 word characters. If you just want to get just the amount of word characters, you could subset the string by matching every character, which is a word character.



#### str_pad

str_pad(string, width, side = c("left", "right","both"), pad = " ") adds characters either to the left or right of a string (as a default white spaces). The argument width states how long the string should be after the adding of the additional characters. Both as a parameter for the side means that the added characters are split between the left and the right side of the string. With the pad argument any other character can be added.


The idea is to adjust strings of different length to the same length in order to match them in a subsequent step. E.g. when the comparison of the numbers 000247 with 247 should result in a match, we add 0s in front of the second number.


<u>Example</u>

In our dataset, the id consists sometimes of 5 or 6 digits, as you can see here:


```
##  [1] 144176 288190 174243 186363 231008 225599  70689 218370  90181 308152
```

We want to adjust all of the numbers to the same length. So, we add a zero in front to those 5-digit numbers.


```
##  [1] "144176" "288190" "174243" "186363" "231008" "225599" "070689" "218370"
##  [9] "090181" "308152"
```

As we can see, some ids have now a 0 in front. These were 5-digit numbers before. 

This works also, if id is of numeric type in r, but the type is then changed to character. As you can see here, the numbers above don't have quotation marks, while the ones below have some.


#### str_trunc

str_trunc(string, width, side = c("right", "left",
"center"), ellipsis = "...") shortens the string to the given argument width. The side, on which the string is cut off is determined by the side argument.

<u>Example</u>

We could use the function to show more text from the description column than can be seen in the view(recipes) tab, to get a better impression, of what these texts are about. But we might not need the full text. We just want the first 50 characters, so we cut off to the right.


```r
head(str_trunc(recipes$description, width = 50, side = "right"),5)
```

```
## [1] "from oregonlive.com.  i loved the beef lettuce ..."
## [2] "this is just a recipe i came up with when i was..."
## [3] "i just love this recipe. it's quick, easy to ma..."
## [4] "this is another of our family favorites that ma..."
## [5] "finally comfort food for us vegetarians!!  post..."
```

```r
recipes %>% mutate(description_short = str_trunc(description, width = 50, side = "right")) %>% select(name, description_short) %>% head()
```

```
## # A tibble: 6 x 2
##   name                                  description_short                       
##   <chr>                                 <chr>                                   
## 1 chinese spicy beef lettuce wraps      from oregonlive.com.  i loved the beef ~
## 2 peanut butter and dark fudge brownie~ this is just a recipe i came up with wh~
## 3 dorito casserole                      i just love this recipe. it's quick, ea~
## 4 pink lady squares                     this is another of our family favorites~
## 5 vegetable pot pie   pies              finally comfort food for us vegetarians~
## 6 artichoke gratinata                   this is a giada delaurentis recipe that~
```



#### str_trim

str_trim(string, side = c("both", "left", "right")) trims whitespaces from the start or end or both.

<u>Example</u>


```r
str_trim("     Hello, world!        ", side = "left")
```

```
## [1] "Hello, world!        "
```
As you can see, the whitespaces on the left are cut out, while the whitespaces on the right are still there.

For deleting all the white spaces in a larger string, you could also use a regular expression and match all not white spaces by using "\\S".



### Subset Strings


#### str_sub

str_sub(string, start=1L, end = -1L) can be used to extract substrings of a string. The first argument requires text and as 2nd and 3rd parameter the start and end index of the substring must be given. This can also be used to delete characters at the beginning and end of a string by extracting a version of the string without the beginning and end characters.


<u>Example</u>

In our data we have the nutrition given as a character string in the following format

```
## [1] "[267.4, 18.0, 15.0, 28.0, 42.0, 20.0, 5.0]"
```
This whole term is a string, which we want to split into a list. First, we have to delete the "[" in the beginning of the string and "]" at the end.

Here it is useful, that We can use the str_sub also to eliminate unwanted signs at the beginning and the end of a string.



```r
str_sub(recipes$nutrition[1], 2,str_length(recipes$nutrition[1])-1)
```

```
## [1] "267.4, 18.0, 15.0, 28.0, 42.0, 20.0, 5.0"
```

Then we can use the strsplit() function from base R to split the string into a list of strings by using ", " as the seperator. We don't have to care for the brackets additionally then.




#### str_subset

str_subset(string, pattern) gives back strings that contain a pattern match. The function looks for all strings with the argument pattern in it and returns all the fitting strings as a (possibly) smaller set of strings.

<u>Example</u>

To give an example, we look at the description of a recipe, which looks like the following:


```
## [1] "from oregonlive.com.  i loved the beef lettuce wraps from a restaurant in montauk, ny called east by northeast. this was the closest i have found to recreating it with a few minor adjustments from me."
```
Some of the descriptions contain the source of the recipe. So in this case, we want all the recipes, which originate from oregonlive.com


```r
str_subset(recipes$description, "oregonlive.com")
```

```
## [1] "from oregonlive.com.  i loved the beef lettuce wraps from a restaurant in montauk, ny called east by northeast. this was the closest i have found to recreating it with a few minor adjustments from me."                                                                                                                                                                                                                                                                                                                       
## [2] "this is a recipe in transition. it was made after a conversation with a friend. there is no place that i have been able to find a recipe for bacon lo mein - this was the closest i came on the internet. originally, i found this on oregonlive.com, tuesday, nov. 29, 2005 from philip jones, fort atkinson, wis. however, have tweaked it and now it's mine bad or good! here's to ya'.update: 01/22/2010 - to the reviewer who gave it a thumbs up - did you mean to omit the stars? after all - i got a glowing report! ;)"
```
As you can see, the set of strings has been reduced just to two strings. These are those, which contain "oregon.live"


#### str_extract

str_extract(string, pattern) returns the first pattern match found in each string as a vector. 

If you simply give in a word as an argument for pattern, then the first match is either the word itself or the function gives NA out, if there is no match. If you give in a set of strings, the result is a vector with just the two entries mentioned before. Since this just gives the information, if a string contains a word or not, the str_detect function would be more suitable for the task.

The function can also be used with a vector of strings as an argument for pattern. Then the function gives back the first string of the vector, which has been found in the string of the first argument, which shall be searched. str_detect would just indicate that one of the strings in the vector is true, but not which of them.


If you don't simply want the first match, then you can use also str.extract.all(). The output is a list of vectors.

<u>Example 1</u>


We look at the common words love and recipe in the description. The result shows, in which decriptions which word of these comes first or gives back NA if the pattern isn't found at all in the string. 



```r
head(str_extract(recipes$description, "(love\\w|recipe\\w)"),60)
```

```
##  [1] "loved"   NA        NA        NA        NA        NA        NA       
##  [8] NA        NA        NA        NA        "recipes" NA        NA       
## [15] NA        NA        NA        NA        NA        NA        "recipes"
## [22] NA        NA        NA        NA        NA        "recipes" NA       
## [29] NA        NA        NA        NA        NA        "loves"   NA       
## [36] NA        "recipel" NA        NA        NA        NA        NA       
## [43] NA        NA        NA        "recipes" NA        NA        NA       
## [50] NA        NA        NA        NA        "recipes" "lovel"   NA       
## [57] NA        NA        NA        NA
```
This example also shows, that you have to be careful with regular expressions. The 55rd entry "lovel" comes from "lovely". Maybe you would rather have "lovely" returned.



<u>Example 2</u>

One could also look at the ingredients and check, which recipes contain milk and eggs. Since we want to show that both are present in the dish or just one of these ingredients, we use str_extract_all


```r
head(str_extract_all(recipes$ingredients, "milk|eggs"),5)
```

```
## [[1]]
## character(0)
## 
## [[2]]
## [1] "eggs" "milk"
## 
## [[3]]
## character(0)
## 
## [[4]]
## [1] "milk"
## 
## [[5]]
## [1] "milk"
```


As you can see the second recipe contains both eggs and milk, the 4th and 5th recipe contain just milk, but no eggs.


#### str_match

str_match(string, pattern) returns the first pattern match found in each string, as a matrix with a column for each () group in pattern.

This is based on groups from regular expressions. Strings can be grouped via brackets (). Everything within this bracket belongs to the same group. E.g. in the regex (a|b)(c|d) the regular expression a|b is one group. because they're in the same bracket.

In this function if a match of a is detected, this is a match for group 1 and thus "a" is put into column 2. The first column is always reserved for the whole expression. E.g. if the string "ac" is found, there will be an entry "ac" in the first column, "a" in the second column, because this is the letter matched for the first group. In the third column "c" will be matched, because "c" belongs to the second group.

As with str_extract, here also exists a str_match_all function to get all the matches and not just the first.


<u>Example</u>

Here we want to match the regular expression (love\\w|recipe\\w).+(beef). This means "love" and "recipe" form one group and beef forms the other. The whole expression as such is also a group.


```r
head(str_match(recipes$description, "(love\\w|recipe\\w).+(beef)"),5)
```

```
##      [,1]             [,2]    [,3]  
## [1,] "loved the beef" "loved" "beef"
## [2,] NA               NA      NA    
## [3,] NA               NA      NA    
## [4,] NA               NA      NA    
## [5,] NA               NA      NA
```


As you can see, in the first column there is the entry beloging to the whole regular expression as a group. The string contains "love" as well as "beef". In the second column, the string matched from the first group is "love", in the third column the string matched from the second group is "beef".



### Detect matches

#### str_detect

str_detect(string, pattern) detects the presence of a pattern match in a string. The result is simply TRUE and FALSE


<u>Example</u>

Here we search for love and recipe in the description of every recipe. 


```r
head(str_detect(recipes$description, "(love\\w|recipe\\w)"),100)
```

```
##   [1]  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
##  [13] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE
##  [25] FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE FALSE FALSE
##  [37]  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE    NA  TRUE FALSE FALSE
##  [49] FALSE FALSE FALSE FALSE FALSE  TRUE  TRUE FALSE FALSE FALSE FALSE FALSE
##  [61] FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
##  [73] FALSE FALSE FALSE FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE FALSE
##  [85] FALSE FALSE FALSE    NA  TRUE FALSE FALSE FALSE FALSE FALSE FALSE  TRUE
##  [97] FALSE FALSE FALSE  TRUE
```
In contrast to extract, we just see, that one of the strings is in a certain string or not, but not which one of those.

#### str_which

str_which(string, pattern) finds the indices of strings that contain a pattern match. The output is a vector with these indices.


<u>Example</u>

Again we search for "love" and "recipe" in the description column.


```r
head(str_which(recipes$description, "(love\\w|recipe\\w)"),100)
```

```
##   [1]   1  12  21  27  34  37  46  54  55  77  89  96 100 115 125 126 127 146
##  [19] 152 154 166 169 174 180 191 192 193 200 224 227 234 239 246 276 282 286
##  [37] 302 309 311 325 333 337 345 346 352 354 367 389 392 402 405 410 417 427
##  [55] 431 435 440 442 457 463 465 473 483 487 497 509 516 518 522 523 538 550
##  [73] 555 560 564 573 575 596 604 611 612 620 639 649 666 670 677 695 712 724
##  [91] 725 732 734 746 768 770 779 780 788 798
```
In these rows there are descriptions, which contain either the word love or recipe.


#### str_count

str_count(string, pattern) counts the number of matches in a string.

<u>Example</u>

"i" occurs very often in the descriptions of the recipes. Let's see how often per description. As a regular expression we have chosen " i ", that is an i surrounded by white spaces. This means in sentences like "I ...." the i is not matched.


```r
head(str_count(recipes$description, " i "),100)
```

```
##   [1]  2  2  0  1  0  1  0  0  2  0  1  0  0  1  0  1  0  0  1  1  0  0  0  1  2
##  [26]  2  1  8  0  0  0  6  0  0  0  0  3  0  0  0  0  0  0  0 NA  0  0  0  0  2
##  [51]  2  0  1  4  0  2  2  0  1  0  1  0  1  0  0  1  0  1  0  2  0  0  0  0  2
##  [76]  0  2  0  1  1  2  0  0  0  1  0  0 NA  2  0  0  0  0  0  0  1  2  0  0  0
```

#### str_locate

str_locate(string, pattern) locates the positions of the first pattern match within a string.


```r
head(str_locate(recipes$description, " i "),10)
```

```
##       start end
##  [1,]    22  24
##  [2,]    22  24
##  [3,]    NA  NA
##  [4,]   175 177
##  [5,]    NA  NA
##  [6,]    40  42
##  [7,]    NA  NA
##  [8,]    NA  NA
##  [9,]   112 114
## [10,]    NA  NA
```
If you look up the first description string


```
## [1] "from oregonlive.com.  i loved the beef lettuce wraps from a restaurant in montauk, ny called east by northeast. this was the closest i have found to recreating it with a few minor adjustments from me."
```
Then you can see, that the first " i " is indeed from position 22 to 24. The length of 3 (24-22+1) come from 2 whitespaces and the i.


### Mutate Strings

In this part we will use Italy, a subset of "recipes" that is exclusively containing recipes mentioning "pizza".



#### str_replace (and replace_all) 

The replace functions of stringr are used to match patterns and replace them with new strings. 

<u>Example</u>

```r
Italy <- Italy %>% 
  mutate(name1 = str_replace(Italy$name, "pizza", "fluffy pizza"))
```


For seo (search engine optimization) we will add the popular word "fluffy" to all our pizza recipes in the name column.  
In order to replace several matches, use str_replace_all and create a vector containing multiple conditions.



```r
Italy <- Italy %>% 
  mutate(name2 = str_replace_all(Italy$name, c("pizza" = "fluffy pizza", "easy" = "easy peasy")))
```

##### str_to_lower

We can use str_to_ to change the case of text strings. They are intuitive: 
str_to_lower will change text to lower case, 
str_to_upper will change text to upper case,
str_to_title will change first letter of a string into a capital letter.

... 



<u>Example</u>

```r
p <- c("i","like","pizza","and","i don't share pizzza")
str_to_upper(p)
```

```
## [1] "I"                    "LIKE"                 "PIZZA"               
## [4] "AND"                  "I DON'T SHARE PIZZZA"
```

```r
str_to_sentence(p)
```

```
## [1] "I"                    "Like"                 "Pizza"               
## [4] "And"                  "I don't share pizzza"
```

```r
p <- str_c(p, collapse = " ") #make it one sentence (see str_c)
str_to_sentence(p)
```

```
## [1] "I like pizza and i don't share pizzza"
```

Attention: Locales will apply language conventions when changing the case. As you know, capitalization is used different in German and English. Above, the default would be locale = "en" but you can change it into every other language. The code for your language can be found in Wikipedia - List of ISO 639-1 codes.

Locales also come in handy for the str_order or str_sort fuction. 
str_sort will sort a list of string vectors by alphabet (locale will determine wich alphabet is applied)


```r
str_sort(Italy$name1[1:10], decreasing = FALSE, na_last = TRUE, locale = "en", numeric = FALSE)
```

```
##  [1] "fluffy pizza tomato sauce"                            
##  [2] "karen s slow cooker fluffy pizza chicken"             
##  [3] "macaroni fluffy pizza casserole"                      
##  [4] "mini fluffy pizza muffins"                            
##  [5] "parmesan spinach fluffy pizza"                        
##  [6] "pizzeria style sausage fluffy pizza"                  
##  [7] "quick bread fluffy pizza"                             
##  [8] "strawberry fluffy pizza"                              
##  [9] "three cheese fluffy pizza with truffle oil"           
## [10] "yeast free fluffy pizza with fresh basil and tomatoes"
```

### Join and Split

#### str_c

If you want to merge several strings into one, the str_c function is what you need. It is simple: either you join two or more single strings using the separate argument to choose a separation sign or you join a vector of strings by having the argument collapse control how the strings are separated. 

<u>Example</u>
In our Pizza example you can first see the join of the first few names into one string separated by ", " and then a vector that is joint into one string using the words "and then" as a form of separation. 


```r
str_c(Italy$name2[1],Italy$name2[2], Italy$name2[3], sep = ", ")
```

```
## [1] "three cheese fluffy pizza with truffle oil, karen s slow cooker fluffy pizza chicken, yeast free fluffy pizza with fresh basil and tomatoes"
```

```r
str_c(Italy$name2[1:3], collapse = " and then ")
```

```
## [1] "three cheese fluffy pizza with truffle oil and then karen s slow cooker fluffy pizza chicken and then yeast free fluffy pizza with fresh basil and tomatoes"
```

#### str_split

The str_split function will split a vector of strings into a list of substrings. The split is made where there is a match with a pattern of your choice.

<u>Example</u>
In our example we will split the column step containing step by step instructions into single substrings. As the steps currently have the format of a list, we can use the comma as a pattern to separate the strings. 


```r
Italy$steps %>% str_split("', '") %>% head(2)
```

```
## [[1]]
## [1] "['follow heating directions for crust"                  
## [2] "sprinkle mozzarella and parmesan cheese evenly on crust"
## [3] "dollop the ricotta cheese to your liking on top"        
## [4] "bake pizza until cheese get bubbly"                     
## [5] "drizzle truffle oil over cooked pizza"                  
## [6] "enjoy']"                                                
## 
## [[2]]
## [1] "['place chicken , onion , bell pepper and celery in a slow cooker"                                                                     
## [2] "in a medium bowl combine the tomato soup , cream of mushroom soup , tomato paste , water , parsley , oregano , basil , salt and pepper"
## [3] "mix well and pour mixture over chicken and vegetables in slow cooker"                                                                  
## [4] "stir to coat and add bay leaf"                                                                                                         
## [5] "cook on low setting for 8 hours , until chicken and vegetables are tender']"
```
If you'd rather have the subset of strings in a matrix, you can use str_split_fixed and choose n for the number of columns in the matrix. 

#### str_glue


<u>Example</u>

```r
pandas <- c("Carl","Jakob","Jakob","Markus","Robert")
str_glue("Hey {pandas}! I'd recommend the recipe '{str_to_title(Italy$name2[1:5])}' for you." )
```

```
## Hey Carl! I'd recommend the recipe 'Three Cheese Fluffy Pizza With Truffle Oil' for you.
## Hey Jakob! I'd recommend the recipe 'Karen S Slow Cooker Fluffy Pizza Chicken' for you.
## Hey Jakob! I'd recommend the recipe 'Yeast Free Fluffy Pizza With Fresh Basil And Tomatoes' for you.
## Hey Markus! I'd recommend the recipe 'Parmesan Spinach Fluffy Pizza' for you.
## Hey Robert! I'd recommend the recipe 'Macaroni Fluffy Pizza Casserole' for you.
```

As you can see, str_glue literally glues strings together to create a new string. Expressions must be written in {}, everything else is up to you tough: you can use data frames, lists and regex. Useful for customized mailing, distribution of tasks ect. 



## Literature

Silge, J., & Robinson, D. (2017). Text mining with R: A tidy approach. " O'Reilly Media, Inc.".