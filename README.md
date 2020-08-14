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

## Usage

### Add Forms

Forms are handle as unique data files stored under `data/tnd-forms`

#### Form data

```yaml
id: contact
title: Contact Us
redirect: /thank-you/
submit: Get in touch
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
  If successful submision should redirect to a page (Not available on Formspree free plan)
- submit:
  Text for the submit button
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

#### Built in Field types

**text|phone|email|file|textarea|hidden**:
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

Any form can be using a specific provided amont the supported ones. To specify a global default provider, use this key.

#### css

A map containing certain HTML elements produced by the Module's templates. Builtin class key are:
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

**Editor** can print the form using the `tnd-form` shortcode on any content page.

```markdown
---
Contact US
---

Fill free to fill the form below to get in touch...

{{< tnd-form "contact" >}}
```

**Programmers** can call the code from any template file using the `tnd-forms/form.html` partial

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

## Customization

Module can load its own very basic styling by loading `tnd-forms/head.html` partial inside your project's template's `<head>` tag.

```html
<head>
  <title>My Website</title>
  {{ partialCached "tnd-form/head.html . }}
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

Check the original files for the available context. 

## Extanding

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

This project is maintained and love by [thenewDynamic](https://www.thenewdynamic.com).