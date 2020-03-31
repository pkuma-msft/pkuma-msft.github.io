#### Test Point status stays as in-porgress when you close Web Runner using the close button

Chrome 80 disabled a sync-XHR feature flag, allow-sync-xhr-in-page-dismissal, which prevents any synchronous network calls from happening during page unload or close event. You can read more about this here [https://www.chromestatus.com/features/4664843055398912](https://www.chromestatus.com/features/4664843055398912)

Because of this, the network call that used to happen when you closed web runner is now **not** happening. It is during this call that we used to clean-up an in-progress run if there were no updates to it.
 
As a temporary workaround you can disable this feature flag in Chrome. The instructions to disable the feature flag in Chrome are below:
1. Go to [chrome://flags/](chrome://flags/)  
2. Search for ```allow-sync-xhr-in-page-dismissal```  
3. Its value should be set to ```Default```. Change it to ```Enabled```
4. You will be prompted to relaunch the browser  

The issue should get resolved once you relaunch the browser.

If you're using Firefox or Edge Chromium or any other favorite browser of yours, you shouldn't be seeing this issue.  

We will take up the work to react to this change in Chrome in the coming sprint.

Related links  
[https://developercommunity.visualstudio.com/content/problem/946112/in-progress-test-runs-are-being-created-when-a-tes.html](https://developercommunity.visualstudio.com/content/problem/946112/in-progress-test-runs-are-being-created-when-a-tes.html)  
[https://developercommunity.visualstudio.com/content/problem/946291/testcase-outcome-stuck-on-in-progress-state-after-1.html](https://developercommunity.visualstudio.com/content/problem/946291/testcase-outcome-stuck-on-in-progress-state-after-1.html)
