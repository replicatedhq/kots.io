

## In this Tutorial
Learn how to manage customers and their licenses with the `replicated` CLI

### Prerequisites
* An application [release](todo) promoted to a [channel](todo)
* An [API Token](/vendor/guides/vendor-portal/team/api-tokens.md) for a member of your vendor team
* The [`replicated` CLI](todo) is installed and configured
* The member must have at least `platform/app/[:appId]/license/**` in their [RBAC](/vendor/guides/vendor-portal/team/rbac.md) policy
    > Note: the default `admin` profile includes these permissions

### Application Information

1. Determine the `APP_SLUG` (unique name) for the application

    ```shell
    replicated app ls
    ```

    The output should look similar to this:

    ```
    ID                        NAME     SLUG           SCHEDULER
    1qtlxxxxxxxxxxxxxxeQqaY   Sentry   sentry-bream   kots
    ```

    > The `APP_SLUG` in the above example is: `sentry-bream`

1. Determine the name of a Release Channel

    ```shell
    replicated channel ls --app APP_SLUG
    ```

    The output should look similar to this:

    ```
    ID                        NAME       RELEASE   VERSION
    1qtlxxxxxxxxxxxxxxeQqaY   Unstable   2         0.0.2
    ```

    > An example channel name is `Unstable`

1. Listing customers

    ```shell
    replicated customer ls --app APP_SLUG
    ```

## License Duration

Some licenses are meant to provide a limited amount of time for a customer to use and evaluate the application. These licenses are created with a time `duration`.

A license duration can expressed by designating a length of time in hours (`h`), minutes (`m`), and seconds(`s`).

> Example: Setting a duration of: `240h30m` means the license will expire in 10 days (240 hours) and 30 minutes.

Note: Hours (`h`) is the largest accepted unit of time

> Example: Setting a duration of `0` means the license will `never` expire.

1. Create a new customer and license

    The following information is required to create a new license using the CLI:

    * `CUSTOMER_NAME` : The name of the customer to create
    * `LICENSE_DURATION` : the length of time for the license to be valid.
        * Use `0` for a license that will never expire
    * `CHANNEL_NAME` : the release channel to associate with the customer
    * `APP_SLUG` : the unique name of the application

    ```shell
    replicated customer create \
      --name CUSTOMER_NAME \
      --expires-in LICENSE_DURATION \
      --channel CHANNEL_NAME \
      --app APP_SLUG
    ```

    You should expect output similar to:

    ```console
    $ replicated customer create \
      --name "Example Inc." \
      --expires-in "0" \
      --channel "Unstable" \
      --app "sentry-bream"
    
    ID                            NAME           CHANNELS   EXPIRES   TYPE
    1txCfXkXHi1m4wxVBv6KooFb3Te   Example Inc.   Unstable   Never     dev
    ```

## Download the Customer's License

Run the following command to download a customers license and save it to the file `license.yaml`

```shell
replicated customer download-license \
    --app APP_SLUG \
    --customer CUSTOMER_NAME > license.yaml
```
