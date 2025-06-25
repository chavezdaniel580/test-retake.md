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

