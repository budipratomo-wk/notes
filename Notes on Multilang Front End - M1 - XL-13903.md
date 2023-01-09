
## TASKS
- [x] Default language should be retrieved from the Locale if it's null. Otherwise use what's already written in the db. 
- [x] Point to the report id prop available from the Profile Management Tab component to manipulate the DTO
- [x] The available language source still needs to be discovered.
- [x] The Enabled Languages will be stored in the report store. 
- [x] Add supports multilang check in the taxonomy as criteria to show languages tab
- [x] Moved the executeChange to way later when the user click apply 
- [ ] Investigate how the front end testing can be done independently on the backend (mock framework)
- [ ] Keep the width the same of the dropdown
- [ ] Have a property "has unsaved changes" in the store, and update this whenever a dropdown option is selected.  
- [ ] Include this in the onHide modal property callback
- [ ] Include this in the onHide modal property callback


## INFO
LD flag: rrpl-xbrl-enable-multi-language-labels
Jira Link for the Front End: https://jira.atl.workiva.net/browse/XL-13903
Pending bug https://jira.atl.workiva.net/browse/XMAS-13623
testing URL: http://localhost:8080/a/QWNjb3VudB81OTE0Nzc1MjQ3NjUwODE2/doc/ab233a88d8a348bebe529b094853d7e9/r/-1/v/1/sec/ab233a88d8a348bebe529b094853d7e9_32?wkdev=true&rrpl-xbrl-enable-multi-language-labels=true
testing URL2: http://localhost:8080/a/QWNjb3VudB81OTE0Nzc1MjQ3NjUwODE2/doc/73d4ad4db0734426a3a76b661c20d5b6/r/-1/v/1/sec/73d4ad4db0734426a3a76b661c20d5b6_33?wkdev=true&rrpl-xbrl-enable-multi-language-labels=true

## QUESTIONS
 - In language translator client, there are a few languages that couldn't be retrieved from H 
 - So who decides if the default language should not be in the enabled languages, front end or back end?
 - Should the language name displayed in english or their respective languages? (e.g. Russian in Cyrillic?) Should we use the languageCodeToName in the constants.dart?
 - What happens when someone opts-in to the multi language labels. 


## LINKS
[[How actions work in the Report Store]] 
[[When to execute change to the XBRL ReportDTO]]
 [[How to test]]
[[How to have a form that doesn't commit to API unless saved]]
[[How languages play a role in the XBRL generation]]
[[Questions for Language Selection - UX Candace]]
