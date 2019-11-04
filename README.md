# kots.io

## Prerequisites
1. hugo: Install with `brew install hugo` or `sudo apt-get install hugo`
2. yarn: Install with `brew install hugo` or `sudo apt-get install hugo`
3. Set up additional dependencies by running `make install`

## Running local dev instance
* Run `make dev` and a webserver will run locally at a URL similar to `http://localhost:1313/`

## Reindexing Search Locally
* Run `make index` and JSON index file will be created in public/algolia.json

## Reindexing Search
* Run `make index-and-send` and JSON index file will be created and sent to Algolia. Note that the ALGOLIA_WRITE_KEY environment variable needs to be set to the write key for your Algolia account to perform this step. See https://github.com/replicatedhq/hugo-algolia for more details. 




