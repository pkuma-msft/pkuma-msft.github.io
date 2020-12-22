Since Feb-Mar 2020, we started getting a number of tickets where there was some inconsistency between: 
   1. Charts and Execute tab/Progress Report 

   2. Progress Report and Charts/Execute tab 
   3. Execute tab and Progress Report/Charts 

 

## Possible reason for the sudden increase in the number of data discrepancy issues: 
 

Most of the cases that we observed were reported only by a few customers. However, the first type of discrepancy, I.e., **between Charts and Execute tab/Progress report**, was reported by many users. So, this is something we needed to figure out why started happening suddenly. 

- One thing to note is that the dual-write feature for writing results both in TCM and TFS was implemented sometime during April 2019. 
 
- Since the default retention period for keeping test runs and results is 1 year, exactly after 1 year, retention started kicking in for all the users, who were using the default retention settings. 
 
- This created inconsistencies in point results data between TFS and TCM, which is discussed in details later. 

- This is the only possible reason we can think of for the sudden increase in the number of discrepancy issues. 

 
 
 
## Opening and closing of the web-runner: 
Before Discussing all the scenarios and their fixes in details, let us first understand the major functions carried out from the opening of the web-runner to its closing. 
 

- The user action to launch the web-runner triggers the creation of 'in progress' test runs, both in TFS and TCM. 
 

- Whenever the user marks an outcome, the results corresponding to these test runs are updated to show the latest outcome. 
 

- However, due to some reasons, if the outcome is not marked and the web-runner is closed, these runs and results need to be deleted or overwritten by the last test run, in order to show the last valid outcome. 
 

- Upon closing of the web-runner without marking the outcome, **the deletion (or overwriting) takes place in 2 steps**: 

  - The 'End' API is called, which first fetches the latest result associated with the test run from TCM and checks whether the result was completed or not. This is done to make sure whether deletion is required or not. 

  - If deletion is required, an abort function is called with the run id and the fetched results to perform the required operations. In TCM, the 'in progress' runs and results are deleted and the previous outcome is copied again in pointResultDetails. This outcome is then used to overwrite the result in TFS point table.


![End API flow](../images/azure-test-plans-scenarios/web-runner.png)

 
## Now, let us discuss every scenario in detail and understand how they were fixed: 
 

### Scenario 1: Discrepancy between Charts and Execute tab/Progress Report 

#### Reason:

This issue came up for users when the **retention** period for keeping test results (default 365 days) completed. The problem was due to the results getting cleaned from TCM due to retention kicking in but staying as it is in TFS. This causes discrepancy between progress report(gets data from TFS) and charts(gets data from TCM). In addition to that, though execute tab users **'getTestPointsAPI'** call to fetch outcome data from TCM, when it  receives NULL results from TCM, it used to send outcomes returned from TFS in API responses, causing execute tab to also show TFS data. 

![Discrepancy scenario 1](../images/azure-test-plans-scenarios/discrepancy-scenario1.png)  
 
#### Fix:
This was fixed in two steps. 

- [First](https://dev.azure.com/mseng/AzureDevOps/_git/AzureDevOps/pullrequest/564886?_a=files), to fix data shown on execute tab, for the **getTestPointsAPI**, we stopped using outcomes from TFS, **when we received NULL values from TCM and returned NULL itself instead**. 
 

- In the [second step](https://dev.azure.com/mseng/AzureDevOps/_git/AzureDevOps/pullrequest/568737?_a=files), we made a change to **start writing NULL values in TFS DB when we received NULL from TCM**. This results in an on-demand clean-up of the results from TFS that were already cleaned up from TCM, due to retention. This fixed the data shown on progress reports. 

 

Details of discussion [here](https://microsoft.sharepoint.com/teams/DD_TestingTools/_layouts/15/Doc.aspx?sourcedoc={0debd44e-c026-482c-86d5-cc5eec7e133c}&action=edit&wd=target%28TestHubRefresh-New.one%7C3aef3f86-5716-4638-bd49-eae4ed695ff9%2FDesign%20TCM%20TFS%20Point%20Outcome%20Mismatch%20-%20%20retention%20setting%7Cfe91920e-37ce-47c7-bc5c-cc203fec2b69%2F%29&wdorigin=703)
 
 
### Scenario 2: Discrepancy between Progress Report and Charts/Execute tab 

 
#### Case 1
#### Reason: 
##### When the web-runner fails to launch due to some reasons like blocked popups, etc. or was closed before it was completely loaded -

- As explained above, even in this case, due to the user action to launch the web-runner, a new 'in progress' test run is created for the test point, which needs to be cleared in order to show the last valid outcome. 
 
- However, in this case, instead of calling the 'End' API, **a direct call is made to the 'Abort' API**, with just the run id as payload. 
 
- Due to the missing result parameter to the API, which is usually sent through the 'End' API flow, the deletion takes place only in TCM but not in TFS, as in TFS we send the old valid result from TCM to overwrite the 'in progress' one. 

#### Fix:
In the abort function, a [change](https://dev.azure.com/mseng/AzureDevOps/_git/AzureDevOps/pullrequest/566399?_a=files) was made to fetch the latest results from TCM, if they were not provided.  
 
This ensures that we get the valid outcome copied to TFS point table too. 


#### Case 2
#### Reason:
##### Update result analysis flow (result summary page)- 
 
- This flow is used by a user to add comments to a result to be able to review it later. 
 
- In case, the user tries to add comments to some older result, there can be discrepancies in 2 ways: 

  - **When the comment for a single result is updated**: The newer result is overwritten in the TFS table and not in the TCM table. 

  - **When the comment for multiple results are updated in the same call**:  This overwrites the newer results in TCM tables but not in the TFS tables. 

 

- The difference in the two flows is based on the payload we send in the API call and also the sproc being used to make updates in TCM tables: 

  - In the first case, **the payload has the plan id**, which causes the point to be updated in TFS tables but some checks in the TCM sproc prevent changes in the TCM tables. 

  - In the second case, **the plan id is set to 0 in the payload**, which prevents changes to TFS tables but there are no checks in the TCM sproc being used and hence, results are overwritten in TCM tables.
    See [here](https://dev.azure.com/mseng/_git/AzureDevOps?path=%2FTfs%2FService%2FTestManagement%2FServer%2FService%2FPoints%2FModel%2FTestPointOutcomeHelper.cs&version=GBmaster&line=52&lineEnd=52&lineStartColumn=1&lineEndColumn=44&lineStyle=plain&_a=contents).
 
#### Fix:
- The first scenario of single test result analysis can be taken care of, **by removing the plan id from the request payload**, since it is not required to update the comment. The second scenario would require some other changes, which haven't been thought of yet. 
 

- Since this was reported only by one customer and updating the comment of an older test result was a highly unlikely case, **we have added it to our knowledge base to be addressed later, if we get more similar queries**. 
 

#### Case 3
#### Reason:
##### When a manual outcome is marked for a test point whose automated run is in progress - 

- The most common scenario where this can happen is when there are more than 1 points being executed by a single automated run. 
 
- Consider  a case where 2 different test points are executed by a single automated test run. 

  - Suppose, **the execution for the first point, say P1, completes and the execution for the second point, say P2, is in progress**. 

  - Now, **if the point P1 is marked manually**, its latest outcome should be the manual one. 

  - However, when the execution for P2 completes, **both the automated results are copied into the TCM pointResultDetails table**, not taking into account the completed timestamp for the automated result of P1, although it was less than that of the manual run. 

  - But the sync job, which is responsible for fetching automated results from TCM to TFS, considers the completed timestamp for the automated run and shows the correct outcome. 

 

- Thus, in this scenario, TFS point table has the correct outcome for the test points, whereas, TCM pointResultDetails table has the incorrect outcome for one of the test points. 

#### Fix:
- This was fixed by this [change](https://dev.azure.com/mseng/AzureDevOps/_git/AzureDevOps/pullrequest/568942?_a=files) made to the sprocs, responsible for copying the outcomes to pointResultDetails, upon run completion. 
 
- After this fix, outcomes were copied to pointResultDetails table only after comparing the completed timestamp in TestResults table with the last updated timestamp in pointResultDetails table. 
 

- **Regression**: The above fix resolved the issue but caused another regression, which caused Charts and Execute tab to go out of sync. 
 
  This regression was observed when this change was deployed on TCM ring 0 and a customer tried to mark the outcomes of test points manually, without launching the web-runner. 
 
  However, in the end, in order to address the new issue, we had to revert the original fix and think of a new fix for the same. 

  After a few rounds of discussion, we reached with a solution to the problem but that required more sproc changes. So, we came to a decision to not go ahead with the changes until we received more feedbacks for similar issues, as this was also reported by only one customer and is sort of a one-off scenario. 

 
  

### Scenario 3: Discrepancy between Execute tab and Progress Report/Charts 

#### Reason:


##### When the web-runner is opened and closed for a test point previously reset to active state - 
 

- Whenever a test point is reset to active, the active state of the test points is saved only in TFS point table and not in the TCM tables. This was the major cause of this discrepancy. 
 

- Suppose, **at time T1, a point P1 was provided with an outcome, say passed**. This outcome gets saved both in TFS and TCM. Now, **at time T2, the point was reset to active**. 
 

- Due to this, the last valid outcome in the TCM tables show the point as passed. 
 

- Now, **at time T3, the point is opened in web-runner and closed without marking any outcome**. 
 

- If we recall the cleanup of the 'in progress' results, **we fetch the last valid outcome from TCM and copy that into TFS**, which makes the points to get marked as passed as at T1, instead of marking them as active, as at T2. 
 

- **While fetching the data for execute tab, we used to take into account the last reset to active timestamp, which helped us show the correct active state there**. However, this logic was not included in Charts and Progress Report, due to which they kept showing the points as passed. 

 
#### Fix:
- The first [change](https://dev.azure.com/mseng/AzureDevOps/_git/AzureDevOps/pullrequest/566883?path=%2FTcm%2FShared%2FSql%2FPlans%2FSProcs%2Fprc_UpdateTestPointOutcome.sql&_a=files) was to add a check while copying the previous outcome from TCM to ensure that the point was not reset to active after the completion of the previous result. This fixed the issue with Progress Reports. 
 

- The second [change](https://dev.azure.com/mseng/AzureDevOps/_git/AzureDevOps/pullrequest/567469?path=%2FTcm%2FShared%2FSql%2FResults%2FSprocs%2Fprc_GetTestExecutionReport2.sql&_a=files) was to take into account the last reset to active timestamp while fetching data for Charts.

![Discrepancy Scenario 3](../images/azure-test-plans-scenarios/discrepancy-scenario3.png)
 

 
