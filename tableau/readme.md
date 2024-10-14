- [Tips](#tips)
  - [Quick Access Field \& Local Change](#quick-access-field--local-change)
  - [Wrap DATE function to other Date functions](#wrap-date-function-to-other-date-functions)
  - [Use TODAY() Reference Line in chart](#use-today-reference-line-in-chart)
  - [Preview Source Data In Worksheet](#preview-source-data-in-worksheet)
  - [ATTR Function Use Case \& Emblem worksheet](#attr-function-use-case--emblem-worksheet)
  - [Hide Chart Grid Line](#hide-chart-grid-line)
  - [Show/Hide Card](#showhide-card)
  - [Format Number](#format-number)
  - [Fix values format because of continous column](#fix-values-format-because-of-continous-column)
- [Basic](#basic)
  - [Discrete \& Continuous](#discrete--continuous)
  - [Renaming Discrete Field Locally(Within Worksheet)](#renaming-discrete-field-locallywithin-worksheet)
  - [Alias](#alias)
  - [Create Hierachy](#create-hierachy)
  - [Drill Down \& Drill Up - Hierachy](#drill-down--drill-up---hierachy)
  - [Date Hierarchy](#date-hierarchy)
  - [Create Group](#create-group)
  - [Create Cluster Group](#create-cluster-group)
  - [Create Set](#create-set)
  - [Bins](#bins)
  - [Filtering Data](#filtering-data)
    - [Data Source Filter](#data-source-filter)
    - [Context Filter](#context-filter)
    - [Dimension Filter](#dimension-filter)
    - [Measure Filter](#measure-filter)
    - [Table Calculation Filter](#table-calculation-filter)
    - [Apply filters](#apply-filters)
  - [Disable Sorting](#disable-sorting)
  - [Parameters](#parameters)
    - [Dynamic Calculation](#dynamic-calculation)
    - [Dynamic Reference Line](#dynamic-reference-line)
    - [Dynamic Filters](#dynamic-filters)
    - [Dynamic Swap Dimensions/Measures](#dynamic-swap-dimensionsmeasures)
    - [Dynamic Title](#dynamic-title)
    - [Dynamic Bins](#dynamic-bins)
  - [Tableau Actions](#tableau-actions)
    - [Filter Action](#filter-action)
    - [Filter Highlight Action](#filter-highlight-action)
    - [Change Parameter Action](#change-parameter-action)
- [Tableau Calculations](#tableau-calculations)
  - [Logical Functions](#logical-functions)
    - [IF statement](#if-statement)
    - [SWITCH CASE](#switch-case)
    - [IIF](#iif)
  - [CEILING,FLOOR,ROUND](#ceilingfloorround)
  - [String Functions](#string-functions)
  - [Date Functions](#date-functions)
  - [Null Functions](#null-functions)
  - [LOD (Level Of Detail) Functions](#lod-level-of-detail-functions)
    - [FIXED](#fixed)
  - [Table Calculation Functions](#table-calculation-functions)
    - [First Use Case](#first-use-case)
    - [Running Total Use Case](#running-total-use-case)
    - [Difference Use Case](#difference-use-case)
- [Charts](#charts)
  - [Charts Types](#charts-types)
    - [Individual Axes](#individual-axes)
    - [Single Axes](#single-axes)
    - [Dual Axes](#dual-axes)
  - [Chart Examples](#chart-examples)
    - [Side by side bar chart](#side-by-side-bar-chart)
    - [Stack bar](#stack-bar)
    - [Stack Bar 2](#stack-bar-2)
    - [Full Stack Bar](#full-stack-bar)
    - [Multi Small Bar Charts](#multi-small-bar-charts)
    - [Bar-in-bar chart](#bar-in-bar-chart)
    - [Barcode chart](#barcode-chart)
    - [Multi Lines Chart](#multi-lines-chart)
    - [Highlighted Line Chart(1)](#highlighted-line-chart1)
    - [Highlighted Line Chart(2)](#highlighted-line-chart2)
    - [Bump Chart](#bump-chart)
    - [Barbell Chart](#barbell-chart)
    - [Barbell Chart 2](#barbell-chart-2)
    - [Rounded Bar Chart](#rounded-bar-chart)
    - [Slope Chart](#slope-chart)
    - [Bullet Chart](#bullet-chart)
    - [Lollipop Chart(H)](#lollipop-charth)
    - [Lollipop Chart(V)](#lollipop-chartv)
    - [Basic Scatter Plot](#basic-scatter-plot)
    - [Customized Scatter Plot](#customized-scatter-plot)
    - [Dot plot](#dot-plot)
    - [Circle Timeline](#circle-timeline)
    - [Dotnut Chart](#dotnut-chart)
    - [Tree Map](#tree-map)
    - [Bublle Chart](#bublle-chart)
    - [Stacked Bublle Chart](#stacked-bublle-chart)
    - [Dark Border Map](#dark-border-map)
    - [Histogram](#histogram)
    - [Calendar Chart](#calendar-chart)
    - [Waterfall Chart](#waterfall-chart)
    - [Pareto Chart](#pareto-chart)
    - [Pareto Chart 2](#pareto-chart-2)
    - [Butterfly Chart(1)](#butterfly-chart1)
    - [Butterfly Chart(2)](#butterfly-chart2)
    - [Quadrant Chart(Dynamic)](#quadrant-chartdynamic)
    - [KPI Chart](#kpi-chart)
    - [KPI \& Bar Chart](#kpi--bar-chart)
    - [BANS](#bans)
    - [Progress Bar Chart](#progress-bar-chart)
- [Advances](#advances)
  - [Toggle Charts By Actions](#toggle-charts-by-actions)


# Tips

## Quick Access Field & Local Change
Local Change mean the changes wont effect other sheets
![1-95](assets/1-95.gif)

## Wrap DATE function to other Date functions
This will ensure the date is present in Date format instead of string
![1-96](assets/1-96.png)

## Use TODAY() Reference Line in chart
![1-97](assets/1-97.gif)

## Preview Source Data In Worksheet
![1-100](assets/1-100.png)

## ATTR Function Use Case & Emblem worksheet
![1-101](assets/1-101.png)
![1-102](assets/1-102.gif)

## Hide Chart Grid Line
![1-132](assets/1-132.gif)

## Show/Hide Card
![1-137](assets/1-137.gif)

## Format Number
![1-206](assets/1-206.png)

## Fix values format because of continous column

**Summary:** wrap field value with `{}`

![1-207](assets/1-207.gif)



# Basic

## Discrete & Continuous
![1-201](assets/1-201.png)
![1-202](assets/1-202.png)
![1-203](assets/1-203.png)
![1-204](assets/1-204.png)
![1-205](assets/1-205.png)

## Renaming Discrete Field Locally(Within Worksheet)

![1-1](assets/1-1.gif)

## Alias

![1-2](assets/1-2.jpg)

**Notes**
1. Only Worksheet can create alias
2. Alias work globally for all sheets

**Steps**
1. Right click the field you want to create alias, and select **Aliases** option
2. Edit Alias
![1-3](assets/1-3.jpg)

**Recommended Way to create Aliases**
Duplicate the field first, then make the aliases on that duplicate field
**Right Click the field -> Duplicate**
![1-4](assets/1-4.jpg)
Duplicate field always start `=` sign in front of data type

## Create Hierachy

![1-6](assets/1-6.png)
![1-5](assets/1-5.png)

**Notes**
1. Only can create at worksheet page
2. Hierary only can create for dimension field but not continuous field

![1-7](assets/1-7.png)

**Steps create hierarchy**
1. Select the top level hierachy field, for this case, it will be `Country`
2. Right click -> Hierarchy ->　Create Hierarchy -> Give Hierarchy Name
3. And then just drag and drop the second,third or even more of other level fields inside hierarchy just created.

**Easy Method**
Drag and drop one of the field to another field, then it will pop out create hierarchy window
![1-8](assets/1-8.png)

## Drill Down & Drill Up - Hierachy

For those which is hierarchy field and have child nodes, it will show `+` sign
![1-9](assets/1-9.png)

After click `+` icon(drill down icon)
![1-10](assets/1-10.png)

Vice versa, `-` icon mean drill up

## Date Hierarchy
![1-11](assets/1-11.png)

## Create Group

**Steps**
1. Worksheet Panel
2. Right click the field for group created -> Create -> Group -> Give Group Name(Field Name)
3. Select the items to group them together
![1-12](assets/1-12.png)
![1-13](assets/1-13.png)

You can include `Group` inside the hierarchy
![1-14](assets/1-14.png)

## Create Cluster Group
![1-15](assets/1-15.png)

**Steps**
![1-16](assets/1-16.png)

[Ref](https://youtu.be/K3pXnbniUcM?t=20092)
![1-17](assets/1-17.png)

## Create Set
![1-18](assets/1-18.png)
![1-19](assets/1-19.png)
![1-20](assets/1-20.png)

**Steps For Fix Set**
Right Click Field -> Create -> Set -> General Tab -> Select items(In below case select id 2 & 5)
![1-21](assets/1-21.png)

**Steps For Dynamic Set(Condition)**
Right Click Field -> Create -> Set -> Condition Tab -> set condition(below case is if score is > 400)
![1-22](assets/1-22.png)

**Steps For Dynamic Set(Top)**
Right Click Field -> Create -> Set -> Top Tab -> set top condition

**Steps For Create Combine Set**
Right Click on any `Set Type Field` -> Create Combined Set

**Quick create set**
![1-79](assets/1-79.png)

## Bins
Bins divide the data into groups of equally sized containers resulting in systematic distribution of the data and we can use those pens to create charts(Histogram).

![1-23](assets/1-23.png)
![1-24](assets/1-24.png)
![1-25](assets/1-25.png)

**Notes**
- Bins only work for measure field but not dimension field
- Better convert them into `continuous measures`
- Calculated fields cannot be used to create bins.
- Histogram in statistic show the frequency of data within a certain range

**Create bins**
Right click field -> Create -> Bins
![1-26](assets/1-26.png)
![1-27](assets/1-27.png)

## Filtering Data
![1-28](assets/1-28.png)
![1-29](assets/1-29.png)
![1-30](assets/1-30.png)
![1-31](assets/1-31.png)

### Data Source Filter
Data Source Page right top corner
![1-32](assets/1-32.png)

### Context Filter
![1-33](assets/1-33.png)
![1-34](assets/1-34.png)
Add to Context nothing change in surface except turn filter indicate to grey. However, it does change something on background to improve the performance.
![1-35](assets/1-35.png)

Context Filter cannot protect(restrict) specific sensitive or confidential data, it only work on data source filter.

### Dimension Filter
Everything inside `Edit Filter` is Dimension Filter
![1-36](assets/1-36.png)

### Measure Filter
As name imply, only work for measure field
![1-37](assets/1-37.png)

### Table Calculation Filter
Create table calculation
![1-38](assets/1-38.png)

Then drag and drop to filter zone, this will become table calculation filter

### Apply filters
![1-39](assets/1-39.png)
![1-40](assets/1-40.png)

## Disable Sorting
![1-41](assets/1-41.png)

## Parameters

### Dynamic Calculation
![1-47](assets/1-47.png)

**Simple Example**

Original Viz
![1-43](assets/1-43.png)

Right click data panel -> Create Calculated Field
![1-42](assets/1-42.png)

Insert logic
![1-44](assets/1-44.png)

Drag to color for created calculated field
![1-45](assets/1-45.png)

Edit color
![1-46](assets/1-46.png)

Now create new parameter for logic update later
![1-48](assets/1-48.png)

![1-49](assets/1-49.png)

You can show parameter for user to edit
![1-50](assets/1-50.png)

Update KPI CAL calculated field
![1-51](assets/1-51.png)

### Dynamic Reference Line
![1-52](assets/1-52.png)
![1-53](assets/1-53.png)

### Dynamic Filters

Original Viz
![1-57](assets/1-57.png)

Drag using CTRL
![1-54](assets/1-54.png)

Create Param for top filter
![1-55](assets/1-55.png)

Show parameters and make filter adjustable
![1-56](assets/1-56.png)

### Dynamic Swap Dimensions/Measures
![1-57](assets/1-57.gif)

Create Parameters For Dimension Field(Country/Category)
![1-58](assets/1-58.png)

Create Calculate Field for dimension
![1-59](assets/1-59.png)

Drag created calculate field to viz and then show created parameters
![1-60](assets/1-60.png)

Create Parameters For Measure Field(Sales,Quantity,Profit)
![1-61](assets/1-61.png)

Create Calculate Field for measures
![1-62](assets/1-62.png)

Drag and replace SUM(Sales) to SUM(Dynamic Measures)
![1-63](assets/1-63.png)

### Dynamic Title
![1-64](assets/1-64.png)

### Dynamic Bins
![1-65](assets/1-65.png)
![1-66](assets/1-66.png)
![1-67](assets/1-67.png)

## Tableau Actions
![1-68](assets/1-68.png)

![1-69](assets/1-69.png)

![1-70](assets/1-70.png)

### Filter Action

Let say we have two sheets in dashboard
![1-71](assets/1-71.png)

Add Action to dashboard. This action mean, when the bar on `Sale Insign Sheet` was selected, the selected values will become the filter values to `Profit Insight Sheet` graph.
![1-72](assets/1-72.png)

Try select on Sale Insign Sheet and see output
![1-73](assets/1-73.png)

You can use quick filter by hover the sheet, this will auto generate a filter action to you
![1-74](assets/1-74.png)

### Filter Highlight Action
![1-75](assets/1-75.png)
![1-76](assets/1-76.png)

Another highlight method is add highlighther
![1-77](assets/1-77.png)
![1-78](assets/1-78.png)

### Change Parameter Action

Result expected: Select range of bar/points and get total sum.

First Sheet
![1-80](assets/1-80.png)

Create Parameter
![1-82](assets/1-82.png)

Create Calculate Field
![1-83](assets/1-83.png)

Create Action
![1-81](assets/1-81.png)
![1-85](assets/1-85.png)

Put two sheet in dashboard
![1-84](assets/1-84.png)

Select and total sales values update
![1-86](assets/1-86.png)

Update total sales format
![1-87](assets/1-87.png)


# Tableau Calculations

## Logical Functions
![1-99](assets/1-99.png)

### IF statement
```
IF [LOGIC] THEN [RESULT]
ELSEIF [LOGIC2] THEN [RESULT2]
ELSE [RESULT3]
END
```

### SWITCH CASE
```
CASE [FIELD]
WHEN [VALUES] THEN [RESULT]
WHEN [VALUES2] THEN [RESULT2]
END
```

### IIF
```
IIF([LOGIC],[RESULT IF TRUE],[RESULT IF FALSE])
```

## CEILING,FLOOR,ROUND
![1-88](assets/1-88.png)

## String Functions
![1-89](assets/1-89.png)

## Date Functions
![1-90](assets/1-90.png)
![1-91](assets/1-91.png)
![1-92](assets/1-92.png)
![1-93](assets/1-93.png)

**Notes**
Be sure change it to Exact Date/Exact Value. When you drag drop any field, it will be assign default function, like if we drag and drop Sales Field, it will auto wrap in SUM(Sales) and display in Viz.
![1-94](assets/1-94.png)

## Null Functions
![1-98](assets/1-98.png)

## LOD (Level Of Detail) Functions

### FIXED
**Question**:
![1-103](assets/1-103.png)
![1-104](assets/1-104.png)

**Example(Check Frequency of order)**
![1-106](assets/1-106.png)

Base on above screenshot, we want to see the frequency of order make, for example, there is **3 orders make** by **2 customer**

![1-107](assets/1-107.png)

## Table Calculation Functions
![1-108](assets/1-108.png)

### First Use Case
![1-109](assets/1-109.gif)

### Running Total Use Case
![1-110](assets/1-110.png)

**Question: How to merge them to one?**
![1-111](assets/1-111.gif)

### Difference Use Case
![1-112](assets/1-112.png)

![1-113](assets/1-113.png)

![1-114](assets/1-114.gif)

# Charts

![1-115](assets/1-115.png)

## Charts Types

### Individual Axes
Different measures display in different chart
![1-116](assets/1-116.png)

### Single Axes
Two or more meaures display in same chart
![1-117](assets/1-117.png)

Quick way combine measure to viz:
![1-118](assets/1-118.gif)

### Dual Axes
![1-119](assets/1-119.png)

Two way create dual axes:
![1-120](assets/1-120.gif)

**Different between Single and Dual Axes**
- Single Axes merge multi measures in one chart. There no options are available to customize each measure in the chart
- Dual Axes put two measures in one chart. There have options to customize each measure in the chart. But it only two measures are allow in a chart.

## Chart Examples

### Side by side bar chart
![1-121](assets/1-121.png)

### Stack bar
![1-122](assets/1-122.gif)

### Stack Bar 2
![1-209](assets/1-209.gif)

### Full Stack Bar
![1-156](assets/1-156.png)

![1-123](assets/1-123.gif)

### Multi Small Bar Charts
![1-124](assets/1-124.png)

![1-125](assets/1-125.gif)

### Bar-in-bar chart
![1-126](assets/1-126.png)

![1-127](assets/1-127.gif)

### Barcode chart
![1-128](assets/1-128.png)

![1-129](assets/1-129.gif)

### Multi Lines Chart
![1-130](assets/1-130.png)

![1-131](assets/1-131.gif)

### Highlighted Line Chart(1)
![1-133](assets/1-133.png)

![1-134](assets/1-134.gif)

### Highlighted Line Chart(2)

![1-148](assets/1-148.png)

1. Create Parameter
![1-134](assets/1-135.png)

2. Create Calculate Field
![1-136](assets/1-136.png)

![1-138](assets/1-138.gif)

### Bump Chart
![1-147](assets/1-147.png)

**Use Case**:Ranking
![1-139](assets/1-139.gif)

### Barbell Chart
![1-146](assets/1-146.png)

**Use Case:** Show the distance or range(min/max) between two values accross multiple categories

Create two calculate fields before start
![1-140](assets/1-140.png)
![1-141](assets/1-141.png)

![1-142](assets/1-142.gif)

### Barbell Chart 2
**Second Method**
![1-208](assets/1-208.gif)

### Rounded Bar Chart
![1-145](assets/1-145.png)

![1-143](assets/1-143.gif)

### Slope Chart
**Use Case:** Live Ranking
![1-144](assets/1-144.png)

![1-149](assets/1-149.gif)

### Bullet Chart

**Use Case:** Compare two year data

![1-150](assets/1-150.png)

![1-151](assets/1-151.gif)

### Lollipop Chart(H)

![1-152](assets/1-152.png)

![1-153](assets/1-153.gif)

### Lollipop Chart(V)

Same steps as Lollipop Chart(H), just swap rows and columns

![1-154](assets/1-154.png)

### Basic Scatter Plot

**Use Case:** Correlation and compare two measures
![1-157](assets/1-157.png)

### Customized Scatter Plot
![1-158](assets/1-158.png)

![1-159](assets/1-159.gif)

### Dot plot
![1-160](assets/1-160.png)

### Circle Timeline
![1-161](assets/1-161.png)

### Dotnut Chart
![1-162](assets/1-162.png)

![1-163](assets/1-163.gif)

### Tree Map
![1-164](assets/1-164.png)

### Bublle Chart
![1-165](assets/1-165.png)

### Stacked Bublle Chart
![1-166](assets/1-166.png)

### Dark Border Map
![1-167](assets/1-167.png)

![1-168](assets/1-168.gif)

### Histogram

![1-169](assets/1-169.png)

![1-170](assets/1-170.png)

### Calendar Chart
![1-171](assets/1-171.png)

### Waterfall Chart
![1-172](assets/1-172.png)


![1-173](assets/1-173.gif)

### Pareto Chart

**Pareto Principle 80/20 Rule**
`80%` of consequences come from `20%` of the causes

![1-174](assets/1-174.png)

![1-175](assets/1-175.gif)

**Analysis Data:**
![1-176](assets/1-176.png)

### Pareto Chart 2
![1-178](assets/1-178.png)

![1-178](assets/1-178.gif)

**Analysis Data:**
![1-179](assets/1-179.png)

### Butterfly Chart(1)
![1-180](assets/1-180.png)

![1-181](assets/1-181.gif)

### Butterfly Chart(2)
![1-182](assets/1-182.png)

![1-183](assets/1-183.gif)

### Quadrant Chart(Dynamic)

![1-188](assets/1-188.png)

Before start, create 2 parameters and 2 calculate field
![1-184](assets/1-184.png)
![1-185](assets/1-185.png)

![1-187](assets/1-187.png)
![1-186](assets/1-186.png)

![1-189](assets/1-189.gif)

### KPI Chart
![1-190](assets/1-190.png)

Create 1 calculate field before start:
![1-191](assets/1-191.png)

![1-192](assets/1-192.gif)

### KPI & Bar Chart
![1-194](assets/1-194.png)

Need prepare three calculate field before start:
![1-193](assets/1-193.png)
![1-195](assets/1-195.png)
![1-196](assets/1-196.png)

![1-219](assets/1-219.gif)

### BANS
![1-198](assets/1-198.png)

### Progress Bar Chart
![1-199](assets/1-199.png)

![1-200](assets/1-200.gif)

# Advances

## Toggle Charts By Actions

**Demo**
![1-210](assets/1-210.gif)

**Steps**
1. Prepare datasets for filters. For this case, i prepare 2 options: `Quarter` and `Year`. Be note load you dataset in different data source(Filter_Opts).
![1-211](assets/1-211.png)
2. Create Toggle View with button pattern
![1-212](assets/1-212.png)
**Few thing to be note:**
- **LEFT([GroupBy],1)** mean take first character, like Quarter = `Q`
- **Update Labels Alignment**: Make horizotal alignment as `center`
3. Create calculate field for toggle function
![1-213](assets/1-213.png)
**PS:** Make sure this calculate field create at main datasource
4. Now we need to prepare two chart. Let say chart1 show quarterly data and chart2 show yearly data.


Let say param default value is 'Quarter', so let walk throught to chart1(quarter) first.

Drag and Drop [Toggle-Trend Switch] to Filters fields, then you will see `1` in the filter options.
As we state in calc field, `1` mean 'Quarter'
Tick it to make chart1 only show if param values is 'Quarter'
![1-214](assets/1-214.png)

Similar to chart1, chart2 doing the same. You have to manually change the parameter values to enable see `2` in the filter list.
`Show Parameter` and change option(From Quarter to Year)
![1-215](assets/1-215.png)
Drag and Drop [Toggle-Trend Switch] to Filters fields, then you will see `2` in the filter options.
As we state in calc field, `2` mean 'Year'
Tick it to make chart1 only show if param values is 'Year'
![1-216](assets/1-216.png)

5. Viz Layout
Put two chart in Horizontal Container
![1-217](assets/1-217.png)

6. Add new action
![1-218](assets/1-218.png)