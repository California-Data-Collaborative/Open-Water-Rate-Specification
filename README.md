
# Open Water Rate Specification

This repository documents the **Open Water Rate Specification** (OWRS), a machine-readable format for specifying and sharing water rate information. 

OWRS is designed for analysts, economists, and software developers interested in analyzing water rates. OWRS attempts to fully encode a water utility's rate structure and pricing schedules in a form that is easy to store, share, modify and apply programmatically. 

## Why a Standard for Sharing Water Rates?

Anyone familiar with water rates is likely familiar with the PDF documents, HTML tables and images generally used to present water pricing information to the public. These formats are useful because they provide a consise representation that is easy for people to understand. However, these formats are often not ideal for analysts, consultants, economists or others interested in analyzing water prices for several reasons:

1. HTML tables may be easy for customers to understand but they are difficult for computer code to understand and usually must be manually translated into code before a rate analysis can be performed.

2. Sometimes the exact formulas used to calculate a water bill are not explicitly defined.

3. PDF and HTML formats are more difficult to store, transmit and interpret than plain text.

## Benefits of OWRS

OWRS attempts to overcome these downsides by specifying a plain text format to fully specify a water rate structure. Specifically: 

1. OWRS is based on [YAML](http://yaml.org/), and as such it is designed to be easy to store, transmit, and parse in any programming language.

2. a

3. a

## Vision for the Future

Ultimately this standard will form the foundation for a comprehensive database of water price information. When combined with customer billing data (like that gathered by the [CaDC](http://californiadatacollaborative.com/)), this enables the analysis of utility revenue stability and water bill equity. 

### How to Contribute

Please reach out with ideas or suggestions using the [issues page](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/issues), or contact the CaDC directly.

## Example: Moulton Niguel Water District

```yaml
---
metadata:
  effective_date: 2016-01-01
  utility_name: "Moulton Niguel Water District"
  bill_frequency: monthly
rate_structure:
  RESIDENTIAL_SINGLE:
    - gpcd: 60
    - landscape_factor: 0.7
    - indoor: "gpcd*hhsize*days_in_period*(1/748)"
    - outdoor: "landscape_factor*ET*irr_area*0.62*(1/748)"
    - budget: "indoor+outdoor"
    - service_charge:
        depends_on: meter_size
        values:
          5/8"   : 11.39
          3/4"   : 11.39
          1"     : 11.39
          1_1/2" : 37.98
          2"     : 60.77
          3"     : 132.94
          4"     : 227.88
          6"     : 475.14
          8"     : 683.65
          10"    : 1101.82
    - tier_starts:
      - 0
      - indoor
      - 100%
      - 125%
      - 150%
    - tier_prices:
      - 1.49
      - 1.70
      - 2.62
      - 4.38
      - 9.17
    - commodity_charge: Budget
    - bill: "commodity_charge+service_charge"


```
