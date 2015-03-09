# Integrating Nuxeo and Drupal

The goal of this project is to explain how Nuxeo can be integrated into Drupal 7.
The idea is to create a new field into nodes likes Articles to select and display Nuxeo Content.
This can also be seen as the what is needed in Drupal 7 to set up a custom field.

## Nuxeo integration

Nuxeo offers a PHP client that is being described here:
[http://doc.nuxeo.com/x/HwJu](http://doc.nuxeo.com/x/HwJu)

You can see here that it is used to get the title of a document from its ID in `nuxeo_content_field_formatter_view` of nuxeo_content.module.

The formatter view also display a link to the small size of the picture, which requires the user to authenticate to Nuxeo (a proxy to dedicated user would be a better solution).

**The search part of the module in edit mode is handled by a plugin that needs to be installed Nuxeo side:**

[https://github.com/fvadon/nuxeo-simplesearchframe](https://github.com/fvadon/nuxeo-simplesearchframe)

It is displayed in Drupal through an Iframe, and communication between the 2 parties is made trough post messages.

## General logic of Drupal

This part aims at explaining a few hints of the Drupal logic. It does not replace exploring Drupal documentation but can be a starting point.
Drupal custom dev are called modules. In Drupal you have several kind of objects called "nodes". IT can be blocks, menues, fields...

A module is composed of a few files:

- module_name.info that describes the module (kind of manifest)
- module_name.install that gives statement for the installation of the module (like creating a new field in DB table). 
- module_name.module which is is the module itself containing the code (PHP).

Here we want a new field type in articles so we can use [http://cgit.drupalcode.org/examples/tree/field_example](http://cgit.drupalcode.org/examples/tree/field_example) as an example.

Drupal exposes "hooks". Hooks are like interface, you have to implement specific hooks to create a new bloc, a new field...

For instance, providing a field requires (among other things):

- Defining a field:
	- hook_field_info()
	- hook_field_schema()
	- hook_field_validate()
	- hook_field_is_empty()
- Defining a formatter for the field (the portion that outputs the field for display):
	- hook_field_formatter_info()
	- hook_field_formatter_view()
- Defining a widget for the edit form:
	- hook_field_widget_info()
	- hook_field_widget_form()
	
Each hook should return specific information which can is documented in the hook documentation or can be guessed by looking at the examples.
	
Installation is just done by putting the files at the right place in the installation folder of Drupal and reload the module page (as it is just PHP).

A custom widget element is also defined.


## Installation and requires

## Installation

As any drupal module, you can just put all the files in the sites/all/modules folder of your drupal installation.
It will create a new field type available for your different content types.

### Nuxeo PHP Automation Client

Nuxeo PHP Automation client files are included in the module, taken from:

[https://github.com/nuxeo/nuxeo-automation-php-client/tree/master/NuxeoAutomationClient](https://github.com/nuxeo/nuxeo-automation-php-client/tree/master/NuxeoAutomationClient)


### Nuxeo Simplesearchframe
As described above, a plugin needs to be installed on the Nuxeo server.


## Troubleshooting.

Here are a few hints for issues and question encountered during the implementation of this module

### Implementing new hooks

In order for your new hooks to be loaded, you need to reload the module in the module page.
Once it is done you can modify that hook and reload just by saving the file and reloading the current page in Drupal, but you need to the full module she you implement new hooks...

### widget_form()

The edit mode of the field is defined by the hook hook_field_widget_form() where Drupal tells us to create form elements for our field's widget. It means that it should return and element containing form properties. The list of available for properties can be found at:

[https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7](https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7)

For instance the type (checkbox, text filed...) is the first property to set up, see the example referenced earlier to see how to format the output of the function.

## hook_field_formatter view()
As explained by the doc, it has to return a renderable array of items, more explanation at:

[https://www.drupal.org/node/930760](https://www.drupal.org/node/930760)

