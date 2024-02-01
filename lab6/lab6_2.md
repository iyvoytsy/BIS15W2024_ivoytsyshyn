---
title: "dplyr Superhero"
date: "2024-02-01"
output:
  html_document: 
    theme: spacelab
    toc: true
    toc_float: true
    keep_md: true
---

## Learning Goals  
*At the end of this exercise, you will be able to:*    
1. Develop your dplyr superpowers so you can easily and confidently manipulate dataframes.  
2. Learn helpful new functions that are part of the `janitor` package.  

## Instructions
For the second part of lab today, we are going to spend time practicing the dplyr functions we have learned and add a few new ones. This lab doubles as your homework. Please complete the lab and push your final code to GitHub.  

## Load the libraries

```r
library("tidyverse")
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.4.4     ✔ tibble    3.2.1
## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
## 
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- read_csv("data/heroes_information.csv", na = c("", "-99", "-"))
```

```
## Rows: 734 Columns: 10
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (8): name, Gender, Eye color, Race, Hair color, Publisher, Skin color, A...
## dbl (2): Height, Weight
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

```r
superhero_powers <- read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## Rows: 667 Columns: 168
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr   (1): hero_names
## lgl (167): Agility, Accelerated Healing, Lantern Power Ring, Dimensional Awa...
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here. Before you do anything, first have a look at the names of the variables. You can use `rename()` or `clean_names()`.    


```r
superhero <- clean_names(superhero_info)
```

## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data.  

```r
tabyl(superhero, alignment)
```

```
##  alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

1. Who are the publishers of the superheros? Show the proportion of superheros from each publisher. Which publisher has the highest number of superheros?  

```r
publisher_info <- superhero %>%
  select(publisher, name) %>%
  arrange(publisher)
table(publisher_info$publisher)
```

```
## 
##       ABC Studios Dark Horse Comics         DC Comics      George Lucas 
##                 4                18               215                14 
##     Hanna-Barbera     HarperCollins       Icon Comics    IDW Publishing 
##                 1                 6                 4                 4 
##      Image Comics     J. K. Rowling  J. R. R. Tolkien     Marvel Comics 
##                14                 1                 1               388 
##         Microsoft      NBC - Heroes         Rebellion          Shueisha 
##                 1                19                 1                 4 
##     Sony Pictures        South Park         Star Trek              SyFy 
##                 2                 1                 6                 5 
##      Team Epic TV       Titan Books Universal Studios         Wildstorm 
##                 5                 1                 1                 3
```

2. Notice that we have some neutral superheros! Who are they? List their names below.  


```r
filter(superhero, alignment == "neutral")
```

```
## # A tibble: 24 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Biza… Male   black     Biza… Black         191 DC Comics white      neutral  
##  2 Blac… Male   <NA>      God … <NA>           NA DC Comics <NA>       neutral  
##  3 Capt… Male   brown     Human Brown          NA DC Comics <NA>       neutral  
##  4 Copy… Female red       Muta… White         183 Marvel C… blue       neutral  
##  5 Dead… Male   brown     Muta… No Hair       188 Marvel C… <NA>       neutral  
##  6 Deat… Male   blue      Human White         193 DC Comics <NA>       neutral  
##  7 Etri… Male   red       Demon No Hair       193 DC Comics yellow     neutral  
##  8 Gala… Male   black     Cosm… Black         876 Marvel C… <NA>       neutral  
##  9 Glad… Male   blue      Stro… Blue          198 Marvel C… purple     neutral  
## 10 Indi… Female <NA>      Alien Purple         NA DC Comics <NA>       neutral  
## # ℹ 14 more rows
## # ℹ 1 more variable: weight <dbl>
```


## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?


```r
select(superhero, "name", "alignment", "race")
```

```
## # A tibble: 734 × 3
##    name          alignment race             
##    <chr>         <chr>     <chr>            
##  1 A-Bomb        good      Human            
##  2 Abe Sapien    good      Icthyo Sapien    
##  3 Abin Sur      good      Ungaran          
##  4 Abomination   bad       Human / Radiation
##  5 Abraxas       bad       Cosmic Entity    
##  6 Absorbing Man bad       Human            
##  7 Adam Monroe   good      <NA>             
##  8 Adam Strange  good      Human            
##  9 Agent 13      good      <NA>             
## 10 Agent Bob     good      Human            
## # ℹ 724 more rows
```


## Not Human
4. List all of the superheros that are not human.


```r
filter(superhero, race != "Human")
```

```
## # A tibble: 222 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abe … Male   blue      Icth… No Hair       191 Dark Hor… blue       good     
##  2 Abin… Male   blue      Unga… No Hair       185 DC Comics red        good     
##  3 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  4 Abra… Male   blue      Cosm… Black          NA Marvel C… <NA>       bad      
##  5 Ajax  Male   brown     Cybo… Black         193 Marvel C… <NA>       bad      
##  6 Alien Male   <NA>      Xeno… No Hair       244 Dark Hor… black      bad      
##  7 Amazo Male   red       Andr… <NA>          257 DC Comics <NA>       bad      
##  8 Angel Male   <NA>      Vamp… <NA>           NA Dark Hor… <NA>       good     
##  9 Ange… Female yellow    Muta… Black         165 Marvel C… <NA>       good     
## 10 Anti… Male   yellow    God … No Hair        61 DC Comics <NA>       bad      
## # ℹ 212 more rows
## # ℹ 1 more variable: weight <dbl>
```


## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".


```r
good_guys <- filter(superhero, alignment == "good")
bad_guys <- filter(superhero, alignment == "bad")
```


6. For the good guys, use the `tabyl` function to summarize their "race".

```r
tabyl(good_guys$race)
```

```
##     good_guys$race   n     percent valid_percent
##              Alien   3 0.006048387   0.010752688
##              Alpha   5 0.010080645   0.017921147
##             Amazon   2 0.004032258   0.007168459
##            Android   4 0.008064516   0.014336918
##             Animal   2 0.004032258   0.007168459
##          Asgardian   3 0.006048387   0.010752688
##          Atlantean   4 0.008064516   0.014336918
##         Bolovaxian   1 0.002016129   0.003584229
##              Clone   1 0.002016129   0.003584229
##             Cyborg   3 0.006048387   0.010752688
##           Demi-God   2 0.004032258   0.007168459
##              Demon   3 0.006048387   0.010752688
##            Eternal   1 0.002016129   0.003584229
##     Flora Colossus   1 0.002016129   0.003584229
##        Frost Giant   1 0.002016129   0.003584229
##      God / Eternal   6 0.012096774   0.021505376
##             Gungan   1 0.002016129   0.003584229
##              Human 148 0.298387097   0.530465950
##    Human / Altered   2 0.004032258   0.007168459
##     Human / Cosmic   2 0.004032258   0.007168459
##  Human / Radiation   8 0.016129032   0.028673835
##         Human-Kree   2 0.004032258   0.007168459
##      Human-Spartoi   1 0.002016129   0.003584229
##       Human-Vulcan   1 0.002016129   0.003584229
##    Human-Vuldarian   1 0.002016129   0.003584229
##      Icthyo Sapien   1 0.002016129   0.003584229
##            Inhuman   4 0.008064516   0.014336918
##    Kakarantharaian   1 0.002016129   0.003584229
##         Kryptonian   4 0.008064516   0.014336918
##            Martian   1 0.002016129   0.003584229
##          Metahuman   1 0.002016129   0.003584229
##             Mutant  46 0.092741935   0.164874552
##     Mutant / Clone   1 0.002016129   0.003584229
##             Planet   1 0.002016129   0.003584229
##             Saiyan   1 0.002016129   0.003584229
##           Symbiote   3 0.006048387   0.010752688
##           Talokite   1 0.002016129   0.003584229
##         Tamaranean   1 0.002016129   0.003584229
##            Ungaran   1 0.002016129   0.003584229
##            Vampire   2 0.004032258   0.007168459
##     Yoda's species   1 0.002016129   0.003584229
##      Zen-Whoberian   1 0.002016129   0.003584229
##               <NA> 217 0.437500000            NA
```


7. Among the good guys, Who are the Vampires?

```r
filter(good_guys, race == "Vampire")
```

```
## # A tibble: 2 × 10
##   name  gender eye_color race   hair_color height publisher skin_color alignment
##   <chr> <chr>  <chr>     <chr>  <chr>       <dbl> <chr>     <chr>      <chr>    
## 1 Angel Male   <NA>      Vampi… <NA>           NA Dark Hor… <NA>       good     
## 2 Blade Male   brown     Vampi… Black         188 Marvel C… <NA>       good     
## # ℹ 1 more variable: weight <dbl>
```


8. Among the bad guys, who are the male humans over 200 inches in height?


```r
filter(bad_guys, gender == "Male", height > 200)
```

```
## # A tibble: 22 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Abom… Male   green     Huma… No Hair       203 Marvel C… <NA>       bad      
##  2 Alien Male   <NA>      Xeno… No Hair       244 Dark Hor… black      bad      
##  3 Amazo Male   red       Andr… <NA>          257 DC Comics <NA>       bad      
##  4 Apoc… Male   red       Muta… Black         213 Marvel C… grey       bad      
##  5 Bane  Male   <NA>      Human <NA>          203 DC Comics <NA>       bad      
##  6 Dark… Male   red       New … No Hair       267 DC Comics grey       bad      
##  7 Doct… Male   brown     Human Brown         201 Marvel C… <NA>       bad      
##  8 Doct… Male   brown     <NA>  Brown         201 Marvel C… <NA>       bad      
##  9 Doom… Male   red       Alien White         244 DC Comics <NA>       bad      
## 10 Kill… Male   red       Meta… No Hair       244 DC Comics green      bad      
## # ℹ 12 more rows
## # ℹ 1 more variable: weight <dbl>
```


9. Are there more good guys or bad guys with green hair?


```r
good_guys %>%
  filter(hair_color == "Green") %>%
  nrow()
```

```
## [1] 7
```

```r
bad_guys %>%
  filter(hair_color == "Green") %>%
  nrow()
```

```
## [1] 1
```



10. Let's explore who the really small superheros are. In the `superhero_info` data, which have a weight less than 50? Be sure to sort your results by weight lowest to highest.  


```r
small_hero <- superhero %>%
  filter(weight <= 50) %>%
  arrange(weight)
small_hero
```

```
## # A tibble: 31 × 10
##    name  gender eye_color race  hair_color height publisher skin_color alignment
##    <chr> <chr>  <chr>     <chr> <chr>       <dbl> <chr>     <chr>      <chr>    
##  1 Iron… Male   blue      <NA>  No Hair        NA Marvel C… <NA>       bad      
##  2 Groot Male   yellow    Flor… <NA>          701 Marvel C… <NA>       good     
##  3 Jack… Male   blue      Human Brown          71 Dark Hor… <NA>       good     
##  4 Gala… Male   black     Cosm… Black         876 Marvel C… <NA>       neutral  
##  5 Yoda  Male   brown     Yoda… White          66 George L… green      good     
##  6 Fin … Male   red       Kaka… No Hair       975 Marvel C… green      good     
##  7 Howa… Male   brown     <NA>  Yellow         79 Marvel C… <NA>       good     
##  8 Kryp… Male   blue      Kryp… White          64 DC Comics <NA>       good     
##  9 Rock… Male   brown     Anim… Brown         122 Marvel C… <NA>       good     
## 10 Dash  Male   blue      Human Blond         122 Dark Hor… <NA>       good     
## # ℹ 21 more rows
## # ℹ 1 more variable: weight <dbl>
```

11. Let's make a new variable that is the ratio of height to weight. Call this variable `height_weight_ratio`.    


```r
height_weight_ratio <- superhero %>%
  select(name, height, weight) %>%
  mutate(height_weight_ratio = height / weight) %>%
  arrange(desc(height_weight_ratio)) %>%
  slice(1)
height_weight_ratio
```

```
## # A tibble: 1 × 4
##   name  height weight height_weight_ratio
##   <chr>  <dbl>  <dbl>               <dbl>
## 1 Groot    701      4                175.
```

12. Who has the highest height to weight ratio?  


```r
height_weight_ratio
```

```
## # A tibble: 1 × 4
##   name  height weight height_weight_ratio
##   <chr>  <dbl>  <dbl>               <dbl>
## 1 Groot    701      4                175.
```

## `superhero_powers`
Have a quick look at the `superhero_powers` data frame.  


```r
summary(superhero_powers)
```

```
##   hero_names         Agility        Accelerated Healing Lantern Power Ring
##  Length:667         Mode :logical   Mode :logical       Mode :logical     
##  Class :character   FALSE:425       FALSE:489           FALSE:656         
##  Mode  :character   TRUE :242       TRUE :178           TRUE :11          
##  Dimensional Awareness Cold Resistance Durability       Stealth       
##  Mode :logical         Mode :logical   Mode :logical   Mode :logical  
##  FALSE:642             FALSE:620       FALSE:410       FALSE:541      
##  TRUE :25              TRUE :47        TRUE :257       TRUE :126      
##  Energy Absorption   Flight        Danger Sense    Underwater breathing
##  Mode :logical     Mode :logical   Mode :logical   Mode :logical       
##  FALSE:590         FALSE:455       FALSE:637       FALSE:646           
##  TRUE :77          TRUE :212       TRUE :30        TRUE :21            
##  Marksmanship    Weapons Master  Power Augmentation Animal Attributes
##  Mode :logical   Mode :logical   Mode :logical      Mode :logical    
##  FALSE:548       FALSE:562       FALSE:659          FALSE:642        
##  TRUE :119       TRUE :105       TRUE :8            TRUE :25         
##  Longevity       Intelligence    Super Strength  Cryokinesis    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:554       FALSE:509       FALSE:307       FALSE:648      
##  TRUE :113       TRUE :158       TRUE :360       TRUE :19       
##  Telepathy       Energy Armor    Energy Blasts   Duplication    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:575       FALSE:659       FALSE:520       FALSE:651      
##  TRUE :92        TRUE :8         TRUE :147       TRUE :16       
##  Size Changing   Density Control  Stamina        Astral Travel  
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:612       FALSE:652       FALSE:378       FALSE:663      
##  TRUE :55        TRUE :15        TRUE :289       TRUE :4        
##  Audio Control   Dexterity        Omnitrix       Super Speed    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:660       FALSE:661       FALSE:666       FALSE:418      
##  TRUE :7         TRUE :6         TRUE :1         TRUE :249      
##  Possession      Animal Oriented Powers Weapon-based Powers Electrokinesis 
##  Mode :logical   Mode :logical          Mode :logical       Mode :logical  
##  FALSE:659       FALSE:627              FALSE:609           FALSE:645      
##  TRUE :8         TRUE :40               TRUE :58            TRUE :22       
##  Darkforce Manipulation Death Touch     Teleportation   Enhanced Senses
##  Mode :logical          Mode :logical   Mode :logical   Mode :logical  
##  FALSE:657              FALSE:660       FALSE:595       FALSE:578      
##  TRUE :10               TRUE :7         TRUE :72        TRUE :89       
##  Telekinesis     Energy Beams      Magic         Hyperkinesis   
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:606       FALSE:625       FALSE:623       FALSE:666      
##  TRUE :61        TRUE :42        TRUE :44        TRUE :1        
##     Jump         Clairvoyance    Dimensional Travel Power Sense    
##  Mode :logical   Mode :logical   Mode :logical      Mode :logical  
##  FALSE:602       FALSE:663       FALSE:644          FALSE:664      
##  TRUE :65        TRUE :4         TRUE :23           TRUE :3        
##  Shapeshifting   Peak Human Condition Immortality     Camouflage     
##  Mode :logical   Mode :logical        Mode :logical   Mode :logical  
##  FALSE:601       FALSE:637            FALSE:598       FALSE:646      
##  TRUE :66        TRUE :30             TRUE :69        TRUE :21       
##  Element Control  Phasing        Astral Projection Electrical Transport
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical       
##  FALSE:659       FALSE:636       FALSE:638         FALSE:666           
##  TRUE :8         TRUE :31        TRUE :29          TRUE :1             
##  Fire Control    Projection      Summoning       Enhanced Memory
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:635       FALSE:665       FALSE:663       FALSE:642      
##  TRUE :32        TRUE :2         TRUE :4         TRUE :25       
##   Reflexes       Invulnerability Energy Constructs Force Fields   
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical  
##  FALSE:503       FALSE:550       FALSE:629         FALSE:581      
##  TRUE :164       TRUE :117       TRUE :38          TRUE :86       
##  Self-Sustenance Anti-Gravity     Empathy        Power Nullifier
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:630       FALSE:666       FALSE:648       FALSE:663      
##  TRUE :37        TRUE :1         TRUE :19        TRUE :4        
##  Radiation Control Psionic Powers  Elasticity      Substance Secretion
##  Mode :logical     Mode :logical   Mode :logical   Mode :logical      
##  FALSE:660         FALSE:618       FALSE:656       FALSE:650          
##  TRUE :7           TRUE :49        TRUE :11        TRUE :17           
##  Elemental Transmogrification Technopath/Cyberpath Photographic Reflexes
##  Mode :logical                Mode :logical        Mode :logical        
##  FALSE:661                    FALSE:644            FALSE:664            
##  TRUE :6                      TRUE :23             TRUE :3              
##  Seismic Power   Animation       Precognition    Mind Control   
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:664       FALSE:662       FALSE:645       FALSE:645      
##  TRUE :3         TRUE :5         TRUE :22        TRUE :22       
##  Fire Resistance Power Absorption Enhanced Hearing Nova Force     
##  Mode :logical   Mode :logical    Mode :logical    Mode :logical  
##  FALSE:649       FALSE:655        FALSE:595        FALSE:665      
##  TRUE :18        TRUE :12         TRUE :72         TRUE :2        
##   Insanity       Hypnokinesis    Animal Control  Natural Armor  
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:662       FALSE:644       FALSE:658       FALSE:631      
##  TRUE :5         TRUE :23        TRUE :9         TRUE :36       
##  Intangibility   Enhanced Sight  Molecular Manipulation Heat Generation
##  Mode :logical   Mode :logical   Mode :logical          Mode :logical  
##  FALSE:647       FALSE:642       FALSE:625              FALSE:643      
##  TRUE :20        TRUE :25        TRUE :42               TRUE :24       
##  Adaptation       Gliding        Power Suit      Mind Blast     
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:662       FALSE:657       FALSE:634       FALSE:655      
##  TRUE :5         TRUE :10        TRUE :33        TRUE :12       
##  Probability Manipulation Gravity Control Regeneration    Light Control  
##  Mode :logical            Mode :logical   Mode :logical   Mode :logical  
##  FALSE:658                FALSE:651       FALSE:639       FALSE:652      
##  TRUE :9                  TRUE :16        TRUE :28        TRUE :15       
##  Echolocation    Levitation      Toxin and Disease Control   Banish       
##  Mode :logical   Mode :logical   Mode :logical             Mode :logical  
##  FALSE:665       FALSE:641       FALSE:657                 FALSE:666      
##  TRUE :2         TRUE :26        TRUE :10                  TRUE :1        
##  Energy Manipulation Heat Resistance Natural Weapons Time Travel    
##  Mode :logical       Mode :logical   Mode :logical   Mode :logical  
##  FALSE:615           FALSE:624       FALSE:609       FALSE:634      
##  TRUE :52            TRUE :43        TRUE :58        TRUE :33       
##  Enhanced Smell  Illusions       Thirstokinesis  Hair Manipulation
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical    
##  FALSE:635       FALSE:629       FALSE:666       FALSE:666        
##  TRUE :32        TRUE :38        TRUE :1         TRUE :1          
##  Illumination    Omnipotent       Cloaking       Changing Armor 
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:665       FALSE:660       FALSE:660       FALSE:666      
##  TRUE :2         TRUE :7         TRUE :7         TRUE :1        
##  Power Cosmic    Biokinesis      Water Control   Radiation Immunity
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical     
##  FALSE:660       FALSE:666       FALSE:654       FALSE:657         
##  TRUE :7         TRUE :1         TRUE :13        TRUE :10          
##  Vision - Telescopic Toxin and Disease Resistance Spatial Awareness
##  Mode :logical       Mode :logical                Mode :logical    
##  FALSE:624           FALSE:619                    FALSE:666        
##  TRUE :43            TRUE :48                     TRUE :1          
##  Energy Resistance Telepathy Resistance Molecular Combustion Omnilingualism 
##  Mode :logical     Mode :logical        Mode :logical        Mode :logical  
##  FALSE:660         FALSE:634            FALSE:665            FALSE:646      
##  TRUE :7           TRUE :33             TRUE :2              TRUE :21       
##  Portal Creation Magnetism       Mind Control Resistance Plant Control  
##  Mode :logical   Mode :logical   Mode :logical           Mode :logical  
##  FALSE:663       FALSE:656       FALSE:655               FALSE:659      
##  TRUE :4         TRUE :11        TRUE :12                TRUE :8        
##    Sonar         Sonic Scream    Time Manipulation Enhanced Touch 
##  Mode :logical   Mode :logical   Mode :logical     Mode :logical  
##  FALSE:663       FALSE:661       FALSE:647         FALSE:660      
##  TRUE :4         TRUE :6         TRUE :20          TRUE :7        
##  Magic Resistance Invisibility    Sub-Mariner     Radiation Absorption
##  Mode :logical    Mode :logical   Mode :logical   Mode :logical       
##  FALSE:661        FALSE:645       FALSE:647       FALSE:660           
##  TRUE :6          TRUE :22        TRUE :20        TRUE :7             
##  Intuitive aptitude Vision - Microscopic  Melting        Wind Control   
##  Mode :logical      Mode :logical        Mode :logical   Mode :logical  
##  FALSE:666          FALSE:648            FALSE:665       FALSE:664      
##  TRUE :1            TRUE :19             TRUE :2         TRUE :3        
##  Super Breath    Wallcrawling    Vision - Night  Vision - Infrared
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical    
##  FALSE:644       FALSE:633       FALSE:633       FALSE:645        
##  TRUE :23        TRUE :34        TRUE :34        TRUE :22         
##  Grim Reaping    Matter Absorption The Force       Resurrection   
##  Mode :logical   Mode :logical     Mode :logical   Mode :logical  
##  FALSE:664       FALSE:656         FALSE:661       FALSE:652      
##  TRUE :3         TRUE :11          TRUE :6         TRUE :15       
##  Terrakinesis    Vision - Heat   Vitakinesis     Radar Sense    
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical  
##  FALSE:665       FALSE:648       FALSE:665       FALSE:661      
##  TRUE :2         TRUE :19        TRUE :2         TRUE :6        
##  Qwardian Power Ring Weather Control Vision - X-Ray  Vision - Thermal
##  Mode :logical       Mode :logical   Mode :logical   Mode :logical   
##  FALSE:665           FALSE:659       FALSE:644       FALSE:644       
##  TRUE :2             TRUE :8         TRUE :23        TRUE :23        
##  Web Creation    Reality Warping Odin Force      Symbiote Costume
##  Mode :logical   Mode :logical   Mode :logical   Mode :logical   
##  FALSE:653       FALSE:651       FALSE:665       FALSE:658       
##  TRUE :14        TRUE :16        TRUE :2         TRUE :9         
##  Speed Force     Phoenix Force   Molecular Dissipation Vision - Cryo  
##  Mode :logical   Mode :logical   Mode :logical         Mode :logical  
##  FALSE:666       FALSE:666       FALSE:666             FALSE:665      
##  TRUE :1         TRUE :1         TRUE :1               TRUE :2        
##  Omnipresent     Omniscient     
##  Mode :logical   Mode :logical  
##  FALSE:665       FALSE:665      
##  TRUE :2         TRUE :2
```



```r
superhero_power <- clean_names(superhero_powers)
```

13. How many superheros have a combination of agility, stealth, super_strength, stamina?

```r
dim(superhero_power)
```

```
## [1] 667 168
```


```r
special_heros <- superhero_power %>%
  filter(agility == "TRUE", stealth == "TRUE", super_strength == "TRUE", stamina == "TRUE")
table(special_heros$hero_names)
```

```
## 
##       Alex Mercer             Angel        Ant-Man II           Aquaman 
##                 1                 1                 1                 1 
##            Batman       Black Flash       Black Manta        Brundlefly 
##                 1                 1                 1                 1 
##             Buffy             Cable   Captain Britain       Cheetah III 
##                 1                 1                 1                 1 
##        Darth Maul            Gamora      Harley Quinn            Hybrid 
##                 1                 1                 1                 1 
##         King Kong  Lady Deathstrike            Legion Martian Manhunter 
##                 1                 1                 1                 1 
##          Mystique    Naruto Uzumaki              Odin             Orion 
##                 1                 1                 1                 1 
##        Sabretooth              Silk             Spawn           Spectre 
##                 1                 1                 1                 1 
##       Spider-Girl       Spider-Gwen        Spider-Man       Steppenwolf 
##                 1                 1                 1                 1 
##            T-1000               T-X             Toxin            Ultron 
##                 1                 1                 1                 1 
##         Venompool             Vixen         Wolverine              X-23 
##                 1                 1                 1                 1
```

## Your Favorite
14. Pick your favorite superhero and let's see their powers! 


```r
my_favorite <- superhero_power %>%
  filter(hero_names == "Groot") %>%
  select_if(~all(. == "TRUE") | . == "Groot")
my_favorite
```

```
## # A tibble: 1 × 14
##   hero_names durability longevity intelligence super_strength duplication
##   <chr>      <lgl>      <lgl>     <lgl>        <lgl>          <lgl>      
## 1 Groot      TRUE       TRUE      TRUE         TRUE           TRUE       
## # ℹ 8 more variables: size_changing <lgl>, stamina <lgl>,
## #   invulnerability <lgl>, fire_resistance <lgl>, regeneration <lgl>,
## #   plant_control <lgl>, matter_absorption <lgl>, resurrection <lgl>
```

15. Can you find your hero in the superhero_info data? Show their info!  


```r
my_hero_info <- superhero_power %>%
  filter(hero_names %in% my_favorite$hero_names)
my_hero_info
```

```
## # A tibble: 1 × 168
##   hero_names agility accelerated_healing lantern_power_ring
##   <chr>      <lgl>   <lgl>               <lgl>             
## 1 Groot      FALSE   FALSE               FALSE             
## # ℹ 164 more variables: dimensional_awareness <lgl>, cold_resistance <lgl>,
## #   durability <lgl>, stealth <lgl>, energy_absorption <lgl>, flight <lgl>,
## #   danger_sense <lgl>, underwater_breathing <lgl>, marksmanship <lgl>,
## #   weapons_master <lgl>, power_augmentation <lgl>, animal_attributes <lgl>,
## #   longevity <lgl>, intelligence <lgl>, super_strength <lgl>,
## #   cryokinesis <lgl>, telepathy <lgl>, energy_armor <lgl>,
## #   energy_blasts <lgl>, duplication <lgl>, size_changing <lgl>, …
```

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.  
