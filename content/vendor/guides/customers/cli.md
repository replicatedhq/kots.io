# Managing Customers with the Replicated CLI

# In this Tutorial
Learn how to manage customers and their licenses with the `replicated` CLI

# Prerequisites
* An application [release](todo) promoted to a [channel](todo)
* An [API Token](/vendor/guides/vendor-portal/team/api-tokens.md) for a member of your vendor team
* The [`replicated` CLI](todo) is installed and configured
* The member must have at least `platform/app/[:appId]/license/**` in their [RBAC](/vendor/guides/vendor-portal/team/rbac.md) policy
    > Note: the default `admin` profile includes these permissions

# Instructions

## Useful commands

1. Determine the `SLUG` name of your application

    ```shell
    replicated app ls
    ```

    The output should look similar to this:

    ```
    ID                             NAME       SLUG               SCHEDULER
    1qtlxxxxxxxxxxxxxxxxxxeQqaY    Sentry     sentry-bream       kots
    ```

    > The SLUG name is `sentry-bream` in this example Output

1. Determine the name of a Release Channel

    ```shell
    replicated channel ls \
      --app "<SLUG name>"
    ```

    The output should look similar to this:

    ```
    ID                             NAME        RELEASE    VERSION
    1rCrxxxxxxxxxxxxxxxxxxeweq2    Unstable    2          0.0.2
    ```

    > An example channel name is `Unstable`

1. Listing customers

    ```shell
    replicated customer ls --app "<SLUG name>"
    ```

## Create a Customer

1. Determine the duration of the license

    Using a duration of `0` is interpreted as `never` expiring

    Specific durations are expressed by designating hours (`h`), minutes (`m`), and seconds(`s`). `240h30m` sets the license to expire in 10 days (240 hours) and 30 minutes

    > Note: Hours (`h`) is the largest accepted unit of time

1. Create the customer

    You will need the following information:

    * The Customer name
    * The license duration (`0` if it never expires)
    * The channel Name (see [Useful commands](##markdown-header-useful-commands))
    * The app's SLUG name (see [Useful commands](##markdown-header-useful-commands))

    ```shell
    replicated customer create \
      --name "<Customer name>" \
      --expires-in "<License Duration>" \
      --channel "<Channel name>" \
      --app "<App name>"
    ```

    You should expect output similar to

    ```console
    $ replicated customer create --name "Example Inc." --expires-in "0"  --channel "Unstable" --app "sentry-bream"
    ID                             NAME              CHANNELS     EXPIRES    TYPE
    1txCfXkXHi1m4wxVBv6KooFb3Te    Example Inc.      Unstable     Never      dev
    ```

## Download the Customer's License

1. Run the following command to download a customers license and save it to the file `license.yaml`

    ```shell
    replicated customer download-license --app "<SLUG name>" --customer "<Customer name>" > license.yaml
    ```
