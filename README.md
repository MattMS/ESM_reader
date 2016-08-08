# Read Fallout 3 ESM files

**Please note:** This is a work in progress.
It is only partially implemented and barely tested.
Come back later.

This package parses the `*.esm` data files used by Fallout 3 and exposes the data as Objects via a Node.js Stream.

Another aim of this project is to provide documentation of the file format.


## Usage

Add this as a dependency to your project:

	npm install esm_reader --save


## File structure and parsers

Values are stored in [little endian](https://en.wikipedia.org/wiki/Endianness) format.
This controls the Node Buffer methods to use.

Files always start with a TES4 record.

The rest of the file is divided into groups, described next.

[read_file_data.coffee.md](./read_file_data.coffee.md) switches to the appropriate parser based on the current state.


### Groups

[read_group_header.coffee.md](./read_group_header.coffee.md) parses the start of each group.

The rest of the group is made up of records, described next.


### Records

[read_record_header.coffee.md](./read_record_header.coffee.md) parses the start of each record.

The rest of the record is made up of fields, described next.


### Fields

[read_field.coffee.md](./read_field.coffee.md) parses field data from a Buffer.

A field contains a 4 byte label and then 2 bytes indicating the size of the field data.
The remaining bytes store the value of the field.

Fields can contain different types of values: text, numbers, bit flags, or lists of those.
The type of the value are not described by the field, but rather determined by the labels of the group+record+field.
