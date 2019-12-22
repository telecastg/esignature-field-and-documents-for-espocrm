# esignature-field-and-documents-for-espocrm
Module to allow the use of an electronic signature canvas as a field and to generate full page documents that incorporate the electronic signature and can be printed using the browser's PDF engine

Steps to create an electronic signature document based on an EspoCRM entity:

1) Create a new entity or edit an existing one (the 'target entity') through the Administration module and add a field type 'eSignature' (the 'target field'). 

NOTE: If you are trying to add an eSignature field to a core EspoCRM entity (like 'account', 'contact', etc) do not modify those  entities, instead create a custom entity that clones the core entity and use that custom entity in your application in order to make the changes "upgrade safe" .

2) Create a template to display the target entity.

3) Include in the template a placeholder for the target field formatted as: '@@sig[target-field-name]/sig@@' where target-field-name is the name of the target field.

4) If you are using EspoCRM's default clientDefs for the custom entity (you do not have a custom record view for the target entity), modify the json object "recordViews" section as follows:

  BEFORE: 
  "recordViews": {
    "detail": "views/record/detail"
  }

  AFTER:
  "recordViews": {
    "detail": "esignature:views/record/detail"
  }

5) If you use a custom view instead, (eg: 'custom:views/{target entity}/record/detail') make the following changes to that script:

  BEFORE:
  Espo.define('custom:views/{target entity}/record/detail', 'views/record/detail', function (Dep) {

  AFTER:
  Espo.define('custom:views/{target entity}/record/detail', ['views/record/detail','esignature:views/record/detail'], function (Dep,,Esignature) {

  Make also the following changes to the rest of the script's code:

        setupActionItems: function () {
        
          (all existing code goes here)
          
          Esignature.prototype.setupActionItems.call(this,true);            
        },
        
        (all existing functions go here)
        
        actionDisplayEsignatureDocument: function () {  
            Esignature.prototype.actionDisplayEsignatureDocument.call(this,true);
        }

Instructions to generate the electronic signature document:

1) Open the target entity detail view and select 'display eSignature Document' from the top left dropdown menu.

2) Select the template created to display the document

3) The complete document with the electronic signature panels will be rendered so that a user can enter an electronic signature by drawing inside the canvas provided. The signature will be saved when the "Update" link on the top right corner of the signature panel is clicked.

4) After an electronic signature has been saved, it will no longer be possible to edit it and instead of an empty canvas, the electronic signature image will be displayed in the document.

5) To print the document, click on the button with a printer icon on the top left corner of the document. In order to generate a PDF select your browser's PDF printer as the printing device.

6) To close the document and return to the entity detail view, click on the button with the X at the top left corner of the document.
