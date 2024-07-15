---
id: litvis

narrative-schemas:
  - ../../lectures/narrative-schemas/project.yml

elm:
  dependencies:
    gicentre/elm-vegalite: latest
    gicentre/tidy: latest
---

@import "../../lectures/css/datavis.less"

```elm {l=hidden}
import Tidy exposing (..)
import VegaLite exposing (..)
```

# Data Visualization Project Summary

{(whoami|} Taha Jomehpour Arashlou
taha.jomehpour-arashlou@city.ac.uk {|whoami)}

{(task|}

You should complete this datavis project summary document and submit it, along with any necessary supplementary files to **Moodle** by **Sunday 17th December, 5pm UK time**. Submissions will be awarded up to **80 marks** towards your coursework assessment total.

You are also encouraged to regularly commit and push changes to your datavis project throughout the term as you develop your project.

{|task)}

{(questions|}

- how does the london housing infrastructure differs to other regions of the country and what can be the reason?
- how the price of property in each region grew and how does that correlate with the demand ?
-

{|questions)}

{(visualization|}

**V1) population density bar chart**
per km

```elm { v interactive  }
valueBar : Spec
valueBar =
    let
        data =
            dataFromUrl "https://TahaJomehpour1.github.io/datavisdata/accoRegV5.csv"

        enc =
            encoding
                << position X [ pName "areaName", pNominal ]
                << position Y
                    [ pName "popDensityAvg"
                    , pQuant
                    ]
                << color
                    [ mName "pop"
                    , mQuant
                    , mScale [ scScheme "reds" [] ]
                    ]
                << tooltips
                    [ [ tName
                            "pop"
                      ]
                    ]
    in
    toVegaLite
        [ width 700
        , height 400
        , data []
        , enc []
        , bar []
        ]
```

**V2) value distribution**

```elm {v }
pie : Spec
pie =
    let
        data =
            dataFromUrl "https://TahaJomehpour1.github.io/datavisdata/accoRegV5.csv"

        cfg =
            configure
                << configuration (coView [ vicoStroke Nothing ])

        enc =
            encoding
                << position Theta [ pName "value", pQuant ]
                << color [ mName "areaName" ]
    in
    toVegaLite [ cfg [], data [], enc [], arc [] ]
```

**V3) comparission of housing**
instruction: by hovering over each bar you can see its exact value, percentage of its region and percentage of its category.

```elm {v interactive }
facetBarsReg1 : Spec
facetBarsReg1 =
    let
        data =
            dataFromUrl "https://TahaJomehpour1.github.io/datavisdata/accoRegV5.csv"

        enc =
            encoding
                << position X [ pName "category", pNominal, pTitle "category" ]
                << position Y [ pName "value", pAggregate opSum, pTitle "Number of Accomodation" ]
                << row [ fName "areaName" ]
                << color
                    [ mName "pop"
                    , mQuant
                    , mScale [ scScheme "yellowgreenblue" [] ]
                    ]
                << tooltips
                    [ [ tName
                            "value"
                      ]
                    , [ tName
                            "avgPerReg"
                      ]
                    , [ tName "avgPerCat" ]
                    ]
    in
    toVegaLite
        [ height 120
        , width 500
        , data []
        , bar []
        , enc []
        ]
```

**V4) price of property in time**
you can move or zoom in the chart

```elm { v interactive }
scaleDateInterval : Spec
scaleDateInterval =
    let
        data =
            dataFromUrl "https://TahaJomehpour1.github.io/datavisdata/avgPriceAllP.csv" []

        ps =
            params
                << param "scaling"
                    [ paSelect seInterval [ seEncodings [ chX ] ]
                    , paBindScales
                    ]
                << param "label"
                    [ paSelect sePoint
                        [ seNearest True, seOn "mouseover", seEncodings [ chX ] ]
                    ]

        enc =
            encoding
                << position X [ pName "period", pTemporal, pTitle "" ]
                << position Y [ pName "avgPrice", pQuant ]
                << color [ mName "region", mScale [ scScheme "category10" [] ] ]
    in
    toVegaLite
        [ width 650
        , height 300
        , data
        , ps []
        , enc []
        , line [ maInterpolate miMonotone ]
        ]
```

**V5) comparission of sale and average price between 2000 to 2021**
by clicking you can select a frame of the chart and see the highlighted data.

```elm {v interactive}
priceDemand : Spec
priceDemand =
    let
        data =
            dataFromUrl "https://TahaJomehpour1.github.io/datavisdata/avgPriceAllP.csv" []

        ps =
            params
                << param "myBrush" [ paSelect seInterval [] ]

        enc =
            encoding
                << position X [ pName "Sales volume", pQuant ]
                << position Y [ pName "avgPrice", pQuant ]
                << shape [ mName "region" ]
                << color
                    [ mCondition (prParam "myBrush")
                        [ mName "region", mScale [ scScheme "category10" [] ] ]
                        [ mStr "grey" ]
                    ]
    in
    toVegaLite
        [ width 600
        , height 500
        , data
        , ps []
        , enc []
        , point [ maFilled True, maSize 160, maOpacity 0.6 ]
        ]
```

{|visualization)}

{(insights|}

RQ1) to be able to answer and expand on this question I wil go through **visualisations number 1, 2 and 3**,by looking at the first graph we can see that London has around 5,700 residents living in 1 KM and other regions are between 200 to 500 residents per KM with wales being the lowest in data, which shows london has 10 to 20 times higher population density than other regions.
one more thing that we can take away form population density bar graph is that if we look at South East region we can see it has a higher population with around 9,200,000 whoch is around 400,000 higher than london but its population density is extremely lower.
this can be related to land mass of the region and people tend to live in a smaller communites in the region.
by looking at **value distribution graph** we can see that almost all of the regions have the same amount of property and the largest one is North East followed by London and the lowest amount of properties is in north east followed by wales which is reasonable as they both have the lowest population between the regions.
in **comparission of housing** it showcases to us that london has a much higher rate of apartments compared to other regions, apartments take about 40% of the whole london and accumaltes to around 33% of the whole apartments in england and wales.
if we look closer to north west and south east which they had the highest population density after London, in this chart we can see that they have a higher apatment rates compared to other parts.
it seems that higher populaton density has an effect apartments make up a larger portion of the properties in that region.

**V4) price of property in time** shows us how price of properties in 5 regions of england has changed from 1st of Jan 2000 to 1st of jan 2001,
we can see that London alwasy had a higher price compared to the other regions, South East, South West, West Midlands and North East in order with each around £20,000 diffrence in price in 2001 so for example north east had a price of £50,000 in Jan 2001 and London had around £130,000 average price of a property.
the graph shows a steady growth of all regions at a similar rate, but during beggining of 2009 which property prices were at all time high dropped significantly but after a year it came back to the previous track but london had a higher rate of growth after 2009 and in 2021 we can see London prices compared to north East has a major diffrence of £350,000 london at 500,000 and north east at 150,000 so we can see how london had a much higher rate of growth after 2009 compared to others.

by going through Visualisation 5 we can see with high prices prices set in london there are not much of sale within the region compared to south east with a lower price but higher sales.
we can also have a look at range of 150,000 to 200,000 with sales of 6,000 to 8,000 we can see most of them are from west midlands and south west.

{|insights)}

{(designJustification|}

colours: I have decided to use colours to distinguish areaNames and value of a data to an extent in my first, second and third vis.

interaction:
I have used tool tip in my 1st adn 3rd vis to showcase the data the additional data needed.
I have also used zoom and scale on my 4th vis to be able to precisely look through the data and look at a specific place in the data.
in my last vis I have used set intervals to be able to choose a window to be able to see how the data is scattered in that specific location.

initially I wanted to use a normal bar graph instead of my 3rd vis but I decided to go with facet bars as i could compare the data better with the same surface.
**initial vis**

```elm { v interactive}
valueBar1 : Spec
valueBar1 =
    let
        data =
            dataFromUrl "https://TahaJomehpour1.github.io/datavisdata/accoRegV5.csv"

        enc =
            encoding
                << position X [ pName "areaName", pNominal ]
                << position Y
                    [ pName "value"
                    , pQuant
                    ]
                << color [ mName "category", mScale [ scScheme "rainbow" [] ] ]
                << tooltips
                    [ [ tName
                            "category"
                      ]
                    , [ tName
                            "value"
                      ]
                    , [ tName
                            "avgPerReg"
                      ]
                    , [ tName "avgPerCat" ]
                    ]
    in
    toVegaLite
        [ width 700
        , height 350
        , data []
        , enc []
        , bar []
        ]
```

{|designJustification)}

{(references|}

Uk gov house prices index: https://www.gov.uk/government/news/uk-house-price-index-for-december-2021

vega github colour scheme: https://vega.github.io/vega/docs/schemes/#accent
{|references)}

land registrery gov uk: https://landregistry.data.gov.uk/app/ukhpi

ONS accomodation Data : https://www.ons.gov.uk/census/census2021dictionary/variablesbytopic
ONS pop Data : https://www.ons.gov.uk/census/census2021dictionary/variablesbytopic
ONS pop density Data : https://www.ons.gov.uk/census/census2021dictionary/variablesbytopic

```

```
