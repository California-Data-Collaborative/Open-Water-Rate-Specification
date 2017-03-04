
# Open Water Rate Specification

This repository documents the **Open Water Rate Specification** (OWRS), a machine-readable format for specifying and sharing water rate information.

OWRS is designed for analysts, economists, and software developers interested in analyzing water rates. OWRS attempts to fully encode a water utility's rate structure and pricing schedules in a form that is easy to store, share, modify and apply programmatically.

### Table of Contents

* [Why a Standard for Sharing Water Rates?](#why)
   - [Benefits of OWRS](#benefits)
   - [Alliance for Water Efficiency - Building a better (efficiency-oriented) rate structure] (https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/blob/master/AWE-Building-a-better-RateStructure.pdf)
* [Getting Started](#getting-started)
   - [Example 1 - Simple Flat Rate](#example1)
   - [Example 2 - Fixed Service Charge](#example2)
   - [Example 3 - Fixed Service Charge that Depends on Meter Size](#example3)
   - [Example 4 - Tiered Rates](#example4)
   - [Example 5 - Budget Based Rates](#example5)
   - [Example 6 - Other Cases](#example6)
* [Full Utility Examples](#utility-examples)

### Contribute

Please see the [CONTRIBUTING](CONTRIBUTING.md) file for details.

### Contact

Please reach out with comments, ideas or suggestions using the [issues page](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/issues), or [contact the CaDC](http://californiadatacollaborative.com/join-us/) directly.

----------------------------------------

## <a name="why"></a>Why a Standard for Sharing Water Rates?

Anyone familiar with water rates is likely familiar with the PDF documents, [HTML tables](https://www.smgov.net/Departments/PublicWorks/ContentWater.aspx?id=7743) and [images](https://www.mnwd.com/waterandwastewaterrates/) generally used to present water pricing information to the public. These formats are useful because they provide a concise representation that is easy for people to understand. However, these formats are often not ideal for analysts, consultants, economists or others interested in analyzing water prices for several reasons:

1. HTML tables may be easy for customers to understand but they are difficult for computer code to understand and usually must be manually translated into code before a rate analysis can be performed.

2. Sometimes the exact formulas used to calculate a water bill are not explicitly defined.

3. PDF and HTML formats are more difficult to store, transmit and interpret than plain text.

OWRS attempts to overcome these downsides by specifying a plain text format to fully specify a water rate structure. Specifically:

1. OWRS is based on [YAML](http://yaml.org/), and as such it is designed to be easy to store, transmit, and parse in any programming language while also being easy for humans to read.

2. All the details stating how to calculate a customer bill, including formulas and conditional charges are specified in a single flat file.

### <a name="benefits"></a>Benefits of OWRS

Machine-readability sounds nice on paper, but the real benefit of this standard is in all of the tools that it enables.

* [RateParser](https://github.com/California-Data-Collaborative/RateParser) is an [R package](https://www.r-project.org/about.html) that has the ability to interpret OWRS files and calculate water bills. The package is designed to simplify the work of analysts and economists interested in calculating water bills under a variety of rate structures.

* [RateComparison](https://github.com/California-Data-Collaborative/RateComparison) is a software program (written in [R Shiny](http://shiny.rstudio.com/)) that compares the revenue, equity, and demand implications of different water rate structures.

* In the future, this standard will form the foundation for a comprehensive database of water price information, similar in nature to the [Utility Rate Database](http://en.openei.org/wiki/Utility_Rate_Database) in the energy sector. When combined with customer billing data (like that centralized through the [CaDC](http://californiadatacollaborative.com/)), this will enable a detailed, inter-utility analysis of revenue stability and water price equity.

## <a name="getting-started"></a>Getting Started

Perhaps the best way to demonstrate how OWRS specifies a water rate is through an example. Let's consider the simplest possible OWRS file, representing a simple flat rate structure.

#### <a name="example1"></a>Example 1 - Simple Flat Rate
```yaml
---
metadata:
  effective_date: 2016-01-01
  utility_name: "Example Water District"
  bill_frequency: monthly
rate_structure:
  RESIDENTIAL_SINGLE:
    flat_rate: 2.1
    commodity_charge: flat_rate*usage_ccf
    bill: commodity_charge
```

The `yaml` file format works by specifying a series of keys and values. In the example above, `bill_frequency` is a key and `monthly` is the value corresponding to that key. This key-value pair is itself a value of the `metadata` key.

`metadata` is used to specify information about the rate structure that is not actualy used to calculate bills, such as the name of the utility and the date the rate structure went into effect.

`rate_structure` specifies information that is actually used for calculating water bills. The values of the `rate_structure` key are the customer classes used to define rates. In our examples we use the standard classes defined by the CaDC: `RESIDENTIAL_SINGLE`, `RESIDENTIAL_MULTI`, `IRRIGATION`, `COMMERCIAL`, `INDUSTRIAL`, and `INSTITUTIONAL`.

In Example 1 above, we see a rate structure defined only for single-family residential customers, where the commodity charge is calculated as `flat_rate*usage_ccf` where `flat_rate` is 2.1, so the whole expression represents $2.1 per CCF of water used. In this case `usage_ccf` is the data column name used to represent water usage. The total bill (`bill`) for each customer is then equal to just the commodity charge.

#### <a name="example2"></a>Example 2 - Fixed Service Charge
```yaml
---
metadata:
  ...
rate_structure:
  RESIDENTIAL_SINGLE:
    service_charge: 14.65
    flat_rate: 2.1
    commodity_charge: flat_rate*usage_ccf
    bill: commodity_charge+service_charge
```

This second example extends Example 1 by adding a fixed service charge. Note that `bill` is now calculated as the sum of `service_charge` and `commodity_charge`.

#### <a name="example3"></a>Example 3 - Fixed Service Charge that Depends on Meter Size
```yaml
---
metadata:
  ...
rate_structure:
  RESIDENTIAL_SINGLE:
    service_charge:
      depends_on: meter_size
      values:
         3/4": 14.65
         1"  : 16.77
         2"  : 25.83
    flat_rate: 2.1
    commodity_charge: flat_rate*usage_ccf
    bill: commodity_charge+service_charge
```

Examples 1 & 2 above are composed entirely of simple parts, referred to in this context as **Fields** (e.g. `flat_rate: 2.1`) or **Formulas** (e.g. `bill: commodity_charge+service_charge`). However, often in real settings is it useful to have rate components that change for each customer. The canonical example is of a fixed service charge that depends on the size of the water meter used by each account (often referred to as a "meter charge").

Example 3 is almost the same as Example 2, but the fixed service charge now changes depending on the size of the meter. It is important to ensure that when the OWRS file is used to calculate water bills (e.g. with the [RateParser](https://github.com/California-Data-Collaborative/RateParser) package), that the values specified in `values` ('3/4"', '1"', etc) are exactly the same as those that appear in the billing data set under the `meter_size` column.

#### <a name="example4"></a>Example 4 - Tiered Rates
```yaml
---
metadata:
  ...
rate_structure:
  RESIDENTIAL_SINGLE:
    service_charge:
      depends_on: meter_size
      values:
         3/4": 14.65
         1"  : 16.77
         2"  : 25.83
    tier_starts:
      - 0
      - 15
      - 41
      - 149
    tier_prices:
      - 2.87
      - 4.29
      - 6.44
      - 10.07
    commodity_charge: Tiered
    bill: commodity_charge+service_charge
```

Example 4 replaces the flat rate structure of earlier examples with an [Increasing Block Rate](http://www.allianceforwaterefficiency.org/1Column.aspx?id=712), or "Tiered" rate structure. This pricing scheme is encoded using two new fields.
* `tier_starts` represents the lower end of each block. It is the first unit of water billed at a specified price.
* `tier_prices` specifies the price of water within each tier, in dollars per billing unit.

In Exampe 4, we can see that the first through the 14th units of water are billed at $2.87 per unit. The 15th through the 40th unit are billed at $4.29. The 41st through the 148th at $6.44. Finally all water use from the 149th unit and up is billed at $10.07 per unit.

In this case, the `commodity_charge` field **must** be set as "Tiered" in order to specify how the tier starts are interpreted.

#### <a name="example5"></a>Example 5 - Budget Based Rates

OWRS also accomodates budget/allocation based rates, although the complexity of the file must rise to match the complexity of the rate structure.

```yaml
---
metadata:
  ...
rate_structure:
  RESIDENTIAL_SINGLE:
    service_charge:
      depends_on: meter_size
      values:
         3/4": 14.65
         1"  : 16.77
         2"  : 25.83
    gpcd: 60
    landscape_factor: 0.7
    days_in_period: 30.4
    indoor: "gpcd*hhsize*days_in_period*(1/748)"
    outdoor: "landscape_factor*et_amount*irr_area*0.62*(1/748)"
    budget: "indoor+outdoor"
    tier_starts:
      - 0
      - indoor
      - 100%
      - 133%
    tier_prices:
      - 2.87
      - 4.29
      - 6.44
      - 10.07
    commodity_charge: Budget
    bill: commodity_charge+service_charge
```

Example 5 shows how to specify single-family rates under a budget based rate structure. There a several new fields and formulas in this example:
* `gpcd`, `landscape_factor`, `days_in_period` are all simple fields that are the same across all SFR customers.
      - Note that if `days_in_period` were not defined here as 30.4 (average number of days in a month) then it would be expected that the user defines this as a data column and this value could change to reflect the actual number of days in each customer's billing period.
* `hhsize`, `irr_area`, and `et_amount` are expected to be provided as data columns.
* `indoor`, `outdoor`, and `budget` represent the calculated indoor allocation, outdoor allocation, and total water budget, respectively.

When the `commodity_charge` is specified as "Budget", OWRS parsers interpret `tier_starts` differently. As is visible in this example, tier starts may be specified either as flat values in terms of billing units (as in Example 4) or they can be specified as a percentage of the `budget` field. This allows OWRS to accomodate different tier widths for each account based on data specific to that account.  

#### <a name="example6"></a>Example 6 - Other Cases

The examples above cover the vast majority of use cases, but there are still a few that have not yet been discussed. Fortunately the OWRS framework is extremely flexible. For example:

##### Tiers that depend on other values
```yaml
---
tier_starts:
   depends_on: meter_size
   values:
     5/8":
       - 0
       - 211
     1_1/2":
       - 0
       - 466
     2":
       - 0
       - 871
```

##### Tiers that depend on multiple other values

For example, [LADWP's tiers](https://www.ladwp.com/cs/idcplg?IdcService=GET_FILE&dDocName=AD17DWPWEB9173008669&RevisionSelectionMethod=LatestReleased) depend on season, lot size, and temperature zone

```yaml
---
tier_starts:
   depends_on:
     - season
     - lot_size_group
     - temperature_zone
   values:
     Winter|1|Low:
       - 0
       - 17
       - 23
       - 35
     Summer|1|Low:
       - 0
       - 17
       - 29
       - 53
     Summer|1|Medium:
       - 0
       - 17
       - 31
       - 59
     Summer|1|High:
     ...
```
In this case each combination is enumerated as "value1|value2|...|valueN", where the values appear in the order they are given in the `depends_on` field. This format is not quite as visually appealing as a table, but it provides easy processing for OWRS parsers.

##### Rolling averages

One somewhat common use case is for utilities with budget based rates to set their CII allocations as a rolling average of historical water use for a given month. Unfortunatley this functionality is not supported by current OWRS parsers, but there is a work-around. Instead of being computed in the parser, allocations for CII customers can be precomputed and saved as a data column (e.g. `cii_allocation)`. This column could then be referenced in the OWRS file as

```yaml
---
budget: cii_allocation
tier_starts:
  ...
tier_prices:
  ...
commmodity_charge: Budget
...
```

## <a name="utility-examples"></a>Full Utility Rate Files

See the [examples from California](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/tree/master/full_utility_rates/California) in this repository for examples of full utility rate structures in OWRS format.
