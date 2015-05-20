# Integrating Nuxeo and Drupal

The goal of this project is to explain how Nuxeo can be integrated with Drupal 7.
The idea is to create a new field into nodes likes Articles to select and display Nuxeo Content.
This can also be seen as what is needed in Drupal 7 to set up a custom field.

## Installation and requires

This module requires installing a Drupal module that will call a specific Nuxeo module. It is highly recommended that you put a reverse-proxy between the 2 to simplify authentication.


## Drupal side

As any Drupal module, you can just put all the files in the sites/all/modules folder of your Drupal installation and enabling the module ``Nuxeo Labs Content``
It will create a new field type available for your different content types.

## Nuxeo side

**The search part of the module in edit mode is handled by another plugin that needs to be installed on Nuxeo server side:**

[https://github.com/fvadon/nuxeo-simplesearchframe](https://github.com/fvadon/nuxeo-simplesearchframe)

It is displayed in Drupal through an Iframe, and communication between the 2 parties is made through post messages.

Nuxeo also offers a PHP client that is being described here:
[http://doc.nuxeo.com/x/HwJu](http://doc.nuxeo.com/x/HwJu)

The Drupal client is used for updating Nuxeo external references trough this Nuxeo plugin: 
[https://github.com/fvadon/nuxeo-external-reference
](https://github.com/fvadon/nuxeo-external-reference)

## Reverse-proxy

The display of the iframes (edit) an the final picture (view) are made through a direct link to Nuxeo. So it means you need a way to authenticate to Nuxeo. 

In the case of Drupal contents, you would probably want everyone to have the same access on Nuxeo to avoid referring to an Picture when editing that ends up not being seen by another user in view mode. The best solution to do that is to access to Nuxeo from Drupal via a dedicated reverse-proxy that will authenticate to Nuxeo with a specific user (by setting the header for instance). The reverse-proxy is also a good way to add some caching on the resources.


## General logic of the Drupal Module

This part aims at explaining how is implemented this module. 

Drupal custom dev are called modules. In Drupal you have several kind of objects called "nodes". It can be blocks, menues, fields...

A module is composed of a few files:

- module_name.info that describes the module (kind of manifest)
- module_name.install that gives statement for the installation of the module (like creating a new field in DB table). 
- module_name.module which is is the module itself containing the code (PHP).

Here we want a new field type in articles so we can use [http://cgit.drupalcode.org/examples/tree/field_example](http://cgit.drupalcode.org/examples/tree/field_example) as an example.

Drupal exposes "hooks". Hooks are like interface, you have to implement specific hooks to create a new bloc, a new field...

Each hook should return specific information which is documented in the hook documentation (also references some implementation as examples).
	
Installation is just done by putting the files at the right place in the installation folder of Drupal and reload the module page (as it is just PHP) and then enabling the module.

### New field
For instance, providing a field requires (among other things):

- Defining a field:
	- ``hook_field_info()``
	- ``hook_field_schema()``
	- ``hook_field_validate()``
	- ``hook_field_is_empty()``
- Defining a formatter for the field (the portion that outputs the field for display):
	- ``hook_field_formatter_info()``
	- ``hook_field_formatter_view()``
- Defining a widget for the edit form:
	- ``hook_field_widget_info()``
	- ``hook_field_widget_form()``

### Field instance settings

``nuxeo_content_field_info`` defines some instance_settings, they are set up thanks to the hook ``nuxeo_content_field_instance_settings_form()``

### Custom widget element

Usually widgets uses standard Drupal elements like a textfield or a select box. In our case we needed a custom element to display the iframe and communicate with it. So a specific element has been declared, with its own specific theme:

- ``nuxeo_content_element_info()``
- ``nuxeo_asset_theme()``
- ``theme_nuxeo_asset_iframe_element()``


### Rules Action for referencing external content to Nuxeo
The following plugin [https://github.com/fvadon/nuxeo-external-reference](https://github.com/fvadon/nuxeo-external-reference) enables Nuxeo to track what assets are used by external systems trough Rest Calls to Nuxeo when the content is updated or deleted.

2 actions have been made available through the nuxeo_content_rules_action_info() to call that plugin

To use it, it requires the Rules plugin in Drupal to be enabled ([https://www.drupal.org/project/rules](https://www.drupal.org/project/rules)).

## Troubleshooting.

Here are a few hints for issues and question encountered during the implementation of this module.

### Implementing new hooks

In order for your new hooks to be loaded, you need to reload the module in the module page.
Once it is done you can modify that hook and reload just by saving the file and reloading the current page in Drupal, but you need to reload the full module you implement new hooks...

### widget_form()

The edit mode of the field is defined by the hook hook_field_widget_form() where Drupal tells us to create form elements for our field's widget. It means that it should return an element containing form properties. The list of available for properties can be found at:

[https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7](https://api.drupal.org/api/drupal/developer%21topics%21forms_api_reference.html/7)

For instance the type (checkbox, text filed...) is the first property to set up, see the example referenced earlier to see how to format the output of the function.

## hook_field_formatter view()
As explained by the doc, it has to return a renderable array of items, more explanation at:
[https://www.drupal.org/node/930760](https://www.drupal.org/node/930760)

# License
(C) Copyright 2015 Nuxeo SA (http://nuxeo.com/) and others.

All rights reserved. This program and the accompanying materials
are made available under the terms of the GNU Lesser General Public License
(LGPL) version 2.1 which accompanies this distribution, and is available at
http://www.gnu.org/licenses/lgpl-2.1.html

This library is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU
Lesser General Public License for more details.

Contributors:
Frederic Vadon 

# About Nuxeo

Nuxeo provides a modular, extensible Java-based [open source software platform for enterprise content management](http://www.nuxeo.com) and packaged applications for Document Management, Digital Asset Management and Case Management. Designed by developers for developers, the Nuxeo platform offers a modern architecture, a powerful plug-in model and extensive packaging capabilities for building content applications.

