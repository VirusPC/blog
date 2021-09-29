---
---

A Tour through the Visualization Zoo
===

1. [Abstract](#abstract)
2. [Time-Series Data](#time-seriesdata)
    1. [Index Charts](#index-Charts)
    2. [Stacked Graphs (Stream Graphs)](#stacked-graphs)
    3. [Small Multiples](#small-multiples)
    4. [Horizon Graphs](#horizon-graphs)
3. [Statistical Distributions](#statistical-distributions)
    1. [Histograms]()
    2. [Box and Whisker Plots]()
    3. [Stem-and-Leaf Plots]()
    4. [Q-Q Plots]()
    5. [SPLOM (Scatter Plot Matrix)]()
    6. [Parallel Coordinates]()
4. [Maps]()
    1. [Flow Maps]()
    2. [Choropleth Maps]()
    3. [Graduated Symbol Maps]()
    4. [Cartograms]()
5. [Hierarchies]()
    1. [Node-link Diagrams]()
        1. [Reingold-Tiford algorithm]()
        2. [Dendrogram algorithm (cluster algorithm)]()
        3. [indented tree]()
    2. [Ajacency Diagrams]()
        1. [icicle layout]()
        2. [sunburst layout]()
    3. [Enclosure Diagrams]()
        1. [Squarified layout]()
        2. [circle-packing layout]()
6. [Networks]()
    1. [Force-directed Layouts]()
    2. [Arc Diagrams]()
    3. [Matrix Views]()
7. [Conclusion]()
8. [References]()

---

Abstract
---

*Visual encode* is to map data values to graphical features such as position, size, shape, and color.

The challenge is that for any given data set the number of visual codings--and thus the space of possible visualization designs--is extremely large.

To guid this process, people have studied how well different encodings facilitate the comprehension of data types such as numbers, categories, and networks.

Our understanding of graphical perception remains incomplete, however, and must appropriately be balanced with **interaction** and aesthetics.

This article provides a brief tour through the "visualization zoo," **showcasing techniques for visualization and interactiong with diverse data sets**. Here we focus on a few of the more sophisticated and unusual techniques that deal with complex data sets. 

We starting with one of the most common, time-series data; continuing on to statical data and maps; and then completing the tour with hierarchies and networks.

All visualizations share a common "DNA"--**a set of mappings between data properties and visual attributes** such as position, size, shape and color--and that customized species of visualization might always be constructed by varying these encoding.


Time-Serires Data
---

Time-series data—sets of values changing over time—is one of the most common forms of recorded data. One often needs to compare a large number of time series simultaneously and can choose from a number of visualizations to do so.  

### Index Charts ###  

dynamic

With some forms of time-series data, raw values are less important than relative changes. An index chart is an interactive *line chart* that shows percentage changes for a collection of time-series data based on a selected index point. 

### Stacked Graphs ###  

stastic

Also called *stream graph*

By stacking area charts on top of each other, we arrive at a visual summation of time-series values—a *stacked graph*. 

This type of graph depicts aggregate patterns and often supports drill-down into a subset of individual series.  

They do have some notable limitations. A stacked graph does not support negative numbers and is meaningless for data that should not be summed (temperatures, for example). Moreover, stacking may make it difficult to accurately interpret trends that lie atop other curves.  Interactive search and filtering is often used to compensate for this problem.

### Small Multiples ###

stastic or dynamic

showing each series in its own chart. 

Placing multiple series in the same space may produce overlapping curves that reduce legibility. We can now more accurately see both overall trends and seasonal patterns in each sector. 

small multiples can be constructed for just about any type of visualization: bar charts, pie charts, maps, etc. This often produces a more effective visualization than trying to coerce all the data into a single plot.

### Horizon Graphs ###

stastic or dynamic

compare even more time series at once.

a chart that preserves data resolution but uses only part of the space.

The horizon graph is a technique for increasing the **data density** of a time-series view while preserving resolution. 

it has been found to be more effective than the standard plot when the chart sizes get quite small.


Statistical Ditributions
---

Other visualizations have been designed to reveal how a set of numbers is distributed and thus help an analyst better understand the statistical properties of the data. 

Analysts often want to fit their data to statistical models, either to test hypotheses or predict future values, but an improper choice of model can lead to faulty predictions. Thus, one important use of visualizations is exploratory data analysis: gaining insight into how data is distributed to inform data transformation and modeling decisions.

A number of techniques exist for assessing a distribution and examining interactions between multiple dimensions.

### Histograms ###

Shows the prevalence of values grouped into bins.

paint a frequency distribution.

### Box-and-Whisker Plots ###

Convey statistical features such as the mean, median, quartile boundaries, or extreme outliers.


### Stem-and-Leaf Plots ###

For assessing a collection of numbers, one alternative to the *histogram* is the *stem-and-leaf plot*. It typically bins numbers according to the first significant digit, and then stacks the values within each bin by the second significant digit. 

This minimalistic representation uses the data itself to **paint a frequency distribution**, replacing the "information-empty" bars of a traditional histogram bar chart and allowing one to assess both the overall distribution and the contents of each bin. 


茎叶图的思路是将数组中的数按位数进行比较，将数的大小基本不变或变化不大的位作为一个主干（茎），将变化大的位的数作为分枝（叶），列在主干的后面，这样就可以清楚地看到每个主干后面的几个数，每个数具体是多少

### Q-Q Plots ###

the Q-Q (quantile-quantile) plot is a more powerful tool for assessing a frequency distribution.

The Q-Q plot compares two probability distributions by graphing their quantiles against each other. If the two are similar, the plotted values will lie roughly along the central diagonal. If the two are linearly related, values will again lie along a line, though with varying slope and intercept.

You can compare your data with statistical distributions.

a statistical model with three components might be more appropriate.

Though powerful, the Q-Q plot has one obvious limitation in that its effective use requires that viewers possess some statistical knowledge

**To determine which distribution it is more like.**

### SPLOM (Scatter Plot Matrix) ###

Other visualization techniques attempt to represent the relationships among multiple variables. Multivariate data occurs frequently and is notoriously hard to represent, in part because of the difficulty of mentally picturing data in more than three dimensions. One technique to overcome this problem is to use small multiples of scatter plots showing a set of pairwise relations among variables, thus creating the SPLOM (scatter plot matrix). A SPLOM enables visual inspection of correlations between any pair of variables.

 Additionally, interaction techniques such as brushing-and-linking—in which a selection of points on one graph highlights the same points on all the other graphs—can be used to explore patterns within the data.

 ### Parallel Coordinates ###

 Parallel coordinates (||-coord), shown in figure 2D, take a different approach to visualizing multivariate data. 


 ---

 References：
 [https://queue.acm.org/detail.cfm?id=1805128]()