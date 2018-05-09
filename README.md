# Factur-X Python library

Factur-X is a EU standard for embedding XML representations of invoices in PDF files. This library provides an interface for reading, editing and saving the this metadata.

Since there are multiple flavors of the embedded XML data, this library abstracts them into a Python `dict`, which can be used to load and save from/to different flavors.

## Installation

`sudo pip install --upgrade factur-x`

## Usage

Load PDF file without XML and assign some values.
```
from invoicex import InvoiceX

inv = InvoiceX('some-file.pdf')
inv.due_date = datetime(2018, 10, 10)
inv.seller.name = 'Smith'
inv.buyer.country = 'France'
```

Validate and save PDF including XML representation.
```
inv.validate()
inv.save(flavor='zugferd')
```

Load PDF *with* XML. View and update fields via pivot dict.
```
inv = InvoiceX('another-file.pdf')
inv_dict = inv.as_dict()
inv_dict['currency'] = 'USD'
inv.update(inv_dict)
inv.validate()
```

Save XML metadata in original formats and remove.
```
inv.write_xml('metadata.xml', flavor='same')
inv.write_xml('metadata.xml', flavor='zugferd')
inv.write_json('metadata.json')
inv.write_yaml('metadata.yml')

inv.xml = None
inv.save()
```

To have more examples, look at the source code of the command line tools located in the *bin* subdirectory.

## Command line tools

Several sub-commands are provided with this lib:

- Dump embedded metadata: `invoicex dump file-with-xml.pdf metadata.(xml|json|yml)`
- Validate existing metadata: `invoicex validate file-with-xml.pdf`
- Add external metadata file: `invoicex add no-xml.pdf metadata.xml`
- Extract fields from PDF and embed: `invoicex extract no-xml.pdf`

All these commande line tools have a **-h** option that explains how to use them and shows all the available options.

## Licence

This library is published under the BSD licence (same licence as [PyPDF2](http://mstamy2.github.io/PyPDF2/) on which this lib depends).

## Contributors

- Alexis de Lattre <alexis.delattre@akretion.com>

## Changelog

* Version 0.6 dated 2018-05-01

  * Now fully PDF/A-3 compliant with additionnal attachments (tested with veraPDF)
  * facturx-pdfgen: don't overwrite by default and add --overwrite option
  * Add factur-x library version number in metadata creator entry

* Version 0.5 dated 2018-03-29

  * Fix XMP metadata structure
  * Now fully PDF/A-3 compliant when the input PDF file is PDF/A compliant (tested with veraPDF). This implied copying /OutputIntents and /ID datas from source PDF to Factur-X PDF.
  * Fix support for additionnal attachments: they can now all be saved with Acrobat Reader
  * Improve XML extraction from PDF Factur-x file

* Version 0.4 dated 2018-03-27

  * Factur-x specs say /AFRelationship must be /Data (and not /Alternative)
  * Update Factur-X XSD to v1.0 final
  * Add support for additionnal attachments
  * Add factur-x lib version in Creator metadata table
  * Add /PageMode = /UseAttachments, so that the attachments are displayed by default when opening Factur-X PDF invoice with Acrobat Reader
  * Improve and enrich PDF objects (ModDate, CheckSum, Size)
