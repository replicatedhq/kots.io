---
date: 2019-10-09
linktitle: "Config"
title: Config
description: "Config is the vendor-supplied Config screen and input for template function rendering."
weight: 2
aliases:
  - /reference/config/items
---

The Config custom resource can be provided by a vendor to specify a Config screen for collecting customer supplied values and template function rendering.

The admin console settings screen configuration is specified as an array configuration groups and items.

## Groups

Groups are comprised of a `name`, `title`, `description` and an array of items.

```yaml
spec:
  groups:
  - name: authentication
    title: Authentication
    description: Configure application authentication below.
```

**Note:** `description` is only supported in `groups`, see `help_text` property for `items`. [Markdown](https://guides.github.com/features/mastering-markdown/) syntax is supported in this property.

## Items

Items map to input fields and belong to a single group. All items should have `name`, `title`
and `type` properties. Specific item types can including new types.

### Available Item Types
- `bool`
- `file`
- `heading`
- `label`
- `password`
- `text`
- `textarea`
- `select_many`
- `select_one`

## Examples

### `bool`
The `bool` input type should use a "0" or "1" to set the value
```yaml
    - name: toggles
      items:
      - name: http_enabled
        title: HTTP Enabled
        type: bool
        default: "0"
```

### `label`
The `label` type allows you to display an input label.
```yaml
    - name: Email
      items:
      - name: email-address
        title: Email Address
        type: text
      - name: description
        type: label
        title: "Note: The system will send you an email every hour."
```

### `heading`
The `heading` type allows you to display a group heading as a subelement within a group. This is useful when you would like to group items all in one config group but would like to separate the items visually, for instance when using a config group test proc. **Currently, the `heading` item type is not supported**
```yaml
    - name: ldap_settings
      title: LDAP Server Settings
      items:
      ...
      - name: ldap_schema
        type: heading
        title: LDAP schema
      ...
```

### `select`
Types `select_one` and `select_many` are special cases. These types must have nested items
that act as options. These types will be displayed as radio buttons (`select_one`) or
checkboxes (`select_many`) in the admin console. **Currently, the `select_many` item type is not supported**

At this time these two control types do not support the `title` field.

```yaml
    - name: inputs
      title: Inputs
      description: ""
      items:
      - name: logstash_input_enabled
        default: ""
        type: select_many
        items:
        - name: logstash_input_file_enabled
          title: File
          default: "0"
        - name: logstash_input_lumberjack_enabled
          title: Lumberjack
          default: "0"
    - name: authentication
      title: Authentication
      description: ""
      items:
      - name: authentication_type
        default: authentication_type_anonymous
        type: select_one
        items:
        - name: authentication_type_anonymous
          title: Anonymous
        - name: authentication_type_password
          title: Password
```

### `textarea`
A `textarea` can specify a `props` that will map into the HTML element directly. Examples of available properties are `rows`, `cols` and `wrap`. See https://www.w3schools.com/tags/tag_textarea.asp for reference.

```yaml
    - name: custom_key
      title: Set your secret key for your app
      description: Paste in your Custom Key
      items:
      - name: key
        title: Key
        type: textarea
        props:
          rows: 8
      - name: hostname
        title: Hostname
        type: text
```

### `file`
A `file` is a special type of form field that renders an [`<input type="file" />`](https://www.w3schools.com/tags/tag_input.asp) HTML element. It has both a value, which holds the file name, and data, the contents of the file. See the [`ConfigOptionData`](/reference/template-functions/config-context/#configoptiondata) template function for examples on how to use the file contents in your application.

```yaml
    - name: certs
      title: TLS Configuration
      items:
      - name: tls_private_key_file
        title: Private Key
        type: file
      - name: tls_certificate_file
        title: Certificate
        type: file
```

## Properties

### `default` and `value`
A default value will be applied to the ConfigOption template function when no value is
specified. It will appear as placeholder text in the settings section of the On-Prem Console.

A value is data that will be overwritten by user input on non-readonly fields.  It will appear as
the HTML input value in the settings section of the On-Prem Console.

```yaml
    - name: custom_key
      title: Set your secret key for your app
      description: Paste in your Custom Key
      items:
      - name: key
        title: Key
        type: text
        value: ""
        default: change me
```

### `required`
A required field will prevent the application from starting until it has a value.
```yaml
        required: true
```

### `when`
The when value is used to denote conditional inputs that will only be visible (or required) when the condition evaluates to true. The `when` item can be used on groups, items and select_one or select_many options.

The settings UI will update right away when a field used in a when clause is updated (no need to save) and can be used to used to show optional config sections. The equality check should match exactly without quotes.

```yaml
    - name: database_settings_group
      items:
      - name: db_type
        type: select_one
        default: external
        items:
        - name: external
          title: External
        - name: embedded
          title: Embedded DB
      - name: database_host
        title: Database Hostname
        type: text
        when: '{{repl (ConfigOptionEquals "db_type" "external")}}'
      - name: database_password
        title: Database Password
        type: password
        when: '{{repl (ConfigOptionEquals "db_type" "external")}}'
```

### `recommended`
An item can be recommended. This item will bear the tag "recommended" in the admin console. **Presently only works for boolean and text field types**
```yaml
    - name: toggles
      items:
      - name: http_enabled
        title: HTTP Enabled
        type: bool
        default: "0"
        recommended: true
```

### `hidden`
Items can be hidden. They will not be visible if hidden.
```yaml
        - name: secret_key
          title: Secret Key
          type: password
          hidden: true
          value: "{{repl RandomString 40}}"
```

When used in conjunction with [RandomString](/reference/template-functions/static-context/#randomstring)
- If set to `true`, the `value` is **persistent** between Config changes but it **cannot** be modified because its not visible in HTML.
- If not set or set to `false`, the `value` is **persistent** between Config changes. It **can** be modified because it is visible in HTML.

### `readonly`
Items can be readonly.
```yaml
        - name: unique_id
          title: Unique ID
          type: text
          value: "{{repl RandomString 20}}"
          readonly: true
```

When used in conjunction with [RandomString](/reference/template-functions/static-context/#randomstring)
- If set to `true`, the `value` is **ephemeral** between Config changes. It **cannot** be modified because it is greyed out in HTML.
- If not set or set to `false`, the `value` is **persistent** between Config changes. It **can** be modified because its not greyed out in HTML.

### `affix`
Items can be affixed left or right. These items will appear in the admin console on the same line.
```yaml
    affix: left
```

### `help_text`
This is similar to `description` but for `items`. This property can show a helpful message below `title`. **[Markdown](https://guides.github.com/features/mastering-markdown/) syntax is supported.**
```yaml
    - name: toggles
      items:
      - name: http_enabled
        title: HTTP Enabled
        help_text: When enabled we will listen to http
        type: bool
```
