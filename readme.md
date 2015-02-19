# Integrating Nuxeo and Drupal

The goal of this project is to explain how Nuxeo can be integrated into Drupal.
The idea is to create a new field into nodes likes Articles to select and display Nuxeo Content.
This is an ongoing work.


## General logic of Drupal

This part aims at explaining a few hints of the Drupal logic. It does not replace exploring Drupal documentation but can be starting point.
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


## REQUIRES AND WARNING

### Nuxeo PHP Automation Client


Nuxeo PHP Automation client files should be place directly in the module (same level as the .module file). 

The required files are: 

+ NuxeoAutomationUtilities.php
+ NuxeoAutomationAPI.php

And they can be found at: [https://github.com/nuxeo/nuxeo-automation-php-client/tree/master/NuxeoAutomationClient](https://github.com/nuxeo/nuxeo-automation-php-client/tree/master/NuxeoAutomationClient)

### Security

**No specific security is done here and the calls are made with Administrator/Administrator on the local server**

### Error handling
No error handling is done for now.


## Nuxeo integration

Nuxeo offers a PHP client that is being used here:
[http://doc.nuxeo.com/x/HwJu](http://doc.nuxeo.com/x/HwJu)

You can see here that it is used to get the title of a document from its ID in `nuxeo_content_field_formatter_view` of nuxeo_content.module.

## Troubleshooting.

Here are a few hints for issues and question encountered during the implementation of this module

### widget_form()

The edit mode of the field is defined by the hook hook_field_widget_form() where Drupal tells us to create form elements for our field's widget. It means that it should return and element containing form properties. The list of available for properties can be found at:

[https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7](https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7)

For instance the type (checkbox, text filed...) is the first property to set up, see the example referenced earlier to see how to format the output of the function.