# struct-doc
Documentation for different structures used in well known file formats

## Objectives

There is two main objectives of this repo.
  * Finding an ideal format to document structures in files.
    This tries to avoid being linked to any specific programming language.
    The idea is to just need a text editor to edit a template, or a terminal to just read it without UI specific requirements.
    Also, it is intended to follow a specific syntax to allow the development of tools that can parse these files and take profit on them.
  * Having a centralised place to check the structures of those files as a documentation.

## Quick explanation about the syntax in templates
  * Any template contains the definition of just one structure.
  * Any template is subdivided in 3 sections: type declarations, field definitions and constraints.
    The 3 sections are always in this order, and only the field definition one is required.
  * The hash symbol '#' is reserved for comments. Any character after a hash will be ignored by tools until the end of that line.

### Type declarations section
This section is intended to declare types that will be used in the field definitions section.
These declarations can be used for different purposes, such as:
  * Create alias that may be shorter or more suitable that the generic types.
  * The use of generics types

This section is optional, as the fields can be declared directly with generic types.

Regarding its syntax:
  * Any line in this section must start with the 'greater than' symbol '>'.
  * The name of the new type must follow the '>' character.
  * The new type name should start with a lower case character [a-z], and following characters can be lower or upper case alphabetic characters, cyphers, or an underscore '_' [A-Za-z0-9_]*

### Field definitions section
This section is required and it is expected to have at least to field definitions, because there is no point of having an empty structure, or just one.

Regarding its syntax:
  * Any entry in this section declares a field in the structure.
  * The expected tokens in each line are the field type, the field name and optionally a constraint respectively.
  * All tokens can be separated with one or more spaces or tabs.
  * Name and types tokens should start with a lower case character [a-z], and following characters can be lower or upper case alphabetic characters, cyphers, or an underscore '_' [A-Za-z0-9_]*
  * The token '_' can be used instead of a name to indicate that, even if the field is present in the file structure and takes some space, its value is not relevant.
    This can be used for reserved space or padding added to ensure some alignment.

### Constraints section
Every template can have multiple constraints, or none of them.
Sometimes the constraint is that straight-forward that it is better to include it after the name when a field is declared, like for example magic numbers like 'P' and 'K' at the beginning of a ZIP file, where we always expect that value.

When a field can have multiple values and we want to provide information for each of the values, we can use this constraint section.

To declare a constraint, we start a line with the colon symbol ':', followed by the name of an already declared field.

Optionally, a constraint type can follow the field name to specify how the next lines should be interpreted. In case there is no type explicitly written, it defaults to 'enum'.

The following lines after the constraint declaration will specify potential expected values for that field. All the lines will be related to the field until the end of the file, or until another line starting with ':' is found.

#### 'enum' constraint type
This is the default constraint type. The absence of any explicitly declared constraint type in the constraint declaration line will default to this.

When this type is applied, it is understood that the field contains a set of values and each value may indicate a state.

This is not expected to be a field where arithmetic or bit logic operations can be applied to its value.

Then, it is expected to find a value, or range of value at the start of each line, usually followed by a comment to explain what that number means.

Note that numbers should not be repeated between lines, and ranges should not overlap other values and ranges.

#### 'flags' constraint type
When this type is applied, it is understood that the field is a set of flags, which means that each bit could be interpreted as a boolean.

Every line should start with a value representing a bit mask. The hex representation can be used here to make it more readable.

#### 'pack' constraint type
When this type is applied, it is understood that the field value can be subdivided in other values formed by contiguous bits.
For example, a byte, that has 8 bits, could be subdivided in 2 values from 0 to 15 both included.

In this case, every line is expected to start with a range of bits within this value, and followed by an alias for that new value. The alias can be used in the declaration of a new constraint to specify deeper its potential values.
