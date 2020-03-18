#### Azure Test Plans - Best Practices
Hi there, to ensure that the Azure Test Plans experience is performant and reliable, we recommend the below best practices:

- Ensure that the test suites you have in a test plan are regularly/actively used in your test plan execution. 
If a test suite is not really required in a plan, it’s best to remove it. When ever you create a new Test Plan it's best to 
- On the same lines, to some extent, ensure that all test cases that show up within a test plan are also needed there for the test pass. 
Many times users keep cloning the test plan sprint over sprint with the test cases that are no longer required/valid and this 
ends up impacting performance later.
