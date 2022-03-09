# kots.io

As of March 8, 2022, the kots.io documentation is no longer maintained on this site. For up-to-date documentation, see [docs.replicated.com](https://docs.replicated.com/)

## Running the docs
* Run `hugo serve`

## Validating the docs (e.g., to check for broken links)
* Ensure that you have httm-proofer. If not, run `gem install html-proofer` to install. 
* Run `hugo -v -s .`
* Run `htmlproofer --allow-hash-href --check-html --empty-alt-ignore --url-ignore /kots.io/css/ "./public"`

## Create new releases with:
```bash
$ hugo new release-notes/<version>.md
```

## Create shortcodes
```bash
{{< notes title="Note Title" >}}
This is a note.
{{< /notes >}}

{{< warning title="Warning Title" >}}
This is a warning.
{{< /warning >}}

{{< callout >}}
This is a callout.
{{< /callout >}}
```
