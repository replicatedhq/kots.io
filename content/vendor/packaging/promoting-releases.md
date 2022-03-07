---
date: 2019-10-23
linktitle: "Promoting Releases"
title: Promoting Releases
weight: 20040
---

Every Replicated license points to a Release Channel.
When a license is installed, it will pull down and install the release that is currently at the top of its channel.
It’s recommended to create customer licenses on the Stable channel, and only promote releases to Stable that are ready for all customers to install.

Once an application is installed, the active instance can be updated by promoting a release to the channel that instance is licensed to (likely Stable).
Each instance will periodically check for new releases.
When a new release is found, the Replicated installation will show a button that allows end customers managing the instance to read the release notes and install the update.
A license only checks it’s own release channel.

To promote a release, you can use the [vendor portal](https://vendor.replicated.com) and click the Promote button:

![Promote Button](/images/promote-button.png)

When a release is promoted it should be given a version label and detailed release notes.
The release notes support markdown and will be shown to your customer.
Additionally, each promoted release must be given a required status (required or not required).

#### Notes

- Before you can create or install a license, a release must be promoted to the channel.
- Update checking defaults to every 15 minutes but can be configured by end customers.
- It is possible to change a license value to have updates automatically installed when detected by the running instance.
- License values are synced with the values set in the vendor portal when the customer syncs the license.
- Releases will not be editable after being promoted to a channel.
- Release notes, version numbers, and the required status may be edited after promotion by visiting the channel’s history.

## Semantic Versioning

Semantic versioning is available in KOTS v1.58.0 and later.

### Enabling Semantic Versioning

Semantic versioning must be enabled on a per-channel basis. In the vendor portal, select **Enable semantic versioning** when creating a channel or when editing the settings for an existing channel. Semantic versioning can also be enabled and disabled for an existing channel with the [`kots channel enable-semantic-versioning`](/vendor/cli/channel-enable-semantic-versioning) and [`kots channel disable-semantic-versioning`](/vendor/cli/channel-disable-semantic-versioning) commands, respectively.

When semantic versioning is enabled for a channel, the version label for a release promoted to that channel is validated to ensure that it is a valid semantic version. For more information about semantic versioning, see [Semantic Versioning 2.0.0](https://semver.org). 

In the end user environment, the admin console sequences releases by their semantic versions instead of their creation dates.

If releases that do not use a valid semantic version are already promoted to the channel, the admin console will sort any releases that do use a semantic version, but it will not sort versions that do not use a semantic version. Starting with the oldest version and proceeding to the most recent, semantic versions will bubble down to their appropriate location in the list. For example, if 1.0.0, abc, 0.1.0, xyz, and 2.0.0 are promoted in that order, the version history will be sequenced as follows after semantic versioning is enabled for the channel: 0.1.0, 1.0.0, abc, xyz, 2.0.0.

If you enable semantic versioning for a channel and then promote releases to it, we recommend that you do not later disable semantic versioning for that channel.

### Default Behavior

For new applications created in the vendor portal, the Stable and Beta channels will have semantic versioning enabled by default, while the Unstable channel will have semantic versioning disabled by default. For applications created before semantic versioning became the default, no change in original behavior is observed. In both cases, new channels created for the application will have semantic versioning disabled by default.

For more information about checking for updates in the admin console, see [Checking for Updates](/kotsadm/updating/updating-kots-apps/#checking-for-updates).
