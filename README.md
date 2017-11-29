# OPC UA Server XML Generator
This is a dumb (meaning you have to implement the generation logic) XML generator to generate XML configuration files for OPC UA server.

## What does this generator achieve?
I found myself needing to export data to OPC UA server but not necessarily use the server more than feeding it a XML config of how the server should look and then running the server's parser..1...2...3...done. I needed a way to turn objects I could handle in C into XML nodes in such a file. This generator is for that purpose.

## Prerequisites
+ libxml2
+ CMake
+ build essentials

## How to build
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
+ ~~ReferenceType objects~~
+ ~~VariableType objects~~
+ ~~Check these two above exsists~~
+ Removing reference from objects
+ ~~Reference search~~
+ ~~Destoryer functions~~

## Objects

There are a number of different data types that each represent a XML node or a portion of the XML node's information/ characteristics. The document is generated within the `<UANodeSet`> root tag. Each main object has general attirbutes as well as type specific attributes, xmlNode pointers that point to the objects XML node, the object's references node and the object's display name node, function pointer to accessor functions as well as a pointer to the head of areferences  linked list. The references linked list is used to push reference objects onto the object. Once the linked list has been populated the ```create_references``` function can be called to turn the linked list into nested XML nodes.

### General objects

The following objects are ones that are nexted within the main object types, creating more manageable and modular datatypes.

#### ID information

ID information is attached to many different types of nodes within the OPC UA XML configs, it is a tripplet of attributes: ns,i and s.

#### `<Reference`>

Each of the main data types can contain `<Reference`> nodes, potentially an infinite amount. The references objects are attached to the main objects via a linked list and contain the information required for generating such a `<Reference`> node.

References contain a reference type, currently stored as a string literal but this will most likley be expanded in future revisions of this tool. The reference has an ID object attached to it storing its ns,i and s attributes. A reference also has a boolean "IsForward" value that can be set to true, false or -1 (undefined).

#### Node attributes

Node attributes are attributes that are connected with each of the main objects: `<UAObjectType`>, `<UAVariable`>, `<UAMethod`>, `<UAVariableType`>, `<UAReferenceType`>, `<UADataType`>, `<UAView`> and `<UAObject`>. The attributes describe the obkect's parent node ID, node ID, browse and display name as well as functions pointer that point to functions to set and modify the attribute object's values.

#### Object specific attributes

Each main object type also has attributes that are unique to its type, each object contains another datatype that clusters that object's type specific attributes.

## How the generator works

In the OPC UA XML server config, all objects (being `<UAObjectType`>, `<UAObject`>, `<UAMethod`> and `<UAVariable`>) are children of the root XML node. As such there is no need to create parent-child relationships within the XML generator, making the XML generator "dumb" in that it does not control and sort of object relationships. it just prints what it is fed into the root node of the XML document. All objects need to be created by calling creation functions, eg.
```c
opcua_variable_t* tmp_variable = datalog_opcua_create_variable();
```
Once the object has been created, the object functions can be used to set various properties of the object, eg.
```c
tmp_variable->set_parent_id_i(tmp_variable, 1001);
tmp_variable->set_parent_id_ns(tmp_variable, 1);
tmp_variable->set_node_id_i(tmp_variable, 6001);
wtmp_variable->set_node_id_ns(tmp_variable, 1);
```
Each object contains `<Reference`> nodes within its `<References`> tag. To add references to the object, one must first create a reference object using
```c
opcua_reference_t* tmp_ref = datalog_opcua_create_reference();
```
Again using object functions, properties of the reference can be set. Once the reference is set up in the desired fashion it can be added to an object using the following object functions
```c
tmp_variable->add_reference(tmp_variable, tmp_ref);
```
This will add the reference object to the end of the variable object's reference list. After all the required references have been added to an object and all the properties have been set, the objects can be turned into an XML representation by calling the function
```c
tmp_variable->create_references(tmp_variable);
```
This will create an XML representation of the variable.
