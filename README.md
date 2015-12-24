# Read Fallout 3 data files

**Please note:** This is a work in progress.
It is only partially implemented and barely tested.
Come back later.

This project parses the data files (esm, esp) used by Fallout 3.
There are scripts for creating a text file or populating a Redis database.

The various scripts also aim to document the file structure in a readable way.


## Getting started

This has not been pushed to NPM yet because it does not work as a module.

To parse data, you should clone this repo and then execute the following commands from within the folder.

Install the dependencies for the scripts:

```
npm install
```


## Parsing the files

### To a file

```
coffee output_to_file.coffee.md BrokenSteel.esm
```


### To Redis

```
coffee output_to_redis.coffee.md BrokenSteel.esm
```


### To something else

You should read the contents of those `output_to_*` scripts to see how they work and then you could write your own.

The only real requirement is an `emit` function that accepts an identifier and a value.
The parser will call the `emit` function as it extracts values from the file.

This may be changed later to push data to a stream instead.
I am not sure what the best way to expose the data is.


## File structure

Values are stored in [little endian](https://en.wikipedia.org/wiki/Endianness) format.
This controls the Node Buffer methods to use.

Files always start with a TES4 group.
The start of this group is parsed in the `start_tes4_file()` function in [process_stream.coffee.md](./process_stream.coffee.md).

The records in the TES4 group are parsed by [tes4.coffee.md](./tes4.coffee.md).

The rest of the file is divided into groups, described next.


### Groups

[grup.coffee.md](./grup.coffee.md) starts the parsing of each group.
You should read this file to learn the byte structure.

The data section of a group is made up of records, described next.


### Records

[grup_record.coffee.md](./grup_record.coffee.md) starts the parsing of each record.

The data section of a record is made up of fields, described next.


### Fields

[get_field.coffee.md](./get_field.coffee.md) parses field data from a Buffer.

A field contains a 4 byte label and then 2 bytes indicating the size of the field data.
The remaining bytes store the value of the field.

Fields can contain different types of values: text, numbers, bit flags, or lists of those.
The type of the value are not described by the field, but rather determined by the labels of the group+record+field.
