### Test case import and export using CSV

[**Update: 6th July 2020**]
This feature is now enabled on all Organizations!

[**Update: 8th June 2020**]
Shared steps, local parameters and shared parameters are now supported in the import/export feature! Do give this a try and let us know your feedback.  

We are very excited to release the import/export feature for test cases in early preview! This preview will also enable us to get early feedback and address any major issues or bugs. Please read on to find details about how you can opt-in to this feature, where can you report bugs etc.  

#### How do I opt-in to this feature?  
**This is no more required since the feature is now enabled on all Organizations!**  

#### Feature description
Make sure you have [Basic + Test Plans](https://docs.microsoft.com/en-us/azure/devops/organizations/billing/buy-basic-access-add-users?view=azure-devops#assign-users-basic-or-basic--test-plans) license. Select any static suite in the new Test Plans experience. You will find the import/export option in the ```Define``` tab, as shown below. 
![Import/export option in Define tab](../images/import-export-option-ui.png)  
  
The ```Down``` arrow lets you export the test cases in the current suite into a CSV file and save it on disk. The ```Up``` arrow lets you upload a CSV file and import the test cases in to the currently selected suite. Currently, all the test cases within a Suite get exported and selecting only a subset of the test cases has no effect. We are looking to add support to export only selected test cases in a future update.  

Here is a GIF showing how you can update an existing test case using export/import option. (*open in new tab for better viewing*)  
![GIF showing updation of existing test case](../images/import-export-demo-update-existing.gif)

The GIF below shows how you can use the import option to add new test cases to a suite. (*open in new tab for better viewing*)
![GIF showing adding new test cases via import](../images/import-export-demo-add-new.gif)

#### How do I add or import new test cases to a suite?
By importing a CSV file where the first column ```ID``` field is blank, you can create new test cases. The easiest way to do this is to export test cases, edit them in excel, make sure you delete the ID column value and leave it blank and import this CSV file into a suite. The ```ID``` column denotes the test case id. If the ID column contains a valid test case ID, then the test case will get updated.

#### How do I see other fields or columns when I export?
By default we export all the columns that are visible currently in the suite. If you want to see the new field in CSV, you can choose that ```Column option``` in the suite and then perform the export.

#### How do I export all my test cases from a project?
Since we export all test cases from the current suite this is currently not possible. But there is a work around!  

You can create a new Query based suite in a test plan, modify the query as per your requirement; wait for the test cases to load in the new QBS and then export them all. Although you can use this work around to export all the test cases in a team project, we would highly recommend for you to use as specific a query as possible. If your query returns a large number of test cases (in the range of couple of thousands) then the performance of the page will get degraded. **If you created the QBS temporarily to export the test cases, make sure you delete the Query based suite!**  

## What is not supported?  
- [Update 8th Jun 2020] Shared steps are now supported!  
~~Shared steps in test cases is not supported. **WARNING: If your test cases contain shared steps, then exporting and importing the test case may cause you to lose test case steps.**~~
- Retaining formatting of the test steps is not supported. **WARNING: If you rely on the test step formatting, then exporting-importing will make you lose the formatting!! Please do not use the import/export feature in this case.**
- [Update 8th Jun 2020] Local parameters and Shared parameters are now supported!  
~~Local parameters and Shared Parameters in a test case are not supported while exporting or importing.~~
- [Update 6th July 2020] Most common error scenarios are now handled properly!  
~~Graceful error handling and data validation is not supported. This is work in progress and will get better with future updates.~~
- Delete operation is not supported. If you delete or remove a test case from CSV and import it; the test case will not get deleted from the suite or team project.
- When creating new test cases via importing, you must set the state of the test case to ```Design```. Other state values during test case creation are not supported.  
- Test results are not exported or imported.
