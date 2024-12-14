# The 1924 Project
## A Python Workflow for Immigration Restriction History
---
### Description & Rationale 
The main goal of this project is to create a workflow that can serve as a resource for obtaining historical data relating to immigration restrictions in the US. 

2024 marks the 100th anniversary of the creation of the discriminatory national origins quota system. Although the system was replaced after 1965, its legacy and those of earlier exclusionary policies echoes throughout current public discussions of immigration and racialized rhetoric towards migrants. 

To facilitate comparisons between past and present, workflows for extracting datasets of historical documents using Python are described in the project files. Ideally, they provide an additional tool beyond catalog searches to help researchers find resources documenting immigration restrictions.     

### Workflow
The general methodology is using the Python requests library to make API calls from archives or libraries that have historical collections relating to immigration restrictions. Then, using the Pandas library to create a dataset, clean the data and analyze it to provide examples of the kinds of insights it can provide.

**Sources**

I identified several archives with large collections of digitized historical documents, including the Digital Public Library of America (DPLA) and the National Archives. Retrieving digitized documents was the focus in the belief that they’d offer richer information for users, such as images, URLs, subject headings and descriptions. 

Given time restrictions and the challenges of learning APIs from different institutions, I chose to work only with the DPLA, which has extensive and detailed [documentation for its API](https://pro.dp.la/developers/api-codex). 

**Querying** 

