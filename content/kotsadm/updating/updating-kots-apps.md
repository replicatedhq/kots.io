---
date: 2019-10-09
linktitle: "Updating a KOTS application"
title: Updating a KOTS application
weight: 10010
draft: false
---

The simplest way to update a KOTS application is through the "Version History" tab on the Admin Console. 
![Version History](/images/version-history.png)


## Checking for Updates
The Admin Console automatically checks for updates once every 15 minutes. To manually check for a more recent version, click the "Check for updates" button at the top of the admin console. When an update is available, a new upstream version will show in the list of released versions. 

![New Version Available](/images/new-version-available.png)

## Comparing Changes Between Releases
When a new version of the KOTS application is available, you can compare the changes for the new version(s) against the current version by clicking the "files changed" text in the the "Source" column. 

![New Changes](/images/new-changes.png)

Alternatively, changes can be reviewed between any arbitrary release by clicking the icon in the header of the "Source" column. Select the two versions to compare, and click "Diff releases" to show the relative changes between the two releases. 

![Diff Releases](/images/diff-releases.png)


## Preflight Checks
Click the "Preflight results" link to run the preflight checks defined by the application vendor. Based on the outcome of each preflight check, you can decide whether or not to perform the upgrade by clicking the "Continue" button. 

![Preflight Checks](/images/preflight-checks.png)

Preflight failures and warnings do not preclude the upgrade to a new version. The installer may elect to ignore these failures and proceed with the upgrade. 

## Updating 
An update is performed by clicking "Continue" on the preflight checks page, or by clicking the "Deploy" button on the "Version History" tab. At this point, the current cluster will be updated to the new version of the KOTS application and the "Deployed" status will be set on that version. 

![Updated Version](/images/updated-version.png)



