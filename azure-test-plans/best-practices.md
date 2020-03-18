#### Azure Test Plans - Best Practices
Hi there, to ensure that the Azure Test Plans experience is performant and reliable, we recommend the below best practices:

- Ensure that the test suites you have in a test plan are regularly/actively used. If a test suite is not really required in a plan, it’s best to remove it. When ever you create a new Test Plan it's best to review all the test suites within and ensure that all test suites are really required for the current test plan.
- On the same lines, to some extent, ensure that all test cases that show up within a test plan are also needed there for executing the test pass. Many times we keep cloning the test plan sprint over sprint with the test cases that are no longer required/valid and this makes the test plan bloat up with unnecessary suites and test cases impacting performance and reliability.
- Try to keep the nesting of test suites to as less as practically possible. Test suites can be nested upto 14 levels deep. Extensive nesting tends to add complexity to various computations and adversely affects performance.
- If you use a query based suite, ensure that each QBS returns a reasonable number of test cases. The upper limit on this is 20,000; but again if you have a QBS that returns 20,000 test cases you will have a hard time using the test plans UX :-)

#### How many test suites should I have in a test plan? How many test cases should I have in a test suite?
##### Test Suites
Well, there are no official numbers or limits defined for these. But we've seen performance problems crop up if below thresholds are exceeded:  
**Number of static test suites: 200 - 250**  
**Number of Requirement based suites (RBS): 20 - 30**  
**Number of Query based suites (QBS): ~5**  

##### Test Cases/Test Points
We recommend anywhere between 200-300 test points to show up per test suite. Now depending on the number of configuration you have for a test plan or test suite, the number can be adjusted accordingly for test cases.
For e.g. if I have 150 test cases that need to be executed across two configurations; I'd end up with 300 test points.

