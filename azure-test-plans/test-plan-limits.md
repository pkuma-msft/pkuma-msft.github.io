#### Is there a limit to the number of test plans, test suites, test cases and finally the number of test case steps one can create?  

There are no limits as such to the number of test plans in a project or the number of test suites or test cases in a test plan. There are however other forms of limits outlined below. There are also few best practices we recommend to ensure the Test Plans experience is performant and reliable.

- Ensure that the test suites you have in a test plan are regularly/actively used. If a test suite is not really required in a plan, it’s best to remove it. On the same lines, to some extent, ensure that all test cases that show up within a test plan are also needed there for the test pass. Many times users keep cloning the test plan sprint over sprint with the test cases that are no longer required/valid and this ends up impacting performance later.
- Test suites can be nested up to 14 levels deep. But try to keep the nesting to as low as practically possible. More nesting tends to add complexity to various computations and adversely affects performance.
- Since test plans and test cases are just work items underneath, they need to honor the 1000 links limit. A workitem may contain only up to 1000 links to other workitems. This can sometimes be hit in a scenario where you have a shared step which is being used in many other test cases.
- If you use a query based suite, ensure that each QBS shows a reasonable number of test cases. The upper limit on this is 20,000; but again if you have a QBS that returns 20,000 test cases you will have a hard time using the test plans UX 😊
- Test runner has a limitation of being able to run 100 test cases in a single test session.
