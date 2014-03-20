Market Basket Analysis
========================================================
Market Basket Analysis is a specific application of Association rule mining, where retail transaction baskets are analysed to find the products which are likely to be purchased together. The analysis output forms the input for  recomendation engines/marketing strategies. The data "Web Service Association Data.xls" is data taken from Autodesk Inc. where it indicates Products that clients have purchased. The format is according to their ID and product/service.

### Install the "arules" package and other packages required
1. arules contains the apriori function
2. gdata is for reading xls

```r
install.packages("arules")
```

```
## Error: trying to use CRAN without setting a mirror
```

```r
install.packages("gdata")
```

```
## Error: trying to use CRAN without setting a mirror
```

```r

library("arules")
```

```
## Loading required package: Matrix
## Loading required package: lattice
## 
## Attaching package: 'arules'
## 
## The following objects are masked from 'package:base':
## 
##     %in%, write
```

```r
library("gdata")
```

```
## gdata: read.xls support for 'XLS' (Excel 97-2004) files ENABLED.
## 
## gdata: read.xls support for 'XLSX' (Excel 2007+) files ENABLED.
## 
## Attaching package: 'gdata'
## 
## The following object is masked from 'package:stats':
## 
##     nobs
## 
## The following object is masked from 'package:utils':
## 
##     object.size
```


Data Preparation
--------------
1. Create a data frame from the data using read.xls from the gdata library.
2. Create the transaction listing as the data is not in transactional format
3. Convert the List into data frame using the plyr library

```r
adwebservices <- read.xls("Web Service Assocation Data.xls")

# The data is not in transactional format, which APRIORI function will
# require, so we have to create the a dataframe with transactions
attach(adwebservices)
trans_list <- split(Service.Name, Company.ID)
head(trans_list)
```

```
## $`0`
##  [1] AutoCAD WS                                         
##  [2] Autodesk PLM 360                                   
##  [3] Autodesk 360                                       
##  [4] Autodesk 360 Rendering                             
##  [5] Autodesk 360 Mobile (Android)                      
##  [6] Autodesk 360 Mobile (iOS)                          
##  [7] Autodesk 360 Structural Analysis for Autodesk Revit
##  [8] Autodesk BIM 360 Field                             
##  [9] BIM 360 Glue                                       
## [10] Autodesk Simulation CFD 360                        
## [11] Factory Process Simulation                         
## [12] Mockup 360                                         
## [13] AIM Online                                         
## [14] Autodesk 360 Infrastructure Modeler (Mobile)       
## [15] Blink                                              
## [16] Autodesk Simulation Mechanical 360                 
## [17] Fusion 360                                         
## [18] Kraken Simulation                                  
## [19] AIM 360                                            
## [20] Robot Structural Analysis 360                      
## [21] Autodesk Simulation Moldflow Insight 360           
## 21 Levels: AIM 360 AIM Online AutoCAD WS ... Robot Structural Analysis 360
## 
## $`0006E962-C120-475d-80C3-38760B6CB604`
## [1] Autodesk 360           Autodesk 360 Rendering
## 21 Levels: AIM 360 AIM Online AutoCAD WS ... Robot Structural Analysis 360
## 
## $`0012AFAB-4E22-453b-95B5-DB7816096BDC`
## [1] Autodesk 360              Autodesk 360 Mobile (iOS)
## [3] Autodesk 360 Rendering    BIM 360 Glue             
## 21 Levels: AIM 360 AIM Online AutoCAD WS ... Robot Structural Analysis 360
## 
## $`001C582C-A3E9-417e-8E37-43CF7DD890A1`
## [1] Autodesk Simulation CFD 360        Autodesk Simulation Mechanical 360
## [3] Robot Structural Analysis 360     
## 21 Levels: AIM 360 AIM Online AutoCAD WS ... Robot Structural Analysis 360
## 
## $`002CAAC5-96AB-4ead-87E2-624A092089F3`
## [1] Autodesk PLM 360
## 21 Levels: AIM 360 AIM Online AutoCAD WS ... Robot Structural Analysis 360
## 
## $`00367FFB-4B3A-4713-A3D1-AC195FEB217E`
## [1] Autodesk 360 BIM 360 Glue
## 21 Levels: AIM 360 AIM Online AutoCAD WS ... Robot Structural Analysis 360
```


### Using the apriori function from arules library, we create the association rules.
A note on Support: Support is defined as Supp(Z) = P(Z), the probability of the Precedent occuring in the entire set of data.
A note on Confidence: Confidence is defined as Conf(X -> Y) = Supp(X->Y)/Supp(X) = P(Y|X). In other words, the probably of Y occuring given X.

```r
# We want to see almost all the types of association rules in the basket. By
# providing a support of 1% and confidence of 40%.  This allows us to view
# some rules whereby we have a high confidence and low support, implying
# that there is untapped sales potential
basket_rules <- apriori(trans_list, parameter = list(supp = 0.1, conf = 0.4, 
    target = "rules"))
```

```
## 
## parameter specification:
##  confidence minval smax arem  aval originalSupport support minlen maxlen
##         0.4    0.1    1 none FALSE            TRUE     0.1      1     10
##  target   ext
##   rules FALSE
## 
## algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## apriori - find association rules with the apriori algorithm
## version 4.21 (2004.05.09)        (c) 1996-2004   Christian Borgelt
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[21 item(s), 26240 transaction(s)] done [0.01s].
## sorting and recoding items ... [4 item(s)] done [0.00s].
## creating transaction tree ... done [0.01s].
## checking subsets of size 1 2 done [0.00s].
## writing ... [6 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

```r
basket_rules <- apriori(trans_list, parameter = list(supp = 0.01, conf = 0.4, 
    target = "rules"))
```

```
## 
## parameter specification:
##  confidence minval smax arem  aval originalSupport support minlen maxlen
##         0.4    0.1    1 none FALSE            TRUE    0.01      1     10
##  target   ext
##   rules FALSE
## 
## algorithmic control:
##  filter tree heap memopt load sort verbose
##     0.1 TRUE TRUE  FALSE TRUE    2    TRUE
## 
## apriori - find association rules with the apriori algorithm
## version 4.21 (2004.05.09)        (c) 1996-2004   Christian Borgelt
## set item appearances ...[0 item(s)] done [0.00s].
## set transactions ...[21 item(s), 26240 transaction(s)] done [0.01s].
## sorting and recoding items ... [12 item(s)] done [0.00s].
## creating transaction tree ... done [0.01s].
## checking subsets of size 1 2 3 4 5 done [0.00s].
## writing ... [120 rule(s)] done [0.00s].
## creating S4 object  ... done [0.00s].
```

```r

# Number of rules:
basket_rules
```

```
## set of 120 rules
```

```r

# View the association rules:
inspect(basket_rules)
```

```
##     lhs                                                      rhs                                  support confidence    lift
## 1   {}                                                    => {AutoCAD WS}                         0.52607     0.5261  1.0000
## 2   {}                                                    => {Autodesk 360}                       0.64554     0.6455  1.0000
## 3   {Autodesk Simulation CFD 360}                         => {Autodesk Simulation Mechanical 360} 0.01139     0.7293 47.8400
## 4   {Autodesk Simulation Mechanical 360}                  => {Autodesk Simulation CFD 360}        0.01139     0.7475 47.8400
## 5   {Autodesk Simulation CFD 360}                         => {Autodesk 360}                       0.01189     0.7610  1.1788
## 6   {Autodesk Simulation Mechanical 360}                  => {Autodesk 360}                       0.01185     0.7775  1.2044
## 7   {Autodesk 360 Structural Analysis for Autodesk Revit} => {Autodesk 360 Rendering}             0.01181     0.5838  2.6371
## 8   {Autodesk 360 Structural Analysis for Autodesk Revit} => {AutoCAD WS}                         0.01090     0.5386  1.0238
## 9   {Autodesk 360 Structural Analysis for Autodesk Revit} => {Autodesk 360}                       0.01620     0.8004  1.2399
## 10  {Autodesk PLM 360}                                    => {AutoCAD WS}                         0.01120     0.5278  1.0033
## 11  {Autodesk PLM 360}                                    => {Autodesk 360}                       0.01547     0.7289  1.1291
## 12  {Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.02027     0.5252  8.4285
## 13  {Autodesk BIM 360 Field}                              => {Autodesk 360 Mobile (iOS)}          0.01814     0.4699  3.5708
## 14  {Autodesk BIM 360 Field}                              => {AutoCAD WS}                         0.01970     0.5104  0.9702
## 15  {Autodesk BIM 360 Field}                              => {Autodesk 360}                       0.02511     0.6505  1.0077
## 16  {Autodesk 360 Mobile (Android)}                       => {AutoCAD WS}                         0.04592     0.6973  1.3256
## 17  {Autodesk 360 Mobile (Android)}                       => {Autodesk 360}                       0.05537     0.8409  1.3026
## 18  {BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.03464     0.5560  4.2249
## 19  {BIM 360 Glue}                                        => {AutoCAD WS}                         0.03552     0.5700  1.0836
## 20  {BIM 360 Glue}                                        => {Autodesk 360}                       0.04737     0.7602  1.1777
## 21  {Autodesk 360 Mobile (iOS)}                           => {AutoCAD WS}                         0.08476     0.6441  1.2243
## 22  {Autodesk 360 Mobile (iOS)}                           => {Autodesk 360}                       0.10667     0.8106  1.2557
## 23  {Autodesk 360 Rendering}                              => {Autodesk 360}                       0.16117     0.7280  1.1277
## 24  {AutoCAD WS}                                          => {Autodesk 360}                       0.26540     0.5045  0.7815
## 25  {Autodesk 360}                                        => {AutoCAD WS}                         0.26540     0.4111  0.7815
## 26  {Autodesk 360 Rendering,                                                                                                
##      Autodesk 360 Structural Analysis for Autodesk Revit} => {Autodesk 360}                       0.01071     0.9065  1.4042
## 27  {Autodesk 360,                                                                                                          
##      Autodesk 360 Structural Analysis for Autodesk Revit} => {Autodesk 360 Rendering}             0.01071     0.6612  2.9866
## 28  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Structural Analysis for Autodesk Revit} => {Autodesk 360}                       0.01021     0.9371  1.4516
## 29  {Autodesk 360,                                                                                                          
##      Autodesk 360 Structural Analysis for Autodesk Revit} => {AutoCAD WS}                         0.01021     0.6306  1.1987
## 30  {AutoCAD WS,                                                                                                            
##      Autodesk PLM 360}                                    => {Autodesk 360}                       0.01025     0.9150  1.4174
## 31  {Autodesk 360,                                                                                                          
##      Autodesk PLM 360}                                    => {AutoCAD WS}                         0.01025     0.6626  1.2595
## 32  {Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01433     0.7068  5.3709
## 33  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01433     0.7899 12.6773
## 34  {Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk BIM 360 Field}             0.01433     0.4136 10.7147
## 35  {Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01315     0.6485  1.2327
## 36  {AutoCAD WS,                                                                                                            
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01315     0.6673 10.7096
## 37  {Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01684     0.8308  1.2870
## 38  {Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01684     0.6707 10.7642
## 39  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {Autodesk 360 Rendering}             0.01010     0.5567  2.5148
## 40  {Autodesk 360 Rendering,                                                                                                
##      Autodesk BIM 360 Field}                              => {Autodesk 360 Mobile (iOS)}          0.01010     0.7794  5.9229
## 41  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {AutoCAD WS}                         0.01315     0.7248  1.3778
## 42  {AutoCAD WS,                                                                                                            
##      Autodesk BIM 360 Field}                              => {Autodesk 360 Mobile (iOS)}          0.01315     0.6673  5.0710
## 43  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {Autodesk 360}                       0.01650     0.9097  1.4091
## 44  {Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field}                              => {Autodesk 360 Mobile (iOS)}          0.01650     0.6571  4.9931
## 45  {Autodesk 360 Rendering,                                                                                                
##      Autodesk BIM 360 Field}                              => {Autodesk 360}                       0.01235     0.9529  1.4762
## 46  {Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field}                              => {Autodesk 360 Rendering}             0.01235     0.4917  2.2209
## 47  {AutoCAD WS,                                                                                                            
##      Autodesk BIM 360 Field}                              => {Autodesk 360}                       0.01639     0.8317  1.2884
## 48  {Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field}                              => {AutoCAD WS}                         0.01639     0.6525  1.2403
## 49  {Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Mobile (iOS)}                           => {AutoCAD WS}                         0.01162     0.7841  1.4904
## 50  {Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Mobile (iOS)}                           => {Autodesk 360}                       0.01433     0.9666  1.4973
## 51  {Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Rendering}                              => {AutoCAD WS}                         0.01612     0.7318  1.3911
## 52  {Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Rendering}                              => {Autodesk 360}                       0.02107     0.9567  1.4821
## 53  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (Android)}                       => {Autodesk 360}                       0.03952     0.8606  1.3331
## 54  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (Android)}                       => {AutoCAD WS}                         0.03952     0.7137  1.3567
## 55  {Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.01814     0.5237  2.3654
## 56  {Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01814     0.7323  5.5649
## 57  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {BIM 360 Glue}                       0.01814     0.4017  6.4467
## 58  {Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.02470     0.7129  1.3551
## 59  {AutoCAD WS,                                                                                                            
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.02470     0.6953  5.2836
## 60  {Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.03140     0.9065  1.4042
## 61  {Autodesk 360,                                                                                                          
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.03140     0.6629  5.0376
## 62  {Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01753     0.7077  1.3453
## 63  {AutoCAD WS,                                                                                                            
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.01753     0.4936  2.2295
## 64  {Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.02355     0.9508  1.4728
## 65  {Autodesk 360,                                                                                                          
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.02355     0.4972  2.2458
## 66  {AutoCAD WS,                                                                                                            
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.03083     0.8680  1.3446
## 67  {Autodesk 360,                                                                                                          
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.03083     0.6508  1.2372
## 68  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {AutoCAD WS}                         0.03121     0.6911  1.3138
## 69  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {Autodesk 360}                       0.04337     0.9603  1.4876
## 70  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS)}                           => {Autodesk 360 Rendering}             0.04337     0.4066  1.8365
## 71  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS)}                           => {Autodesk 360}                       0.07123     0.8404  1.3018
## 72  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS)}                           => {AutoCAD WS}                         0.07123     0.6677  1.2693
## 73  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Rendering}                              => {Autodesk 360}                       0.07207     0.8932  1.3837
## 74  {Autodesk 360,                                                                                                          
##      Autodesk 360 Rendering}                              => {AutoCAD WS}                         0.07207     0.4472  0.8500
## 75  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01075     0.7500  1.4257
## 76  {AutoCAD WS,                                                                                                            
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01075     0.8174  6.2115
## 77  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01075     0.8174 13.1183
## 78  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk BIM 360 Field}             0.01075     0.4352 11.2727
## 79  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01326     0.9255  1.4337
## 80  {Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01326     0.7873  5.9831
## 81  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01326     0.8037 12.8984
## 82  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk BIM 360 Field}             0.01326     0.4223 10.9397
## 83  {AutoCAD WS,                                                                                                            
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01193     0.9072  1.4054
## 84  {Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01193     0.7081  1.3461
## 85  {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01193     0.7279 11.6821
## 86  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {Autodesk 360}                       0.01235     0.9391  1.4548
## 87  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {AutoCAD WS}                         0.01235     0.7483  1.4224
## 88  {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field}                              => {Autodesk 360 Mobile (iOS)}          0.01235     0.7535  5.7259
## 89  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Mobile (iOS)}                           => {Autodesk 360}                       0.01120     0.9639  1.4932
## 90  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Mobile (iOS)}                           => {AutoCAD WS}                         0.01120     0.7819  1.4863
## 91  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Rendering}                              => {Autodesk 360}                       0.01555     0.9645  1.4942
## 92  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (Android),                                                                                         
##      Autodesk 360 Rendering}                              => {AutoCAD WS}                         0.01555     0.7378  1.4025
## 93  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01441     0.7941  1.5095
## 94  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.01441     0.5833  2.6350
## 95  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01441     0.8217  6.2446
## 96  {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {BIM 360 Glue}                       0.01441     0.4615  7.4072
## 97  {Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01791     0.9874  1.5296
## 98  {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.01791     0.5704  2.5765
## 99  {Autodesk 360,                                                                                                          
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01791     0.7605  5.7793
## 100 {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {BIM 360 Glue}                       0.01791     0.4130  6.6283
## 101 {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.02306     0.9336  1.4463
## 102 {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.02306     0.7342  1.3957
## 103 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.02306     0.7478  5.6830
## 104 {AutoCAD WS,                                                                                                            
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01711     0.9761  1.5120
## 105 {Autodesk 360,                                                                                                          
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01711     0.7265  1.3811
## 106 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.01711     0.5550  2.5070
## 107 {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {Autodesk 360}                       0.03030     0.9707  1.5037
## 108 {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {AutoCAD WS}                         0.03030     0.6986  1.3280
## 109 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS)}                           => {Autodesk 360 Rendering}             0.03030     0.4254  1.9214
## 110 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Rendering}                              => {Autodesk 360 Mobile (iOS)}          0.03030     0.4204  3.1948
## 111 {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01021     0.9504  1.4722
## 112 {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01021     0.7701  1.4639
## 113 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk BIM 360 Field,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01021     0.8562  6.5067
## 114 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk BIM 360 Field}                              => {BIM 360 Glue}                       0.01021     0.8272 13.2750
## 115 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk BIM 360 Field}             0.01021     0.4430 11.4745
## 116 {AutoCAD WS,                                                                                                            
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360}                       0.01421     0.9868  1.5286
## 117 {Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {AutoCAD WS}                         0.01421     0.7936  1.5086
## 118 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      BIM 360 Glue}                                        => {Autodesk 360 Rendering}             0.01421     0.6165  2.7849
## 119 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Rendering,                                                                                                
##      BIM 360 Glue}                                        => {Autodesk 360 Mobile (iOS)}          0.01421     0.8307  6.3129
## 120 {AutoCAD WS,                                                                                                            
##      Autodesk 360,                                                                                                          
##      Autodesk 360 Mobile (iOS),                                                                                             
##      Autodesk 360 Rendering}                              => {BIM 360 Glue}                       0.01421     0.4692  7.5299
```

