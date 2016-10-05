
# Open Water Rate Specification

This repository documents the Open Water Rate Specification (OWRS), a machine-readable format 
for specifying and sharing water rate information. 

OWRS is designed to fully encode a water utility's rate structure and pricing schedules in a form
that is easy to store, share, modify and apply programmatically.

Ultimately this standard will form the foundation for a comprehensive database of water price information. When combined with customer billing data (like that gathered by the [CaDC](http://californiadatacollaborative.com/)), this enables the analysis of water bill equity and utility revenue stability. 

## Example: Moulton Niguel Water District

```yaml
--- 
metadata: 
  effective_date: 2016-01-01
  utility_name: "Moulton Niguel Water District"
  bill_frequency: 1
rate_structure: 
  - Residential:
      gpcd: 60
      landscape_factor: 0.7
      indoor: "gpcd*hhsize*days_in_period*(1/748)"
      outdoor: "landscape_factor*ET*irrigable_area*0.62*(1/748)"
      budget: "indoor+outdoor"
      service_charge: 
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
      tier_starts:
        - 0
        - indoor
        - 100%
        - 125%
        - 150%
      tier_prices: 
        - 1.49
        - 1.70
        - 2.62
        - 4.38
        - 9.17
      bill: "variable+service_charge"

```
