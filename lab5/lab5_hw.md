---
title: "dplyr Superhero"
date: "2022-02-14"
output:
  html_document: 
    theme: spacelab
    toc: yes
    toc_float: yes
    keep_md: yes
---

## Load the tidyverse

```r
library("tidyverse")
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.1 ──
```

```
## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
## ✓ tibble  3.1.6     ✓ dplyr   1.0.7
## ✓ tidyr   1.1.4     ✓ stringr 1.4.0
## ✓ readr   2.1.1     ✓ forcats 0.5.1
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

## Load the superhero data
These are data taken from comic books and assembled by fans. The include a good mix of categorical and continuous data.  Data taken from: https://www.kaggle.com/claudiodavi/superhero-set  

Check out the way I am loading these data. If I know there are NAs, I can take care of them at the beginning. But, we should do this very cautiously. At times it is better to keep the original columns and data intact.  

```r
superhero_info <- readr::read_csv("data/heroes_information.csv", na = c("", "-99", "-"))
```

```
## Rows: 734 Columns: 10
```

```
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr (8): name, Gender, Eye color, Race, Hair color, Publisher, Skin color, A...
## dbl (2): Height, Weight
```

```
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```


```r
superhero_powers <- readr::read_csv("data/super_hero_powers.csv", na = c("", "-99", "-"))
```

```
## Rows: 667 Columns: 168
```

```
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr   (1): hero_names
## lgl (167): Agility, Accelerated Healing, Lantern Power Ring, Dimensional Awa...
```

```
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
```

## Data tidy
1. Some of the names used in the `superhero_info` data are problematic so you should rename them here.  

```r
head(superhero_info)
```

```
## # A tibble: 6 × 10
##   name    Gender `Eye color` Race    `Hair color` Height Publisher  `Skin color`
##   <chr>   <chr>  <chr>       <chr>   <chr>         <dbl> <chr>      <chr>       
## 1 A-Bomb  Male   yellow      Human   No Hair         203 Marvel Co… <NA>        
## 2 Abe Sa… Male   blue        Icthyo… No Hair         191 Dark Hors… blue        
## 3 Abin S… Male   blue        Ungaran No Hair         185 DC Comics  red         
## 4 Abomin… Male   green       Human … No Hair         203 Marvel Co… <NA>        
## 5 Abraxas Male   blue        Cosmic… Black            NA Marvel Co… <NA>        
## 6 Absorb… Male   blue        Human   No Hair         193 Marvel Co… <NA>        
## # … with 2 more variables: Alignment <chr>, Weight <dbl>
```

Yikes! `superhero_powers` has a lot of variables that are poorly named. We need some R superpowers...

```r
head(superhero_powers)
```

```
## # A tibble: 6 × 168
##   hero_names  Agility `Accelerated Healing` `Lantern Power Ri… `Dimensional Awa…
##   <chr>       <lgl>   <lgl>                 <lgl>              <lgl>            
## 1 3-D Man     TRUE    FALSE                 FALSE              FALSE            
## 2 A-Bomb      FALSE   TRUE                  FALSE              FALSE            
## 3 Abe Sapien  TRUE    TRUE                  FALSE              FALSE            
## 4 Abin Sur    FALSE   FALSE                 TRUE               FALSE            
## 5 Abomination FALSE   TRUE                  FALSE              FALSE            
## 6 Abraxas     FALSE   FALSE                 FALSE              TRUE             
## # … with 163 more variables: Cold Resistance <lgl>, Durability <lgl>,
## #   Stealth <lgl>, Energy Absorption <lgl>, Flight <lgl>, Danger Sense <lgl>,
## #   Underwater breathing <lgl>, Marksmanship <lgl>, Weapons Master <lgl>,
## #   Power Augmentation <lgl>, Animal Attributes <lgl>, Longevity <lgl>,
## #   Intelligence <lgl>, Super Strength <lgl>, Cryokinesis <lgl>,
## #   Telepathy <lgl>, Energy Armor <lgl>, Energy Blasts <lgl>,
## #   Duplication <lgl>, Size Changing <lgl>, Density Control <lgl>, …
```

## `janitor`
The [janitor](https://garthtarr.github.io/meatR/janitor.html) package is your friend. Make sure to install it and then load the library.  

```r
library("janitor")
```

```
## 
## Attaching package: 'janitor'
```

```
## The following objects are masked from 'package:stats':
## 
##     chisq.test, fisher.test
```

The `clean_names` function takes care of everything in one line! Now that's a superpower!

```r
superhero_powers <- janitor::clean_names(superhero_powers)
```

## `tabyl`
The `janitor` package has many awesome functions that we will explore. Here is its version of `table` which not only produces counts but also percentages. Very handy! Let's use it to explore the proportion of good guys and bad guys in the `superhero_info` data.  


```r
tabyl(superhero_info, Alignment)
```

```
##  Alignment   n     percent valid_percent
##        bad 207 0.282016349    0.28473177
##       good 496 0.675749319    0.68225585
##    neutral  24 0.032697548    0.03301238
##       <NA>   7 0.009536785            NA
```

2. Notice that we have some neutral superheros! Who are they?

```r
filter(superhero_info, Alignment == "neutral")
```

```
## # A tibble: 24 × 10
##    name    Gender `Eye color` Race    `Hair color` Height Publisher `Skin color`
##    <chr>   <chr>  <chr>       <chr>   <chr>         <dbl> <chr>     <chr>       
##  1 Bizarro Male   black       Bizarro Black           191 DC Comics white       
##  2 Black … Male   <NA>        God / … <NA>             NA DC Comics <NA>        
##  3 Captai… Male   brown       Human   Brown            NA DC Comics <NA>        
##  4 Copycat Female red         Mutant  White           183 Marvel C… blue        
##  5 Deadpo… Male   brown       Mutant  No Hair         188 Marvel C… <NA>        
##  6 Deaths… Male   blue        Human   White           193 DC Comics <NA>        
##  7 Etrigan Male   red         Demon   No Hair         193 DC Comics yellow      
##  8 Galact… Male   black       Cosmic… Black           876 Marvel C… <NA>        
##  9 Gladia… Male   blue        Stront… Blue            198 Marvel C… purple      
## 10 Indigo  Female <NA>        Alien   Purple           NA DC Comics <NA>        
## # … with 14 more rows, and 2 more variables: Alignment <chr>, Weight <dbl>
```

## `superhero_info`
3. Let's say we are only interested in the variables name, alignment, and "race". How would you isolate these variables from `superhero_info`?

```r
select(superhero_info, name, Alignment, "Race")
```

```
## # A tibble: 734 × 3
##    name          Alignment Race             
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
## # … with 724 more rows
```
```

## Not Human
4. List all of the superheros that are not human.

## Good and Evil
5. Let's make two different data frames, one focused on the "good guys" and another focused on the "bad guys".```{r}
filter(superhero_info, Alignment == "good")
```

```r
filter(superhero_info, Alignment == "good")
```

```
## # A tibble: 496 × 10
##    name    Gender `Eye color` Race   `Hair color` Height Publisher  `Skin color`
##    <chr>   <chr>  <chr>       <chr>  <chr>         <dbl> <chr>      <chr>       
##  1 A-Bomb  Male   yellow      Human  No Hair         203 Marvel Co… <NA>        
##  2 Abe Sa… Male   blue        Icthy… No Hair         191 Dark Hors… blue        
##  3 Abin S… Male   blue        Ungar… No Hair         185 DC Comics  red         
##  4 Adam M… Male   blue        <NA>   Blond            NA NBC - Her… <NA>        
##  5 Adam S… Male   blue        Human  Blond           185 DC Comics  <NA>        
##  6 Agent … Female blue        <NA>   Blond           173 Marvel Co… <NA>        
##  7 Agent … Male   brown       Human  Brown           178 Marvel Co… <NA>        
##  8 Agent … Male   <NA>        <NA>   <NA>            191 Marvel Co… <NA>        
##  9 Alan S… Male   blue        <NA>   Blond           180 DC Comics  <NA>        
## 10 Alex W… Male   <NA>        <NA>   <NA>             NA NBC - Her… <NA>        
## # … with 486 more rows, and 2 more variables: Alignment <chr>, Weight <dbl>
```



```r
filter(superhero_info, Alignment == "bad")
```

```
## # A tibble: 207 × 10
##    name    Gender `Eye color` Race   `Hair color` Height Publisher  `Skin color`
##    <chr>   <chr>  <chr>       <chr>  <chr>         <dbl> <chr>      <chr>       
##  1 Abomin… Male   green       Human… No Hair         203 Marvel Co… <NA>        
##  2 Abraxas Male   blue        Cosmi… Black            NA Marvel Co… <NA>        
##  3 Absorb… Male   blue        Human  No Hair         193 Marvel Co… <NA>        
##  4 Air-Wa… Male   blue        <NA>   White           188 Marvel Co… <NA>        
##  5 Ajax    Male   brown       Cyborg Black           193 Marvel Co… <NA>        
##  6 Alex M… Male   <NA>        Human  <NA>             NA Wildstorm  <NA>        
##  7 Alien   Male   <NA>        Xenom… No Hair         244 Dark Hors… black       
##  8 Amazo   Male   red         Andro… <NA>            257 DC Comics  <NA>        
##  9 Ammo    Male   brown       Human  Black           188 Marvel Co… <NA>        
## 10 Angela  Female <NA>        <NA>   <NA>             NA Image Com… <NA>        
## # … with 197 more rows, and 2 more variables: Alignment <chr>, Weight <dbl>
```

6. For the good guys, use the `tabyl` function to summarize their "race".

```r
tabyl(superhero_info, Race)
```

```
##                Race   n     percent valid_percent
##               Alien   7 0.009536785   0.016279070
##               Alpha   5 0.006811989   0.011627907
##              Amazon   2 0.002724796   0.004651163
##             Android   9 0.012261580   0.020930233
##              Animal   4 0.005449591   0.009302326
##           Asgardian   5 0.006811989   0.011627907
##           Atlantean   5 0.006811989   0.011627907
##             Bizarro   1 0.001362398   0.002325581
##          Bolovaxian   1 0.001362398   0.002325581
##               Clone   1 0.001362398   0.002325581
##       Cosmic Entity   4 0.005449591   0.009302326
##              Cyborg  11 0.014986376   0.025581395
##            Czarnian   1 0.001362398   0.002325581
##  Dathomirian Zabrak   1 0.001362398   0.002325581
##            Demi-God   2 0.002724796   0.004651163
##               Demon   6 0.008174387   0.013953488
##             Eternal   2 0.002724796   0.004651163
##      Flora Colossus   1 0.001362398   0.002325581
##         Frost Giant   2 0.002724796   0.004651163
##       God / Eternal  14 0.019073569   0.032558140
##             Gorilla   1 0.001362398   0.002325581
##              Gungan   1 0.001362398   0.002325581
##               Human 208 0.283378747   0.483720930
##     Human / Altered   3 0.004087193   0.006976744
##       Human / Clone   1 0.001362398   0.002325581
##      Human / Cosmic   2 0.002724796   0.004651163
##   Human / Radiation  11 0.014986376   0.025581395
##          Human-Kree   2 0.002724796   0.004651163
##       Human-Spartoi   1 0.001362398   0.002325581
##        Human-Vulcan   1 0.001362398   0.002325581
##     Human-Vuldarian   1 0.001362398   0.002325581
##       Icthyo Sapien   1 0.001362398   0.002325581
##             Inhuman   4 0.005449591   0.009302326
##               Kaiju   1 0.001362398   0.002325581
##     Kakarantharaian   1 0.001362398   0.002325581
##           Korugaran   1 0.001362398   0.002325581
##          Kryptonian   7 0.009536785   0.016279070
##           Luphomoid   1 0.001362398   0.002325581
##               Maiar   1 0.001362398   0.002325581
##             Martian   1 0.001362398   0.002325581
##           Metahuman   2 0.002724796   0.004651163
##              Mutant  63 0.085831063   0.146511628
##      Mutant / Clone   1 0.001362398   0.002325581
##             New God   3 0.004087193   0.006976744
##            Neyaphem   1 0.001362398   0.002325581
##           Parademon   1 0.001362398   0.002325581
##              Planet   1 0.001362398   0.002325581
##              Rodian   1 0.001362398   0.002325581
##              Saiyan   2 0.002724796   0.004651163
##             Spartoi   1 0.001362398   0.002325581
##           Strontian   1 0.001362398   0.002325581
##            Symbiote   9 0.012261580   0.020930233
##            Talokite   1 0.001362398   0.002325581
##          Tamaranean   1 0.001362398   0.002325581
##             Ungaran   1 0.001362398   0.002325581
##             Vampire   2 0.002724796   0.004651163
##     Xenomorph XX121   1 0.001362398   0.002325581
##              Yautja   1 0.001362398   0.002325581
##      Yoda's species   1 0.001362398   0.002325581
##       Zen-Whoberian   1 0.001362398   0.002325581
##              Zombie   1 0.001362398   0.002325581
##                <NA> 304 0.414168937            NA
```

7. Among the good guys, Who are the Asgardians?

```r
superhero_info %>%
  select(Alignment, Race) %>%
  filter( Alignment == "good") %>%
  filter(Race == "Asgardian")
```

```
## # A tibble: 3 × 2
##   Alignment Race     
##   <chr>     <chr>    
## 1 good      Asgardian
## 2 good      Asgardian
## 3 good      Asgardian
```

8. Among the bad guys, who are the male humans over 200 inches in height?

```r
superhero_info %>%
  select(Alignment, Height) %>%
  filter( Alignment == "bad") %>%
  filter(Height >= "200")
```

```
## # A tibble: 28 × 2
##    Alignment Height
##    <chr>      <dbl>
##  1 bad        203  
##  2 bad        244  
##  3 bad        257  
##  4 bad         61  
##  5 bad        213  
##  6 bad        203  
##  7 bad        218  
##  8 bad         30.5
##  9 bad        267  
## 10 bad        201  
## # … with 18 more rows
```

9. OK, so are there more good guys or bad guys that are bald (personal interest)?

10. Let's explore who the really "big" superheros are. In the `superhero_info` data, which have a height over 200 or weight greater than or equal to 450?

```r
superhero_info %>%
  select(Height, Weight) %>%
  filter( Height == "200") %>%
  filter(Weight == "450") 
```

```
## # A tibble: 0 × 2
## # … with 2 variables: Height <dbl>, Weight <dbl>
```

11. Just to be clear on the `|` operator,  have a look at the superheros over 300 in height...

```r
filter(superhero_info, Height >= "300") 
```

```
## # A tibble: 14 × 10
##    name    Gender `Eye color` Race   `Hair color` Height Publisher  `Skin color`
##    <chr>   <chr>  <chr>       <chr>  <chr>         <dbl> <chr>      <chr>       
##  1 Anti-M… Male   yellow      God /… No Hair        61   DC Comics  <NA>        
##  2 Fin Fa… Male   red         Kakar… No Hair       975   Marvel Co… green       
##  3 Galact… Male   black       Cosmi… Black         876   Marvel Co… <NA>        
##  4 Giganta Female green       <NA>   Red            62.5 DC Comics  <NA>        
##  5 Groot   Male   yellow      Flora… <NA>          701   Marvel Co… <NA>        
##  6 Howard… Male   brown       <NA>   Yellow         79   Marvel Co… <NA>        
##  7 Jack-J… Male   blue        Human  Brown          71   Dark Hors… <NA>        
##  8 Krypto  Male   blue        Krypt… White          64   DC Comics  <NA>        
##  9 MODOK   Male   white       Cyborg Brownn        366   Marvel Co… <NA>        
## 10 Onslau… Male   red         Mutant No Hair       305   Marvel Co… <NA>        
## 11 Sasqua… Male   red         <NA>   Orange        305   Marvel Co… <NA>        
## 12 Wolfsb… Female green       <NA>   Auburn        366   Marvel Co… <NA>        
## 13 Ymir    Male   white       Frost… No Hair       305.  Marvel Co… white       
## 14 Yoda    Male   brown       Yoda'… White          66   George Lu… green       
## # … with 2 more variables: Alignment <chr>, Weight <dbl>
```

12. ...and the superheros over 450 in weight. Bonus question! Why do we not have 16 rows in question #10?

```r
filter(superhero_info, Weight >= "450")
```

```
## # A tibble: 339 × 10
##    name    Gender `Eye color` Race   `Hair color` Height Publisher  `Skin color`
##    <chr>   <chr>  <chr>       <chr>  <chr>         <dbl> <chr>      <chr>       
##  1 Abe Sa… Male   blue        Icthy… No Hair         191 Dark Hors… blue        
##  2 Abin S… Male   blue        Ungar… No Hair         185 DC Comics  red         
##  3 Adam S… Male   blue        Human  Blond           185 DC Comics  <NA>        
##  4 Agent … Female blue        <NA>   Blond           173 Marvel Co… <NA>        
##  5 Agent … Male   brown       Human  Brown           178 Marvel Co… <NA>        
##  6 Ajax    Male   brown       Cyborg Black           193 Marvel Co… <NA>        
##  7 Alan S… Male   blue        <NA>   Blond           180 DC Comics  <NA>        
##  8 Alfred… Male   blue        Human  Black           178 DC Comics  <NA>        
##  9 Angel   Male   blue        <NA>   Blond           183 Marvel Co… <NA>        
## 10 Angel … Female yellow      Mutant Black           165 Marvel Co… <NA>        
## # … with 329 more rows, and 2 more variables: Alignment <chr>, Weight <dbl>
```

## Height to Weight Ratio
13. It's easy to be strong when you are heavy and tall, but who is heavy and short? Which superheros have the highest height to weight ratio?

```r
superhero_info %>% 
  select(Height, Weight) %>% 
  arrange(Weight)
```

```
## # A tibble: 734 × 2
##    Height Weight
##     <dbl>  <dbl>
##  1     NA      2
##  2    701      4
##  3     71     14
##  4    876     16
##  5     66     17
##  6    975     18
##  7     79     18
##  8     64     18
##  9    122     25
## 10    122     27
## # … with 724 more rows
```

## `superhero_powers`
Have a quick look at the `superhero_powers` data frame.  

```r
glimpse(superhero_powers)
```

```
## Rows: 667
## Columns: 168
## $ hero_names                   <chr> "3-D Man", "A-Bomb", "Abe Sapien", "Abin …
## $ agility                      <lgl> TRUE, FALSE, TRUE, FALSE, FALSE, FALSE, F…
## $ accelerated_healing          <lgl> FALSE, TRUE, TRUE, FALSE, TRUE, FALSE, FA…
## $ lantern_power_ring           <lgl> FALSE, FALSE, FALSE, TRUE, FALSE, FALSE, …
## $ dimensional_awareness        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ cold_resistance              <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ durability                   <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, T…
## $ stealth                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ flight                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ danger_sense                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ underwater_breathing         <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ marksmanship                 <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ weapons_master               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ power_augmentation           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animal_attributes            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ longevity                    <lgl> FALSE, TRUE, TRUE, FALSE, FALSE, FALSE, F…
## $ intelligence                 <lgl> FALSE, FALSE, TRUE, FALSE, TRUE, TRUE, FA…
## $ super_strength               <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, TRUE, TRUE…
## $ cryokinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ telepathy                    <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ energy_armor                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_blasts                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ duplication                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ size_changing                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ density_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ stamina                      <lgl> TRUE, TRUE, TRUE, FALSE, TRUE, FALSE, FAL…
## $ astral_travel                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ audio_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ dexterity                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnitrix                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ super_speed                  <lgl> TRUE, FALSE, FALSE, FALSE, TRUE, TRUE, FA…
## $ possession                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animal_oriented_powers       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ weapon_based_powers          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ electrokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ darkforce_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ death_touch                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ teleportation                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ enhanced_senses              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ telekinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_beams                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ magic                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ hyperkinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ jump                         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ clairvoyance                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ dimensional_travel           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ power_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ shapeshifting                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ peak_human_condition         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ immortality                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, TRUE, F…
## $ camouflage                   <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALSE, …
## $ element_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ phasing                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ astral_projection            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ electrical_transport         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ fire_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ projection                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ summoning                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_memory              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ reflexes                     <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ invulnerability              <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, TRUE, T…
## $ energy_constructs            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ force_fields                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ self_sustenance              <lgl> FALSE, TRUE, FALSE, FALSE, FALSE, FALSE, …
## $ anti_gravity                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ empathy                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_nullifier              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ radiation_control            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ psionic_powers               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ elasticity                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ substance_secretion          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ elemental_transmogrification <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ technopath_cyberpath         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ photographic_reflexes        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ seismic_power                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animation                    <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALSE, …
## $ precognition                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ mind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ fire_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_absorption             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_hearing             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ nova_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ insanity                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ hypnokinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ animal_control               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ natural_armor                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ intangibility                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_sight               <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ molecular_manipulation       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ heat_generation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ adaptation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ gliding                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_suit                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ mind_blast                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ probability_manipulation     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ gravity_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ regeneration                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ light_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ echolocation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ levitation                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ toxin_and_disease_control    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ banish                       <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_manipulation          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ heat_resistance              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ natural_weapons              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ time_travel                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_smell               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ illusions                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ thirstokinesis               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ hair_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ illumination                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnipotent                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ cloaking                     <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ changing_armor               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ power_cosmic                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, TRUE, …
## $ biokinesis                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ water_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ radiation_immunity           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_telescopic            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ toxin_and_disease_resistance <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ spatial_awareness            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ energy_resistance            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ telepathy_resistance         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ molecular_combustion         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnilingualism               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ portal_creation              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ magnetism                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ mind_control_resistance      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ plant_control                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ sonar                        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ sonic_scream                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ time_manipulation            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ enhanced_touch               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ magic_resistance             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ invisibility                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ sub_mariner                  <lgl> FALSE, FALSE, TRUE, FALSE, FALSE, FALSE, …
## $ radiation_absorption         <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ intuitive_aptitude           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_microscopic           <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ melting                      <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ wind_control                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ super_breath                 <lgl> FALSE, FALSE, FALSE, FALSE, TRUE, FALSE, …
## $ wallcrawling                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_night                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_infrared              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ grim_reaping                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ matter_absorption            <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ the_force                    <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ resurrection                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ terrakinesis                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_heat                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vitakinesis                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ radar_sense                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ qwardian_power_ring          <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ weather_control              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_x_ray                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_thermal               <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ web_creation                 <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ reality_warping              <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ odin_force                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ symbiote_costume             <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ speed_force                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ phoenix_force                <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ molecular_dissipation        <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ vision_cryo                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omnipresent                  <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
## $ omniscient                   <lgl> FALSE, FALSE, FALSE, FALSE, FALSE, FALSE,…
```

14. How many superheros have a combination of accelerated healing, durability, and super strength?

```r
superhero_powers %>% 
  select(accelerated_healing, durability, super_strength)
```

```
## # A tibble: 667 × 3
##    accelerated_healing durability super_strength
##    <lgl>               <lgl>      <lgl>         
##  1 FALSE               FALSE      TRUE          
##  2 TRUE                TRUE       TRUE          
##  3 TRUE                TRUE       TRUE          
##  4 FALSE               FALSE      FALSE         
##  5 TRUE                FALSE      TRUE          
##  6 FALSE               FALSE      TRUE          
##  7 FALSE               TRUE       TRUE          
##  8 TRUE                FALSE      FALSE         
##  9 FALSE               TRUE       FALSE         
## 10 FALSE               FALSE      FALSE         
## # … with 657 more rows
```

## Your Favorite
15. Pick your favorite superhero and let's see their powers!

```r
filter(superhero_powers, hero_names == "Batman")
```

```
## # A tibble: 1 × 168
##   hero_names agility accelerated_healing lantern_power_ring dimensional_awarene…
##   <chr>      <lgl>   <lgl>               <lgl>              <lgl>               
## 1 Batman     TRUE    FALSE               FALSE              FALSE               
## # … with 163 more variables: cold_resistance <lgl>, durability <lgl>,
## #   stealth <lgl>, energy_absorption <lgl>, flight <lgl>, danger_sense <lgl>,
## #   underwater_breathing <lgl>, marksmanship <lgl>, weapons_master <lgl>,
## #   power_augmentation <lgl>, animal_attributes <lgl>, longevity <lgl>,
## #   intelligence <lgl>, super_strength <lgl>, cryokinesis <lgl>,
## #   telepathy <lgl>, energy_armor <lgl>, energy_blasts <lgl>,
## #   duplication <lgl>, size_changing <lgl>, density_control <lgl>, …
```

## Push your final code to GitHub!
Please be sure that you check the `keep md` file in the knit preferences.   
