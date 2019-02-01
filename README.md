# sfos-upgrade
### Scripts for safe and automated upgrading of SailfishOS with logging
<br />

Upgrading SailfishOS at the GUI (per *Settings -> SailfishOS updates*) provides very little information about its progress / process / success, beyond reading `/var/log/systemupdate.log` after the upgrade.  This can make troubleshooting issues hard.<br />
Furthermore the GUI offers no control which SailfishOS version to upgrade to.

In contrast to that, Jolla's [guide how to upgrade SailfishOS at the command line](https://jolla.zendesk.com/hc/en-us/articles/360005795474) offers full control, while lacking any logs or safety checks.<br />
And it is tedious and error prone to issue multiple critical commands manually at the command line.

**sfos-upgrade** performs the steps to upgrade SailfishOS at the command line in an automated manner, while providing extensive safety measures plus full log output at the screen and in a log file.<br />
<br />

Safety measures:

* Check for free space on root filesystem.
* Check for BTRFS allocation, if the root filesystem uses BTRFS.
* Check for upgrading to a correct and available SailfishOS version.
* Check for "jumping over" a [stop release](https://jolla.zendesk.com/hc/en-us/articles/201836347?#4) (since v0.3).
* Automatically unapply all Patches, if Patchmanager is installed.
* Disable all OpenRepos' repositories, if upgrading from a SailfishOS version below 1.0.4.20 (since v0.4).
* Emit a warning when downgrading.
<br />

Usage (as root user):

* **sfos-upgrade [\<version\>]**<br />
  With a version number provided as parameter it sets SSU to this version and in release mode before upgrading.  This is the regular use case.<br />
  Without a version number it retrieves the one set for SSU to perform slightly relaxed checks and balances, but does not alter SSU's settings for upgrading.

* **sfos-upgrade -h|--help**<br />
  Emits a brief usage description.

When an upgrade succeeded, reboot and do not miss to run **post_sfos-upgrade** (as root) then!
Not running it will result in the huge upgrade log files (containing many duplicate entries) and may result in RPMs failing to install ("unmet dependency" / "Fatal error: nothing provides X needed by Y" errors) plus annoying notifications from the store-client that an upgrade to the installed version is available.

Logs are originally written to `/var/log/systemupdate_*.log-dupes.txt` and tidied by **tidy_log-dupes** (which is called by **post_sfos-upgrade**) to `/var/log/systemupdate_*.log.txt`.<br />
<br />

Notes:

* Built RPMs are available in the [release section](https://github.com/Olf0/sfos-upgrade/releases) and for easy installation under SailfishOS at [OpenRepos](https://openrepos.net/content/olf/sfos-upgrade).
* After a "factory reset" **sfos-upgrade** eases and speeds up the process of upgrading to a recent SailfishOS release via consecutively installing all "stop releases" on the way.<br />
Simply run `sfos-upgrade <intended version>`, reboot  and repeat: it will guide you through all [stop releases](https://jolla.zendesk.com/hc/en-us/articles/201836347?#4).<br />
Omit running `post_sfos-upgrade` between consecutive SailfishOS upgrades (but do reboot each time!), only run it once after having upgraded to the intended version.
* **sfos-upgrade** supports [all public SailfishOS releases](https://coderus.openrepos.net/whitesoft/sailversion) and should work fine with any release.
* **sfos-upgrade** does not check battery level and if charging, yet.
