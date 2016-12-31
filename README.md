
# Open Water Rate Specification

This repository documents the **Open Water Rate Specification** (OWRS), a machine-readable format for specifying and sharing water rate information. 

OWRS is designed for analysts, economists, and software developers interested in analyzing water rates. OWRS attempts to fully encode a water utility's rate structure and pricing schedules in a form that is easy to store, share, modify and apply programmatically. 

### Table of Contents

* [Why a Standard for Sharing Water Rates?](#why)
   - [Benefits of OWRS](#benefits)
* [Getting Started](#getting-started)
   - [Example 1 - Simple Flat Rate](#example1)
   - [Example 2 - Flat Rate with Fixed Service Charge](#example2)
   - [Example 3 - Flat Rate with Fixed Service Charge that Depends on Meter Size ](#example3)
* [Full Utility Examples](#utility-examples)

### Contact

Please reach out with comments, ideas or suggestions using the [issues page](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/issues), or [contact the CaDC](http://californiadatacollaborative.com/join-us/) directly.

## <a name="why"></a>Why a Standard for Sharing Water Rates?

Anyone familiar with water rates is likely familiar with the PDF documents, [HTML tables](https://www.smgov.net/Departments/PublicWorks/ContentWater.aspx?id=7743) and [images](https://www.mnwd.com/waterandwastewaterrates/) generally used to present water pricing information to the public. These formats are useful because they provide a consise representation that is easy for people to understand. However, these formats are often not ideal for analysts, consultants, economists or others interested in analyzing water prices for several reasons:

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
    commodity_charge: 2.1*usage_ccf
    bill: commodity_charge
```

The `yaml` file format works by specifying a series of keys and values. In the example above, `bill_frequency` is a key and `monthly` is the value corresponding to that key. This key-value pair is itself a value of the `metadata` key.

`metadata` is used to specify information about the rate structure that is not actualy used to calculate bills, such as the name of the utility and the date the rate structure went into effect.

`rate_structure` specifies information that is actually used for calculating water bills. The values of the `rate_structure` key are the customer classes used to define rates. In our examples we use the standard classes defined by the CaDC: `RESIDENTIAL_SINGLE`, `RESIDENTIAL_MULTI`, `IRRIGATION`, `COMMERCIAL`, `INDUSTRIAL`, and `INSTITUTIONAL`.

In Example 1 above, we see a rate structure defined only for single-family residential customers, where the commodity charge is calculated as `2.1*usage_ccf`, or $2.1 per CCF of water used. In this case `usage_ccf` is the data column name used to represent water usage. The total bill (`bill`) for each customer is then equal to just the commodity charge.

#### <a name="example2"></a>Example 2 - Flat Rate with Fixed Service Charge
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

The flat rate or $2.1 per CCF has also been pulled into it's own field and this field is multiplied by `usage_ccf`. This formulation is just as valid as the formulation in Example 1.

### Fields, Formulas, Maps and Tiers

The two examples above are composed entirely of simple parts, referred to in this context as **Fields** (e.g. `flat_rate: 2.1`) or **Formulas** (e.g. `bill: commodity_charge+service_charge`). However, often in real settings is it useful to have rate components that change for each customer. The canonical example is of a fixed service charge that depends on the size of the water meter used by each account.

#### <a name="example3"></a>Example 3 - Flat Rate with Fixed Service Charge that Depends on Meter Size 
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

Example 3 is almost the same as Example 2, but the fixed service charge now changes depending on the size of the meter. It is important to ensure that when the OWRS file is used to calculate water bills (e.g. with the [RateParser](https://github.com/California-Data-Collaborative/RateParser) package), that the values specified in `values` ('3/4"', '1"', etc) are exactly the same as those that appear in the billing data set under the `meter_size` column.

## <a name="utility-examples"></a>Full Utility Rate Files

See the [examples folder](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/tree/master/examples) in this repository for examples of full utility rate structures in OWRS format.
