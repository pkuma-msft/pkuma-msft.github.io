### Fix a Query Based Suite's query using REST API

This article shows how to update the query of a query-based test suite using REST API. This is particularly useful in cases where the query is now malformed (the area path referred in query is deleted etc.).  

##### Get the ID of that Query Based Test Suite
 You should be able to see the suite Id in the URL of your browser after you select the query-based test suite in the UI - ```https://dev.azure.com/<organizationName>/<projectName>/_testPlans/define?planId=3&suiteId=<suiteIdHere>```

Then get a personal access token so that you can make the API calls below. You can give the token Test management write scope and it should work.

- Get the existing query of that suite:  
Make a GET call to below URL replacing the parts in <> and using your personal access token.
```https://dev.azure.com/<organizationName>/<projectName>/_apis/testplan/Plans/<TestPlanId>/Suites/<TestSuiteId>?api-version=6.0-preview.1```  
It should return you the existing query in queryString part of the response.

- Update the query of that suite:  
Make a PATCH call to below URL replacing the parts in <> and using your personal access token.
```https://dev.azure.com/<organizationName>/<projectName>/_apis/testplan/Plans/<TestPlanId>/Suites/<TestSuiteId>?api-version=6.0-preview.1```  
Use below HTTP headers:  
Content-Type:application/json  
Authorization header for your PAT as described here - https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate

Use body as:  
```
{
"queryString":"<your updated query>"
}
```

Make a HTTP PATCH call, it will update the query that was used for your Query Based Suite.