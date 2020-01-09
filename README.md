# kots.io

## Running the docs
* Run `hugo serve`

## Validating the docs (e.g., to check for broken links)
* Ensure that you have httm-proofer. If not, run `gem install html-proofer` to install. 
* Run `hugo -v -s .`
* Run `htmlproofer --allow-hash-href --check-html --empty-alt-ignore --url-ignore /kots.io/css/ "./public"`



