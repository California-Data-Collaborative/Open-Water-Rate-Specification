
# Contributing to OWRS

This repository documents how to contribute data to the **Open Water Rate Specification** (OWRS), a machine-readable format for specifying and sharing water rate information.

OWRS is designed so that anyone can contribute standardized water rate data.

### Getting Started

Follow these simple steps
(The example we use here is Moulton Niguel Water District)

1. Refer the [OWRS README](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/blob/master/README.md)
2. Fork the OWRS repo to your own Github.
3. Clone the forked OWRS repo locally to your machine. `git clone <LINK to the FORKED REPO>`
4. Find an unclaimed utility on the [spreadsheet tracking OWRS status for California water utilities](https://docs.google.com/spreadsheets/d/1THqfs-tCxQiov65hHD3pG-9wRULHMo1aANy3EOiEAQ4/edit#gid=1887640542) and put your name in the "Assignment" column to claim it as yours.
5. Google search the Utility name (Moulton Niguel Water District) and "water rate" to find the page containing water rate information.
  * For Moulton Niguel Water District - this would be https://www.mnwd.com/waterandwastewaterrates/
6. On your computer
   * `cd Open-Water-Rate-Specification/full_utility_rates/California/`
   * `git checkout -b  "Moulton Niguel Water District - 147"`
7. Create a new folder for that utility `mkdir "Moulton Niguel Water District - 147"` 
  * Use the naming convention "[Agency Name] - [utility_id]" e.g. `mkdir "Moulton Niguel Water District - 147"`
  * [Agency Name] and [utility_id] can be found in the tracking spreadsheet.
8. Write an OWRS file for that utility in that folder
  * You will first use the [generic OWRS template aka `template.owrs` in the templates folder](https://github.com/California-Data-Collaborative/Open-Water-Rate-Specification/tree/master/templates)
  * Based on the type of rate however - you can use the other templates (depends_on_template.owrs OR tiered_template.owrs)
  * Use naming convention "[acronym]-YYYY-MM-DD.owrs"
  * where YYY-MM-DD is the date when that utility's water rate is effective. (found on the Utility website)
9. Ask questions on the OWRS slack as need be
10. When your OWRS file is good to go, push your changes to the remote GitHub repo and submit a pull request!  
11. A member of the CaDC data team will review the OWRS file and let you know if there are any questions
12. Pick another utility!
13. GOTO #4
14. Pioneer a brighter future for CA!


### Open water rate spectacular

If you're attending this event in person, please feel free to ask [Chris](https://github.com/christophertull), [Vyki](https://github.com/vykster), [Patrick](https://github.com/patwater), [Vincent](https://github.com/vincent-hebble) or [David](https://github.com/dmarulli) (in CA) or [Varun](https://github.com/vr00n) / [Vipassana](https://github.com/vipassana) (in NYC) if you have any questions.  Regardless you can always ask questions in the open_water_rates ARGO slack channel.  
