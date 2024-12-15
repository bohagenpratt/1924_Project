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

Given time restrictions and the challenges of learning APIs from different institutions, for now I chose to work only with the DPLA, which has extensive and detailed [documentation for its API](https://pro.dp.la/developers/api-codex). 

**Querying** 

First query used wildcard operators to search for documents about immigration (*or immigrants*) and law. Using the API’s temporal searching and pagination functionalities, the initial query was limited to 100 docs between the years 1880 and 1945, covering the periods when most restrictions were created. 

A later version of this query expanded the document limit to 500, which became the dataset used for the analysis described below. 

```
import requests
url = 'https://api.dp.la/v2/items?q=immig*+AND+law*&sourceResource.date.after=1880&sourceResource.date.before=1945&page_size=100&api_key=7e794707ae677b5eb408334e9e6cc16e'
```
**Building the dataframe**

After parsing the json from the request object, the next step was deciding what fields to include in the dataframe. The sourceResource key within json contains most of the information I wanted and became the basis of the fields in the df.

![The sourceResource key from DPLA's API](Source_resource.png)

```
df = pd.DataFrame({
   'ID': ids,
   'Title': title,
   'URL': link,
   'Dates': date,
   'Locations': location,
   'Description': description,
   'Subjects': subjects,
   'Creator': creator,
   'Contributor': contributor
})
```
**Data Cleaning**

This was *easily* the biggest challenge and most time-consuming aspect of the project. Since the data was in dictionaries, I struggled to access the key:value pairs for some of the values that would be most helpful for analysis, particularly subject headings and geographic locations. 

Subjects proved uniquely trying to work with. Most documents in the df had subjects from their DPLA record, from as few as one to as many as 12 for a single record.
```
immig_docs[2]['sourceResource']['subject']
[{'name': 'Working class--Dwellings'}, {'name': 'Tenement houses--California'}]
```
Despite much toil, efforts to create separate columns for each individual subject or location using Python were unsuccessful, and I had to settle for all the subjects and locations for a given document being lumped into one Subjects or Locations field.

![Error received while appending subjects](Subject_troubles.png)

For cleaning data, I used the Pandas library in Python, and was able to break the locations out into individual columns with the string split() method: 

```
df[['Location_one', 'Location_two', 'Location_three', 'Location_four', 'Location_five']] = df['Locations'].str.split(',', expand=True)
```

Converting each column in the df to a string and then using the string replace() method was a helpful workaround for cleaning unwanted punctuation like dict brackets and quotation marks: 

```
df['Location_one'] = df['Location_one'].astype(str)
df['Location_one'] = df['Location_one'].str.replace("'", ' ')
```

Unfortunately the string split() method did not work for the subjects due to a repeated `ValueError: Columns must be same length as key`, forcing me to do the work in my CSV file instead. 
