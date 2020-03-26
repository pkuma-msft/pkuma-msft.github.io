#### Disabling auto-update of extensions on TFS on-premise (Azure DevOps Server)

When you install an extension from [Marketplace](https://marketplace.visualstudio.com) on a TFS server or Azure DevOps Server; the extensions get auto-updated as the publisher pushes new versions of the extension on Marketplace. This happens if your machine hosting the TFS server is connected to the internet. A scheduled job on TFS server checks for updates regularly and updates the extensions.

Some customers may be sensitive to automatically updating these extensions as it may end up breaking their scenarios. In such cases customers have an option to disable this auto-updating of extensions.

##### Disabling auto update of extensions
To disable auto-update you will need to run the below SQL script on the configuration database associated with your TFS instance. Depending on the version of the server you have, the database may be called ```Tfs_Configuration``` or ```AzureDevOps_Configuration```.  

```
 UPDATE [Tfs_Configuration].[dbo].[tbl_JobDefinition]
 SET    EnabledState = 2
 WHERE  PartitionId > 0 AND
    JobId = 'A8F60BD0-BCBA-4343-82B3-FD6B40D657D8'
```
** Make sure to change the database name above as appropriate **  

This script will disable auto-update for all installed extensions. You can however continue to install specific updates manually from Marketplace.