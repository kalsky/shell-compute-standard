# Compute Server Shell Standard

#### Version 1.0.0


## Introduction
The Compute Server Shell Standard defines a standard for all compute server shells that integrate with CloudShell. The standard defines the Shell’s data model, commands and a set of guidelines that should be followed in compute server shell development and customization.




## Revision History

Version | Date | Notes
--- | --- | ---
1.0.0 | 2016-07-14 | First release of the Compute server Shell Standard


## Definitions

### Versioning
The compute Shell version follows Semantic Versioning Guidelines (see details in http://semver.org). In short, the version structure is Major.Minor.Patch, for example “1.0.2”. A Path version is promoted when making backward-compatibility bug fixes, a Minor version is promoted when adding functionality in a backwards-compatible manner and a  Major version is promoted when making a backwards incompatible changes.


## Data Model
### Families & Models

** Compute Server Data Model **
- Compute Server
 - Port
 - Power Port


** Example **
- Family: Compute Server, Model: Dell Power-Edge Server
- Family: Port, Model: Resource Port
- Family: Power Port, Model: Generic Power Port


#### Family Rules

Family | Rules
--- | ---
Compute Server | Searchable
Port | Searchable, Connectable, Locked By Default
Power Port | Searchable, Connectable, Locked By Default



#### Resource Name and Address
Family | Model | Resource Name | Resource Address
--- | --- | --- | ---
Compute | [Vendor] [Series] Server | (user defined) | (user defined - IP)
Power Port | Generic Power Port | PP[ContainerID][ID] | PP[ContainerID][ID]

Note: The [ID] for each sub-resource is taken from the device itself (corresponds to the names defined in the device).


### Attributes
#### Guidelines
- Attributes which aren’t relevant to a devices won’t be populated by the driver.
- All attributes which aren't user-input are "read only"
- The attribute rules are as follows - all attributes which are user input should have the rule "Configuration" enabled, all attributes which aren't user input should have the rules "Settings" and "Available For Abstract Resources" enabled.
- It is possible to customize the attribute rules selection after importing the Shell to CloudShell.
- Attributes shouldn’t be removed.
- Custom attributes should be added only to the root level model.
- All attributes are of type String unless mentioned otherwise

##### [Vendor] [Series] Server

Attribute Name | Details | User input?
--- | --- | ---
Vendor | | No
Location | | No
Model | | No
OS Architecture | | No
OS Type | | No
OS Distribution | | No
OS Version | | No
Storage Capacity | | No
SNMP Read Community | | Yes
SNMP Write Community | | Yes
SNMP V3 User | | Yes
SNMP V3 Password | | Yes
SNMP V3 Private Key | | Yes
SNMP Version | Possible values – v1, v2c, v3 | Yes
Console Server IP Address | | Yes
Console User | | Yes
Console Port | Attributes of type Numeric | Yes
Console Password | Attribute of type Password | Yes
CLI Connection Type | Attribute of type Lookup. Possible values – Auto, Console, SSH, Telnet, TCP | Yes
Power Management | Attribute of type Boolean. Possible values – True, False | Yes


#####  Generic Power Port

Attribute Name | Details | User input?
--- | --- | ---
Model | | No
Serial Number || No
Version | | No
Port Description | | No



### commands
Below is a list of all the commands that will be part of the standard Shell, their names and interfaces. Each  Shell that will be released by Quali’s engineering will include implementation for all those commands.

When creating a new shell according to the standard it is OK not to implement all commands and/or implement additional command, but a command with a functionality that fits one of the predefined list commands should be implemented according to the standard.

Command outputs: On failure an exception containing the error will be thrown and the command will be shown as failed. A failure is defined as any scenario in which the command didn’t complete its expected behavior, regardless if the issue originates from the command’s input, device or the command infrastructure itself. On success the command will just return as passed with no output. The “Autoload” command has a special output on success that CloudShell reads when building the resource hierarchy. The “Save” command will return an output on success with the file name (exact syntax below).



- ** Autoload ** – queries the devices and loads the structure and attribute values into CloudShell.
  - SNMP Based



  - ** Save ** – creates a configuration file.
    - Inputs
        - Configuration Type – optional, if empty the default value will be taken. Possible values – StartUp or Running Default value – Running
        - Folder Path – the path in which the configuration file will be saved. Won’t include the name of the file but only the folder. This input is optional and in case this input is empty the value will be taken from the “Backup Location” attribute on the root resource. The path should include the protocol type (for example “tftp://asdf”)

   - Output: "<FullFileName>,"
   - The configuration file name should be “[ResourceName]-[ConfigurationType]-[DDMMYY]-[HHMMSS]”


   - ** Restore ** – restores a configuration file.
     - Inputs
         - Path – the path to the configuration file, including the configuration file name. The path should include the protocol type (for example “tftp://asdf”). This input is mandatory.
         - Restore Method – optional, if empty the default value will be taken. Possible values – Append or Override Default value – Override
         - Configuration Type - mandatory, no default. Possible values - StartUp or Running



  - ** Shutdown ** – sends a graceful shutdown to the device
      - Inputs:
        - Note that not all devices support a shutdown command. In such cases the command just wouldn’t be implemented
