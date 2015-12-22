# Read Fallout 3 data files

**Please note:** This is a work in progress.
It is only partially implemented and barely tested.
Come back later.

This project parses the data files (esm, esp) used by Fallout 3.
There are scripts for creating a text file or populating a Redis database.

The various scripts also aim to document the file structure in a readable way.


## Getting started

Install all the dependencies for the scripts:

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

The only real requirement is an `emit` function that accepts a path and a value.
The parser will call the `emit` function as it extracts values from the file.


## Notes on file structure

Values are stored in [Little Endian](https://en.wikipedia.org/wiki/Endianness) format.
This controls the Node Buffer methods to use.

The files are broken up into groups, with records that contain fields.
The fields can contain different types of values: text, numbers, lists or flags.
