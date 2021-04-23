## Contributing Guidelines
When contributing to the KOTS documentation, please consider the following writing styles:

- ### Avoid acronyms. If using one, make sure to define it on first use.

- ### Favor writing in the [active voice](http://writing.wisc.edu/Handbook/CCS_activevoice.html).
**Yes:**  The next section includes some basic information about our app release including the app name.

**No:**  In the next section some basic information is included about our app release including the app name.

- ### Favor writing in the [indicative mood](https://grammarist.com/grammar/english-moods/).
**Yes:**  At runtime, Replicated will rewrite the image tag to pull from `registry.replicated.com`.

**No:**  At runtime, Replicated would rewrite the image tag to pull from `registry.replicated.com`.

- ### Avoid Second Person Pronouns
**Yes:** A license value can be used to show and hide fields on the settings page.

**No:** You can use your license to allow customer access to hidden settings.

- ### Avoid First Person Pronouns
**Yes:** The Go playground can be used to validate the regular expression.

**No:** Here I have used the Go playground to validate the regular expression.

- ### Use allow/deny instead of whitelist/blacklist
**Yes:** The following IP addresses should be added to the firewall allow-list.

**No:** The following IP addresses should be whitelisted.

- ### Use primary/secondary instead of master/slave when describing systems
**Yes:** Run the primary redis instance.

**No:** Run the redis master instance.

- ### Do not reference relative time from when the document was written.
**Yes:** KOTS 1.15.0 includes a new feature to...

**No:** A new feature of KOTS is...

- ### One sentence per line. It's easier to review and comment on. Markdown will render these properly.
**Yes:** Do something.
Do something else.

**No:** Do something. Do something else.

- ### Use relevant, descriptive anchor text in links.
**Yes:** ```More information is available in the <a href="/link">release notes</a>.```

**No:**  ```For more information, read the release notes <a href="/link">here</a>.```

- ### Only use Markdown backticks on code or command literals.
The backtick symbol shouldn't be used for emphasis.

- ### Code snippets in bash should not include sudo.
**Yes:**  ```docker logs -f replicated```

**No:** ```sudo docker logs -f replicated```

- ### Multi-line code snippets should not have the bash prompt.
**Yes:**
```
docker rmi quay.io/replicated/replicated
docker pull quay.io/replicated/replicated
```
**No:**
```
$ docker rmi quay.io/replicated/replicated
$ docker pull quay.io/replicated/replicated
```

- ### Avoid `<h1>` tags on a page.
Use`<h2>` tags to separate page content. If there's a need for another `<h1>`, consider a new document.

- ### Do not end header text with a colon.
**Yes:** `## Installation Guide`

**No:** `## Installation Guide:`

- ### Use hyphens in filenames, not underscores.

- ### When referring to the KOTS product, use all capital letters.
**Yes:** A KOTS application can include...

**No:** A Kots application can include...
**No:** A kots application can include...

- ### When referring to airgapped enviornments, use "airgapped" not "airgap" unless refering to a bundle.
**Yes :** The airgap bundle is finished.
**Yes :** It's and airgapped server.

**No:** The airgapped bundle is finished.
**No :** It's an airgap server.

- ### The product and the company are named Replicated (with a capital R).
**Yes:** How to install Replicated.

**No:** How to install replicated.

- ### The kotsadm Admin Console is referred to as the Admin Console
**Yes:** In the Admin Console...

**No:** In kotsadm...

- ### Use the following terms, when referring to components and parts of the ecosystem.
  - *distribution(s)* of Linux, not *flavors*
  - The kotsadm utility should be referred to as the *Admin Console*, not *management console* or *on-prem management console*
  - *snapshots* not *backups*

- ### None of these rules are absolute
It's perfectly acceptable (and often reasonable) to break these rules on occasion. No writing style can cover 100% of the possible use cases.

## Testing locally

Run `hugo serve` in the root directory of this repo to view the local version of the site in the browser.

## Screenshots

License used to produce screenshots for all documents can be downloaded [here](https://kots.io/sample-license)
