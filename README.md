# Forms Hugo Module

This module enables forms from several services on a Hugo Projects

## Supported Services

- Netlify Forms
- Formspree

## Requirements

Requirements:
- Go 1.14
- Hugo 0.61.0


## Installation

If not already, [init](https://gohugo.io/hugo-modules/use-modules/#initialize-a-new-module) your project as Hugo Module:

```
$: hugo mod init {repo_url}
```

Configure your project's module to import this module:

```yaml
# config.yaml
module:
  imports:
  - path: github.com/theNewDynamic/hugo-module-tnd-forms
```

## Providers

Currently the Module supports [Netlify Forms](https://www.netlify.com/products/forms/) and [Formspree.io](https://formspree.io/). Any form can use any of the supported provider regardless of the default provider of the website.

### Formspree

Formspree requires the Form ID passed to the Form's Data as `formspree_id`

## Usage

### Create Forms

Forms are handle as unique data files stored under `data/tnd-forms`

#### Form data

```yaml
id: contact
# if using Formspree
formspree_id: sxewrre33
title: Contact Us
redirect: /thank-you/
submit: Get in touch
recaptcha: 6LctEP0fAAAxxxxxxxxxxxxxxxxxxxxxx
fields:
- name: name
  required: true
  label: Name
- name: message
  label: Message
  type: textarea
```

- id: 
  Will be used to identify the form when calling from shortcode.
- title: 
  The title displayed above the form
- redirect: 
  If successful submission should redirect to a page (Not available on Formspree free plan)
- submit:
  Text for the submit button
- recaptcha:
  [reCaptcha](https://www.google.com/recaptcha/about/) Site Key, if set, the form will load the recaptcha script and load the g-recaptcha div
- fields:
  List of fields for which data will be detailed below

#### Field data

```yaml
- name: name
  label: Full Name
  type: text
```

- name:
  String to be used as name attribute
- label:
  String to be used as label. Can include markdown.
- type:
  The type of input as detailed below.

### Add form to pages.

A single argument shortcode can be added to any content file's body. The argument should be a string matching the form's `id`.

```markdown
---
title: Contact Us
---

Fill free to fill the form below to get in touch...

{{< tnd-form "contact" >}}
```

### Add form to templates.

Any form can be invoked from within a template using the `tnd-forms/form.html` partial. The context should be a string matching the form's `id`.

```html
<!-- layouts/_default/contact.html -->
{{ define "main" }}
<section class="content">
  {{ .Content }}
</section>
<section class="contact">
  {{ partial "tnd-forms/form" "contact" }}
</section>
{{ end }}
```

#### Built in Field types

**text|phone|email|file|textarea**:
Will use the same template with the defined type attribute
```yaml
  - name: name
    label: Full Name
    type: text
```

**select**:
  A select field. Takes one extra key (`options`)

```yaml
  - name: state
    label: State
    type: select
    options: 
    - Alabama
    - Alaska
    ...
```

**checkboxes|radios**:
  A multiple choice field using either checkboxes or radios. Use `radios` type for radios and `checkboxes` for multiple checkboxes.

```yaml
  - name: source
    label: How did you hear from us?
    type: radios
    options:
      - A friend
      - Internet
      - Ad
```

**checkbox**

  A single checkbox field

```yaml
  - type: checkbox
    name: agree
    label: Do you agree with everything we will ever say?
```

**hidden**

  A hidden field

```yaml
  - type: hidden
    name: lang_code
    # `label` will be used to fill the `value` attribute if `value` is not set:
    label: en
    value: en
```

In absence of the `value` setting, the module will use a hidden field's `label` setting for the `value` attribute.

### Settings

Settings are added to the project's parameter under the `tnd_forms` map as shown below.

```yaml
# config.yaml
params:
  tnd_forms:
    provider: formspree
    css:
      submit: border p-4
```

#### provider

Any form can be using a specific provided among the supported ones. To specify a global default provider, use this key. Default is `netlify`.

#### css

A map containing certain HTML elements produced by the Module's templates. Built-in class key are:
  - form
  - control
  - hidden 
  - submit 
  - input 
  - textarea 
  - select

### Examples

With the following data file.

```yaml
# data/tnd-forms/contact-form.yaml
id: contact
title: Contact Us
redirect: /thank-you/
submit: Get in touch
fields:
  - name: name
    required: true
    label: Name
  - name: email
    label: Email
    type: email
    required: true
  - name: reason
    label: Reason for inquiry
    type: select
    options:
      - Troubleshoot
      - Delivery
      - Financial
  - name: message
    label: Message
    type: textarea
```

## Customization

Module can load its own very basic styling by loading `tnd-forms/head.html` partial inside your project's template's `<head>` tag.

```html
<head>
  <title>My Website</title>
  {{ partialCached "tnd-forms/head.html" . }}
</head>
```
**WARNING**: The above requires Hugo's extended version for SCSS processing.

### Styling through CSS classes.

User can define which CSS classes to be added to each HTML elements printed by the Module. See config

### Styling through HTML templates

Overwriting any input type HTML template is easy. Simply add to your project `layouts/partials/tnd-forms/inputs` directory any file named after an existing input type.
- default.html
- select.html
- hidden.html
- textarea.html
etc...

Check the [original files](https://github.com/theNewDynamic/hugo-module-tnd-forms/tree/main/partials/inputs) for the available context. 

## Extending

### Adding your own field type.

The module will pickup any templates from the `layouts/partials/tnd-forms/inputs` matching the `type` set in a form's field's data.

To create your fancy color picker, simply create its HTML at `layouts/partials/tnd-forms/inputs/color-picker.html`

And add the following field to one of your fields:
```yaml
  - name: color
    label: Color
    type: color-picker
    colors:
      - '#444'
      - '#ccc'
```

Note that any data passed to the field will be made available to your template's context alongside the setting's `css` object.

## theNewDynamic

This project is maintained and loved by [thenewDynamic](https://www.thenewdynamic.com).