# OPC UA Server XML Generator
This is a dump (meaning you have to implement the generation logic) XML generator to generate XML configuration files for OPC UA server.

## What does this generator achieve?
I found myself needing to export data to OPC UA server but not necessarily use the server more than feeding it a XML config of how the server should look and then running the server's parser..1...2...3...done. I needed a way to turn objects I could handle in C into XML nodes in such a file. This generator is for that purpose.

# Prerequisites
+ libxml2
+ CMake
+ build essentials

# Building
I will get around to making a demo CMake for a demo project. When such a dummy CMake exsists building can be done as follows.

### How to build
It's pretty tough....  
cd into the root director and create a build dir, or don't, it'll be your mess.  

```bash
 cd Datalog-API
 mkdir build
 cd build
```
 Generate CMake junk __or__ run ccmake to configure debugging options
```bash
 cmake ..
```
__or__
```bash
 ccmake ..
```
and finally make
```bash
 make
```
The executable can be found in the bin subdirectory in the root dir.  

Library objects in the build subdirectory.

## Documentation

To generate the documentation run the command

```bash
doxygen
```
from the doc folder. Then navigate to ./html/index.html

## Work in progress

I wrote this quickly and dirtily, so excuse the mess.  

### To-Do  
+ ReferenceType objects
+ VariableType objects
+ Check these two above exsists
+ Removing reference from objects
+ Reference search
+ Find out what ns, i and s stand for :|
+ Destoryer functions

# Objects

There are a number of different data types that each represent a XML node or a portion of the XML node's information/ characteristics. The document is generated within the `<UANodeSet`> root tag. Each main object has general attirbutes as well as type specific attributes, xmlNode pointers that point to the objects XML node, the object's references node and the object's display name node, function pointer to accessor functions as well as a pointer to the head of areferences  linked list. The references linked list is used to push reference objects onto the object. Once the linked list has been populated the ```create_references``` function can be called to turn the linked list into nested XML nodes.

## General objects

The following objects are ones that are nexted within the main object types, creating more manageable and modular datatypes.

### ID information

ID information is attached to many different types of nodes within the OPC UA XML configs, it is a tripplet of attributes: ns,i and s.

### `<Reference`>

Each of the main data types can contain `<Reference`> nodes, potentially an infinite amount. The references objects are attached to the main objects via a linked list and contain the information required for generating such a `<Reference`> node.

References contain a reference type, currently stored as a string literal but this will most likley be expanded in future revisions of this tool. The reference has an ID object attached to it storing its ns,i and s attributes. A reference also has a boolean "IsForward" value that can be set to true or false.

### Node attributes

Node attributes are attributes that are connected with each of the main objects: `<UAObjectType`>, `<UAVariable`>, `<UAMethod`> and `<UAObject`>. The attributes describe the obkect's parent node ID, node ID, browse and display name as well as functions pointer that point to functions to set and modify the attribute object's values.

### Object specific attributes

Each main object type also has attributes that are unique to its type, each object contains another datatype that clusters that object's type specific attributes.
