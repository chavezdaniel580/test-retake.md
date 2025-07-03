Forensic Image File and Compression Formats
A forensic image is a copy of media, like a disk or Solid-State Drive (SSD), that can be entered legally into evidence in a court of law. Forensic images are needed to verify data integrity, because the data can be compared and verified to not have been tampered with after the image was taken. A forensic image includes all Operating System (OS) data and written bits, sectors, partitions, files, folders, master boot records, bits marked for deletion, and unallocated space. A forensic image can be manipulated and read without interacting with the original drive and the drive’s contents.

﻿

Collecting forensic images of disk sources can be accomplished in many ways and with different image formats. The image file type created depends on the tools used and the specifics of the source. An image file can be a single file that contains the entire disk source or a series of multiple files that collectively represent the source. Common image file types are as follows:

Raw byte-for-byte.
Single raw file (dd): A Linux command-line file system tool.
Split raw files (affuse): Filesystem in Userspace (FUSE)–format files.
AFF (Advanced Forensic Format).
AFD (AFF multiple file).
AFM (AFF with external metadata).
EWF (Expert Witness format [EnCase]).
Split EWF (split E01 files): A file type used by forensic imaging tools, commonly used for legal evidence-based forensic images.
VM disk image.
VMDK.
VHD/VHDX.

Toolkits provide different types of image files, depending on the need. This lesson covers basic dd-based images, FTK® Imager images, and images opened for analysis with Autopsy.


Forensic Image Compression
Forensic image compression allows organizations to save valuable disk space for both archival requirements and data transfer needs. If all images were stored in their raw format, most organizations would run out of disk space for archived evidence or be unable to transfer larger disk images across interdepartmental networks. 

﻿

The E01 (EnCase) image file format provides a backup of acquired digital evidence that includes many different data points surrounding an image, including timestamps, contents, and other distinguishing characteristics. The E01 format is regularly used because it saves space for analysts and agents who need to keep a large number of files. The format also supports many types of metadata for ease of identification. E01 documents the disk header, logical volume health information, sector health, and other metrics related to the imaging process to verify the integrity of the compression.

﻿

Many standard file compression formats besides E01 exist, but not all are verifiably lossless or approved for evidence collection by many organizations. Third-party tools, such as LZ4 in Linux, are fast and should not compromise integrity of the image, but the image files must be rehashed each time they are decompressed to verify that all the contents of the image have been retained between compressions.

![image](https://github.com/user-attachments/assets/f9e1f076-c3c8-4f60-8ac0-7a94f6b1b142)

![image](https://github.com/user-attachments/assets/5f571a6b-48cd-4040-bc0c-d510ef01b408)

Creating Forensic Images for Analysis
Forensic Image Creation Techniques and Tools 
﻿

Various tools are available for creating forensic images on Windows such as FTK Imager, and most security professionals using Linux use native OS tools like the dd command. The specific tool used matters only for compatibility reasons, not legal or procedural ones. As long as a completed image can be separately hashed and verified after an image is collected, the image can be used in a forensic context. The tools used in this lesson represent examples of the many imaging solutions available.

﻿

Windows Imaging Applications
﻿

A wide variety of disk-imaging applications exists for the Windows OS. At the height of the Windows NT era, Norton Ghost was regularly used as an imaging tool but is not used often today. Lighter-weight toolkits like the FTK imager toolkit are often used because of their fully featured suites of options related to the forensic parts of the imaging process. Windows-based tools can often integrate case files, associate hashes with images, and automatically verify file integrity for decompressed images. Many investigators use these Windows-based tools because of their consistency and ease of use.

﻿

Linux dd Command
﻿

In all distributions of Linux, the dd command allows for the creation of an image of a partition or the complete clone of a disk. Because the dd command makes full copies of the disk or partition, it requires an equal amount of, or more, storage space on the target disk for the copy. This tool is especially useful for creating images of drives that have been connected externally to the Linux machine being used if a software write blocker is installed or the disk is set to read only. A potentially corrupted or infected Windows image can be copied without opening or executing the files on the disk, all from the safety of a Linux host.

Maintaining Data Integrity
Methods of maintaining integrity of data on a forensic image include use of write-protecting hardware, hashing, developing a chain of custody, and documenting the investigation.

﻿

Write-Protecting Hardware
﻿

Once a device is collected for forensic imaging, the investigator should use a hardware write blocker connected to the acquired device. This ensures that the collected device cannot be written to, even if the physical switch has been damaged or compromised. Write blockers can also be updated and regularly checked to ensure functionality between uses.

﻿

Hashing
﻿

When a drive has been imaged, a hash should be created to verify the integrity of the data. If a single bit of data changes between image files, a properly hashed forensic image does not match what was initially collected, indicating evidence tampering. Common hashing standards for forensic images are the same as for individual files (MD5 of SHA1). The hash represents a “fingerprint” formed by creating a numerical value based on the bit values in the image that are then run through a hashing table.

﻿

Chain of Custody
﻿

Where feasible, it is best to implement a chain of custody during an investigation. A chain of custody is an established list of individuals who own certain parts of the forensic process. A chain of custody is standard practice for many forms of evidence, not just forensic images, and this practice creates a burden of responsibility for individuals handling the evidence in question. Sometimes an initial investigation does not require the rigor of full chain-of-custody evidence collection precautions, but understanding when to form a chain of custody can help investigators ensure the integrity of what they have collected at different points in the investigative chain. Each organization has its own operating procedures that should be followed when a chain of custody is required and should be referenced as appropriate.

﻿

Investigation Documentation
﻿

Proper documentation throughout the investigative process is key to building trust in the evidence collected. Investigators should follow their organization’s procedures for proper documentation. As forensic evidence is collected, notes should be taken for every item, relevant persons should be noted for reference, reports should be made about the process taken for the investigation, and proper organization of these notes for potential submission should be developed.

2. Open FTK Imager on the desktop.


3. Select File > Create Disk Image.

   ![image](https://github.com/user-attachments/assets/a2c381b9-530c-406c-b889-e342fabdec62)



5. Select Contents of a Folder from the bulleted list, and select Next. Select yes when prompted.

   ![image](https://github.com/user-attachments/assets/900eefa6-30eb-49af-8b8a-e3d229543c55)



7. Select the Browse button, expand the trainee folder, select Documents, and select OK.

   


9. Select Finish.


10. Select the Add button to add a destination for the image file.


11. Enter the following as a sample for the evidence information for this image:
Case Number: 01
Evidence Number: 02
Unique Description: Test
Examiner: Trainee
Notes: N/A

12. Select Next.


13. Select Browse, and set the image destination as the desktop.


14. Select OK.


15. Enter Documents Image in the Image Filename field.


16. Select Finish.


17. Select Start.


The image is created, and the hash of the image is displayed. 


15. Close FTK Imager. 


The image was created on the desktop as a .e01 file, with a text file with the relevant evidence information for reference.


In a real-world scenario, these steps would likely be performed to collect an image of the entire disk from a physically collected drive.

![image](https://github.com/user-attachments/assets/86de4153-a64c-4313-b613-884d68c73739)


2. Open Autopsy on the desktop.


3. Select New Case.


4. Add the case name Sample, and browse to the Documents folder to set the base directory.

   ![image](https://github.com/user-attachments/assets/de964311-e0b4-4861-a7e4-ec0a01934912)



6. Select Next.


7. Enter the case number 01, and select Finish.


A sample case is created that can be used to gather forensic evidence with Autopsy. An image file can be examined for a number of different uses, and organizations often have standard practices regarding forensic image analysis.


Autopsy requires a data source to be added, and its integrity must be verified, based on the other data provided.


7. Select Specify New Host Name, enter Case Host, and select Next.

   ![image](https://github.com/user-attachments/assets/1af932f6-2592-435c-8a99-f0e5d3cdf809)



9. Select Disk Image or VM File, and select Next.

    ![image](https://github.com/user-attachments/assets/9cc32ee7-f2ff-488f-941e-8cc92d737d62)



11. Select the Browse button, and select the .e01 image on the desktop.


12. Select Next on the Configure Ingest screen. 


13. Select Finish on the Add Data Source screen.

![image](https://github.com/user-attachments/assets/99a8f589-2d4f-4545-b7a8-a2ca46c0c424)



15. Once the case completes, expand the Data Sources option, expand the Case Host option, and select TestCase.E01 to view the image properties.


16. Select the Summary tab, and select the Container tab.


17. Visually compare the MD5 hash provided with the MD5.txt file on the desktop.

![image](https://github.com/user-attachments/assets/543b09dc-d52a-4505-9c58-e04d33b13fa5)

If the two hash values match, integrity of the image has been maintained. A difference between the two hash values indicates tampering or corruption of data. Should such a difference occur, work back through the case change log to find the point at which the hash change occurred.



1. Log in to the VM lin-hunt-cent using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open a new terminal session, and run the following command to obtain a hash of the target partition to compare with copies. If prompted, use the password CyberTraining1!.
$ sudo sha512sum /dev/sda1

![image](https://github.com/user-attachments/assets/8751d65c-7d06-4ff1-9723-4637ddfbeaa0)


3. Collect a copy of the image by running the dd command:
sudo dd if=/dev/sda1 of=/opt/sda1dd bs=64k conv=noerror,sync 



In the above command, if represents the input file and of represents the output file. This command creates an image named sda1Image in the /opt/ directory.


4. Set the file to read only by running the following command:
sudo chmod 0444 /opt/sda1dd



5. Run the following command to hash the image file to ensure that the contents of the copied image are the same and verify that no errors occurred during the copying process:
sha512sum /opt/sda1dd



The hash produced should match the original hash.


6. If the hashes are different, compare the original hash by running the following command:
echo "replaceWithHashFromStep2 /opt/sda1dd" | sha512sum -c

![image](https://github.com/user-attachments/assets/4962ba1f-0610-4b70-8c9d-3119b2d8e451)


If the comparison is successful, the following is displayed:
/opt/sda1dd: OK

![image](https://github.com/user-attachments/assets/a38cccdc-2db4-4f96-9e0c-d6821d5644a0)


If the comparison fails, the following is displayed:
/opt/sha512dd: FAILED
sha512sum: WARNING: 1 computed checksum did NOT match

![image](https://github.com/user-attachments/assets/964587ff-d4ce-45f6-a303-747aeb6629ba)

Creating a Forensic Image from a Remote Source
When the disk to be imaged or the system housing the disk is not physically accessible, remote imaging methods must be used. In some cases, specifically with smaller data sizes, service agents can be used on the remote system to capture the image. The agent may be bootable media that allows collection of disk images while the OS is stopped or an active agent process running on the OS. Collection and electronic transfer of large disks may require many hours or even days. Data collection may be limited to partial acquisition instead of the entire disk or partition, such as collecting specific files or folders. 

﻿

In most cases, specialized forensic imaging kits are sent to remote locations for local administrators to use in the imaging process. These kits allow local forensic image collection, after which the image is shipped back to the investigating team for analysis. Acquiring evidence that needs to be shipped or transported also often requires an established chain of custody for added security. This practice is in place because imaging over a network connection can fail and leave incomplete or corrupted images when network conditions are unstable.

![image](https://github.com/user-attachments/assets/4e56f39b-d9ce-40f4-848d-69ca75da9be0)


Disk Image Parsing Tools and Techniques
Forensic images may be captured in several different formats using tools designed for bit-by-bit copy. These tools create a portrayal of a physical hard disk state that is as accurate as possible for further investigation. Once the images are captured, parsing the file for valuable information is a matter of choosing the right analysis method with the chosen tool. 

﻿

Forensic Image Formats
﻿

The most common forensic image file formats are described below, though this does not encompass all file formats. Image parsing tools can typically handle standard file formats, including these.

﻿

RAW
﻿

The RAW file format is created by the Linux disk imager utility dd. The dd format provides simple bit-for-bit representations of the data imaged by the utility, without additional hash data or compression. 

﻿

E01
﻿

The E01 file format is created by the EnCase tool. When broken into multiple segments, the image metadata includes a hash for each segment and metadata entered by an examiner regarding when the image was captured. Each segment is stored as a separate file with a 640-megabyte (MB) maximum size. The header of an E01 image file provides the case information that a user inputs at the time of collection; this includes the name of the investigator, case name or number, media from which data is transcribed, the date and time of collection, and the software and Operating System (OS) versions of the collection device. E01 is the most common format in the Expert Witness Disk Image Format (EWF) family.

﻿

SMART
﻿

SMART is a file format for Linux imaging software and a legacy format in the EWF family. Image files formatted with this standard use the .s01 extension.

﻿

Advanced Forensic Format (AFF)
﻿

The Advanced Forensic Format file type is a non-proprietary extensible file format. It is designed to store disk images of any size, with or without compression, in one or many files. The image includes file metadata at the time of capture. The format definition also includes provisions for internal self-consistency checking and for certifying evidence file authenticity. Both of these features are important for the integrity of data for law enforcement functions and to recover data, even if a portion of the drive has been corrupted.

Forensic Imaging Tools
dd
﻿

The dd command-line utility is employed by administrators to copy hard drives and device files into backup locations, sometimes with data-encoding conversions during the copying process. Because the file type of the output is “raw” data, the image format RAW is synonymous with the format dd.

﻿

EnCase
﻿

EnCase is an industry-standard tool for capturing forensic images of hard disks for evidence in law enforcement investigations or threat hunts as well as Incident Response (IR) actions. It stores captured images in its proprietary E01 format.

﻿

FTK® Imager
﻿

FTK Imager is a popular, free alternative to EnCase. It can store images in the E01 or SMART format.

Image Parsing Tools
The Sleuth Kit (TSK) and Autopsy
﻿

The Sleuth Kit is a collection of command-line forensic tools and C language libraries used for disk image analysis and file recovery. Autopsy is a browser-based User Interface (UI) front-end application for The Sleuth Kit that improves the efficiency and user-friendliness of the analytical effort involved in parsing hard drive and mobile disk images. It runs as a Hypertext Transfer Protocol (HTTP) service served to the localhost interface. Functions available through these tools include listing all files on a system, cataloging file types, recovering deleted files, and performing hash-based or keyword searches on a file system. This lesson uses Autopsy during a hands-on exercise.

﻿

Foremost
﻿

Foremost is a forensic program, available in distributions such as Kali or the SANS™ Institute’s SIFT™ Workstation, used to recover deleted files from a disk or image file through analysis of headers, footers, and internal data structures. The headers and footers can be specified by a configuration file or the use of command-line switches to specify built-in file types. These built-in types look at the data structures of a given file format, allowing for a more reliable and faster recovery.

Disk Image Analysis Techniques
Mounting an Image to a File System
﻿

To analyze a disk image, an analyst may either run the image through an analysis tool (such as Autopsy, for which techniques are described below) or mount the image with an application (such as OSFMount). The difference is that running an analysis represents a static analysis, which does not alter the image in any way, whereas mounting an image to a file system alters and corrupts the image unless it is mounted in read-only mode. (However, for IR and hunts, mounting the image to a file system rather than in a Virtual Machine [VM] may allow for advanced dynamic malware analysis when the mounting occurs in a sandbox environment.)

﻿

Keyword Searches
﻿

Keyword searches of a file system image may be performed using American Standard Code for Information Interchange (ASCII) strings and grep regular expressions. Searches may be performed on the full file system image or only the unallocated space. An index file may be created for faster searches. Strings that are frequently searched for can be easily configured in Autopsy for automated searching.

﻿

Temporal Analysis
﻿

A timeline of file activity can help identify areas of a file system that may contain evidence. Autopsy can create timelines that contain entries for the Modification, Access, and Change (MAC) times of both allocated and unallocated files.

﻿

Deleted Files and Unallocated Space Searches
﻿

Portions of the file system marked as unallocated space may be examined to recover the contents of deleted files if those disk sectors are not already overwritten for new files. 

﻿

File Listings
﻿

The contents of files may be viewed in RAW or hex format, or the ASCII strings can be extracted. When data is interpreted, Autopsy sanitizes it to prevent damage to the local analysis system. Autopsy does not use any client-side scripting languages. The files are sorted based on their internal signatures to identify files of a known type. Autopsy can also extract only graphic images (including thumbnails). The extension of the file is also compared to the file type to identify files whose extensions have been changed to hide them.

﻿

Metadata Analysis
﻿

Metadata structures contain the details about files and directories. Autopsy allows details of any metadata structure in the file system to be viewed. This is useful for recovering deleted content. Autopsy searches the directories to identify the full path of the file that has allocated the structure.

﻿

Data Unit Analysis
﻿

Data units are where the file content is stored. Autopsy allows the contents of any data unit to be viewed in a variety of formats, including ASCII, hexdump, and strings. The file type is also provided, and Autopsy searches the metadata structures to identify which has allocated the data unit.

﻿

Image Details
﻿

File system details can be viewed, including on-disk layout and times of activity. This mode provides information that is useful during data recovery.

![image](https://github.com/user-attachments/assets/7e865575-f915-499a-98f1-7262ea299da9)


Parse a Forensic Disk Image
In the following lab, read the scenario below, and complete the workflows to parse a forensic disk image. The lab comprises two workflows:

Opening a new case in Autopsy.
Obtaining a file listing through forensic image parsing.
Scenario
﻿

The mission partner has determined that an insider threat information smuggling operation is occurring in their network. They have passed along a forensic image of a system suspected to have been used for the activity. Access the CPT SIFT Workstation, and analyze the image with Autopsy to run basic analysis of the imaged system.

﻿

Open a New Case
﻿

Complete the steps in the following workflow to open a new case in Autopsy.

﻿

Workflow
﻿

1. Open the sift analyst workstation. The login credentials are as follows:

Username: trainee
Password: CyberTraining1!
﻿

2. Open a terminal window.

﻿

3. Launch the Autopsy service with the following command:

trainee@siftworkstation:~$ sudo autopsy
[sudo] password for trainee: CyberTraining1!
﻿

4. Open the Firefox browser, and select the Autopsy bookmark or navigate to the address http://localhost:9999/autopsy.

﻿

5. Select New Case.

﻿

6. Enter the metadata relevant to the case:


Case Number: This field should contain a unique identifier (ID) assigned by a supervisory authority that manages all assigned cases. For this exercise, use the case name CDAH01.
Description: This field is a 32-character field for which a short phrase providing an investigation title is appropriate. (The field is not meant to include copious descriptions of the case.) For this exercise, use the current date and the title Insider Threat.
Investigator Names: In this field, provide the first and last names of the analysts collaborating on the investigation. Figure 16.2-1 provides the example names John Doe and Jane Doe.

![image](https://github.com/user-attachments/assets/5eccf816-a3b4-4a07-9db8-adf65cc9b25c)

7. Select New Case when complete.


8. After receiving confirmation that the case is created, select Add Host to add information about a system that is being investigated:

![image](https://github.com/user-attachments/assets/c4935037-7932-44e3-af0d-c791c7b375ab)

9. Add the following metadata for the host being investigated:

Host Name: host_jean_o1
Description: Jean’s Computer
Time zone: <leave blank>
Timeskew Adjustment: 0
Path of Alert Hash Database: <leave blank>
Path of Ignore Hash Database: <leave blank>

10. Select Add Host to move to the next view:

![image](https://github.com/user-attachments/assets/6591e859-40a8-401c-a66c-6429431d2a5a)

11. Select Add Image:

![image](https://github.com/user-attachments/assets/ab9eb8d5-928b-4c6e-a148-a0b4fd5ed6bd)

12. Add the disk image file to analyze. The path for the file is /home/trainee/Desktop/Cases/nps-2008-jean.e0*.
Select Disk as the image file type and Symlink as the method to import the image file into the Autopsy folders, and select Next:

![image](https://github.com/user-attachments/assets/df2fea86-770c-401f-9ec0-2072620a0ed8)



NOTE: The wildcard character (*) is placed at the end of the file path so that Autopsy can retrieve all the image files. This is because EnCase disk image captures number the file extensions of the image sequentially when the image is split into multiple files.

![image](https://github.com/user-attachments/assets/ae1ce344-c577-4077-9648-a08c74f00497)



13. Select Next, and select Add.



When the files are loaded, Autopsy automatically detects the file system type and mount point:

![image](https://github.com/user-attachments/assets/4b18d2b2-d891-40ab-b9b8-0045b15b3f49)

Workflow


1. Select the radio button for NTFS disk volume, and select Analyze. (The NTFS volume, rather than the RAW disk image, is selected, as the NTFS volume is already formatted by Autopsy, making its contents readable by the tool.)

![image](https://github.com/user-attachments/assets/7b62fb18-275e-4135-ae15-12ddb076d67c)

2. Select the File Analysis tab at the top of the UI.




3. Select Expand Directories in the left pane to view the directory listing for the system:

![image](https://github.com/user-attachments/assets/3c304f6c-1fc6-40d8-901d-5d338415bf7d)

![image](https://github.com/user-attachments/assets/779f150d-6336-4d1e-b8d8-add9cb6ed372)





4. Obtain a file listing for all files in the C: directory (which appears by default) by selecting Generate MD5 List of Files:

![image](https://github.com/user-attachments/assets/14e6c55a-97de-400a-8ffa-3f0a4c6a9283)



One benefit of using this tool to generate the file listing is that in the event that a file is identified as an Indicator of Compromise (IOC), the MD5 hash helps to quickly find identical files, regardless of the filename or location used by threat actors. 

![image](https://github.com/user-attachments/assets/b9a84d92-8de4-4bde-80e1-e10cbd878779)
5. Close the MD5 List tab and select the File Type tab.


6. In the left pane, select Sort Files by Type and then OK:


Because this process may require 10 to 30 minutes to complete, the file type analysis has been run in advance for this exercise, and the resulting documents are already prepared in the Cases folder on the desktop. The screenshots below are only for reference, proceed to Step 7.

![image](https://github.com/user-attachments/assets/a3ea3f7a-186a-4984-bcae-7525c53edebd)



In using this feature, an option exists to exclude some files from the survey and validate the extensions.


If this survey is initiated, the following progress screens indicate the results:
![image](https://github.com/user-attachments/assets/fa9e9c33-4317-4358-8b58-9fc2de0556b8)

7. Unpack the documents from the Cases folder to the Autopsy folder for this case and host with the following command:
sudo tar -zxvf /home/trainee/Desktop/Cases/filetypes.tar.gz -C /



8. Open a new Firefox browser tab, and enter the following address to view the Hypertext Markup Language (HTML) report of the file types in the system:
file:///var/lib/autopsy/CDAH01/host_jean_o1/output/sorter-vol2/index.html
![image](https://github.com/user-attachments/assets/66ff0e02-6bac-406b-a0e6-419fd6af9649)

Deleted files may be viewed using the File Analysis tab.


Using the above workflows, answer the following questions.

![image](https://github.com/user-attachments/assets/577c972f-977e-4e9d-bd73-cc8bad2eeb5f)

![image](https://github.com/user-attachments/assets/876e1407-0574-4f73-ad12-b8e0ffe6a7a8)

![image](https://github.com/user-attachments/assets/aa7afad1-653c-498f-afaa-aa56207e1003)

![image](https://github.com/user-attachments/assets/0b0d241f-48b9-44ad-919d-99bf093b4be2)

Reviewing Windows Event Logs
Accessing Event Logs
﻿

Windows event logs are stored on the same partition as the Windows installation drive. By default, Windows is usually installed on the C:\ drive and the full path to the event logs is C:\Windows\System32\winevt\Logs. Figure 16.3-1 is a view of the directory where the event logs are stored.

![image](https://github.com/user-attachments/assets/aa18d0a7-0f5a-4d8b-b99c-7b211c886386)


Extracting Logs
Windows event logs stored within a forensic image are still in the same directory as listed above, but it takes a few more steps to access them. A forensic image is mounted either through OSFMount or within the Forensic Toolkit (FTK). Both of these tools are free and access the forensic image in a read-only state. FTK can also view the file system of the image without mounting the image, which is useful when looking for deleted files. TFTK provides access to locations that the Windows Operating System (OS) hides by default, which OSFMount does not. For example, the Master File Table (MFT) file $MFT is not visible during a mount, but FTK shows the file. Also, OSFMount does not show files that have been deleted like FTK.

﻿

NOTE: Trainees are not expected to perform the following Mount or FTK Methods. These are performed in the labs later in the lesson.

﻿

Mount Method
﻿

OSFMount requires the path to the forensic image to be able to access the files within it.

﻿

1. Select Mount new, browse to the forensic image, and select Next.

﻿![image](https://github.com/user-attachments/assets/cd08360f-2d4a-4c63-8215-388feaebf6b9)

 

2. Select the Windows partition. The Windows partition is typically the largest. In this case, it is Partition #1 and is 58.91GB in size.
![image](https://github.com/user-attachments/assets/97201a56-e4ba-46ac-a348-a255bc2f2f06)

3. Do not make any changes and select Next.


NOTE: OSFMount skips Step 3; the steps go from Step 2 straight to Step 4.

![image](https://github.com/user-attachments/assets/95347602-a365-453d-b296-958dc61654f8)



4. Select Mount. OSFMount shows the drive letter assigned to the forensic image. Afterward, browse the log directory F:\Windows\System32\winevt\Logs with Windows Explorer to access the event logs. Copy the files of interest out of the image and stored on the local disk.

![image](https://github.com/user-attachments/assets/bbc3036f-7583-4231-b2c5-351f3a958b74)

FTK Method


1. Open AccessData FTK Imager. User Access Control (UAC) appears as this application is being run with administrative privileges. Select Yes.


2. Select Add Evidence > Image File > Next.
![image](https://github.com/user-attachments/assets/e9a913bb-e02c-421f-91bb-07b1682a6776)

3. Enter the source path to the forensic image.
![image](https://github.com/user-attachments/assets/463f167d-92ae-4a4a-8da1-907baae97c87)



4. Expand the forensic image by selecting the plus sign (+) next to the image name. Continue expanding until Windows\System32\winevt\Logs is displayed.
![image](https://github.com/user-attachments/assets/2acaa1a1-17b1-4495-b1e8-cce97e13bf22)

![image](https://github.com/user-attachments/assets/0fcbc83c-799a-413f-abc5-2f33e712c200)

5. Select the log files of interest, right-click and select Export Files, and save the files on the local disk.

6. ![image](https://github.com/user-attachments/assets/0e52da49-d288-456c-b5e2-0160131b168e)


Tools
The Windows event logs have been extracted from a system and need to be analyzed. Windows event logs are stored in a binary format and require specific tools for them to be read. Prior to Windows Vista, event logs had the file extension .evt. Every version of Windows from Vista to Windows 11 uses the .evtx format. Occasionally Windows Event Viewer requests converting older EVT-formatted event logs to EVTX when analyzing the older format on a current version of Windows. If necessary, the older EVT format can be converted to EVTX using the built-in wevtutil utility. Sample syntax is as follows:

wevtutil export-log <sourcelogfile>.evt <targetlogfile>.evtx /lf 
﻿

The following tools are the most commonly used in the industry and are publicly available for free:

Windows Event Viewer
F-Secure’s Chainsaw
Eric Zimmerman’s EvtxECmd
William Ballenthin’s Python modules evtxtract and python-evtx
﻿

Windows Event Viewer
﻿

The Windows Event Viewer is the default viewer that ships with all versions of Windows. It allows for filtering by event ID, searching for text within the event logs, sorting, and cleanly displaying all information with column headings. The drawback is that it only allows for one event log to be viewed at a time and does not have any additional logic to assist with finding suspicious activity.

﻿
![image](https://github.com/user-attachments/assets/aa052203-9940-4b50-a94e-7cb56161290d)

Chainsaw


Chainsaw was created by the Finnish cyber security company F-Secure. Chainsaw has built-in logic with mapping files written in YAML that can quickly extract items that match custom rules and display them to the analyst. Chainsaw saves cyber defenders hours by analyzing all available event logs at once and generates a report. Chainsaw also dumps all events pertaining to a specific event ID if that is preferred.


Syntax


Search all event logs for activity that matches rules:
chainsaw.exe hunt C:\windows\System32\winevt\Logs -r sigma_rules/ -m mapping_files/sigma-mapping.yml --lateral-all --ignore-errors



Dump all logon events:
chainsaw.exe search C:\windows\System32\winevt\Logs\ -e 4624 –output 4624_events.txt

![image](https://github.com/user-attachments/assets/fb128a93-dad8-47e8-8cc3-cab4451da070)

EvtxECmd


EVTX Explorer (EvtxECmd) was written by Eric Zimmerman at Kroll who also wrote Kroll Artifact Parser and Extractor (KAPE), which is a widely used forensic toolkit. EvtxECmd processes a large number of event logs at once. It has a list of indicators, similar to Chainsaw, that it looks for and writes the most significant events into a spreadsheet for an analyst to review.


Syntax
EvtxECmd.exe -f C:\Windows\System32\winevt\Logs\ --csv "C:\Users\Trainee\Desktop" --csvf MyOutputFile.csv

![image](https://github.com/user-attachments/assets/abc853d9-7d64-4793-a09f-e8dddb7b4614)

![image](https://github.com/user-attachments/assets/3ffea018-6890-405a-bf58-7090eea30cba)

Python Modules


Willi Ballenthin from Mandiant wrote two Python tools to extract Windows event logs. The module python-evtx is a module that can be imported into Python scripts to convert the binary format that event logs are stored in and output the logs into Extensible Markup Language (XML) or text. Ballenthin also wrote a tool called EvtXtract that carves event logs from a forensic image based on the header of event log files.


Syntax for Evtxtract
"C:\Program Files\Python310\Scripts\evtxtract.exe" E:\winserv.001 > E:\eventlogs\evtx.xml



The python-evtx module requires a script to be written that uses the module before parsing the logs. This is used if there is a need to parse a large number of logs and if only a select few fields need to be exported from a log. This module allows for customization but overall requires the most setup when compared to the other options available.

Event IDs
This lesson has covered how to extract event logs and access their contents, but to make sense of them, event IDs need to be understood to filter event logs for specific events. There are hundreds of events in each event log file. Microsoft attempts to categorize activity and place it into its own file to limit file sizes and make it easier to find events. Note that some events are in multiple event log files. For example, when a new process is created on the system, the Security event log records it as event ID 4688, whereas Sysmon records it as event ID 1. Table 16.3-1 is a short list of the most common event IDs and logs.

![image](https://github.com/user-attachments/assets/3f27c3c4-60e1-4a85-ac27-766507f26cd1)



Deleted Logs
Attackers commonly delete, clear, or overwrite event logs in an attempt to cover their tracks. Depending on the method used by the attacker to perform these actions, it may be possible to recover the deleted event logs. Within FTK, any non-securely deleted file is prefaced with a red X icon before its filename. Armed with this knowledge, it is possible to browse through a forensic image and to look for event logs in the C:\Windows\System32\winevt\Logs directory and the C:\$Recycle.Bin directory to look for deleted files. Once found, right-click on the file of interest, select Export File, and save the file to the local disk. The event log can then be opened by any of the tools previously mentioned.

﻿

The $Recycle.Bin directory contains files that were deleted but were not overwritten. To access this location in FTK, the path is [root]/$Recycle.Bin and the Security Identifier (SID) of the user account that deleted the files. In this case, it is the Administrator account shown here as:

S-1-5-21-3227957547-616921804-3487276839-500
﻿

Within the specific SID directory, there are many deleted event logs present. The names of the files have been changed, but by selecting one of the files and looking at the hex view below the file, it can be seen that the $R513JPG.evtx file is actually the Microsoft-Windows-WMI-Activity%40Operational.evtx event log.

![image](https://github.com/user-attachments/assets/337a5ec9-93b8-4204-8574-7e243cef4df8)

Accessing Windows Event Logs
Scenario
﻿

There is potentially Malicious Cyberspace Activity (MCA) on a Windows server within the network. Events in Elasticsearch correlate to the time of the activity. The server has been taken offline and the associated forensic image has been collected for analysis. The time of the suspicious activity begins on May 26, 2022 @ 16:08:00 and ends on May 26, 2022 @ 16:18:00.

﻿

6. Query for the hostname of the device being analyzed to filter the logs:
agent.name:dc01



7. Change the view filter to only see the Windows event logs. In the search box, enter the following:
event.module


8. Hover the mouse over the right corner of the Popular box and select Add field as a column. 

9. In the Search field names box, search for the following filters and add them as columns:
event.providerwinlog.channelwinlog.event_id

![image](https://github.com/user-attachments/assets/8aaaf397-ec97-4226-aa45-4c90fc8c7927)

NOTE: The winlog.event_id shows a warning icon, which is not a concern. The lab works properly regardless.


It is time to correlate what is available in Elastic to what is in the forensic image. 


10. Minimize Chrome and open AccessData FTK Imager. User Access Control (UAC) appears as this application is being run with administrative privileges. Select Yes.


11. Add the forensic image E:\winserv.001 into FTK Imager.


![image](https://github.com/user-attachments/assets/2844c037-249e-4ef9-bd76-cc030183ef39)

![image](https://github.com/user-attachments/assets/d80ad22d-71af-4d8e-8a17-3774f1b6e767)

12. Expand winserv.001 to Partition 2/Windows [NTFS]/[root]/Windows/System32/winevt/Logs.

![image](https://github.com/user-attachments/assets/87b1a72c-606d-4b0c-aac4-1329e1e7dac9)

![image](https://github.com/user-attachments/assets/c9f579a0-a864-4b1d-9275-8fd7c5390562)




13. Locate the following logs in the Logs directory as they were present in Elastic:
System
Security
Microsoft-Windows-Sysmon%4Operational.evtx
Microsoft-Windows-WMI-Activity%4Operational
Microsoft-Windows-TaskScheduler%4Operational
Microsoft-Windows-PowerShell%4Operational

Notice that those specific event logs are not present in the forensic image. This shows a potential Indicator of Compromise (IOC); the attacker attempted to cover their tracks. It also shows the importance of sending logs to Elastic before they are cleared locally.


Recovering Deleted Event Logs
This lab is focused on recovering the five event logs that were identified as deleted in the previous lab. It also covers analyzing those event logs to understand the activity that took place on the forensic image that is being investigated.

﻿

Workflow
﻿

1. Log in to the win-hunt VM using the following credentials: 

Username: trainee
Password: CyberTraining1!
﻿

2. Load the winserv.001 image located on the E:\ drive into FTK Imager.

﻿

The administrator account is the focus. His SID is S-1-5-21-3227957547-616921804-3487276839-500. All deleted event logs end up in that user account Recycle Bin.

﻿

3. Expand the directory tree down to winserv.001/Partition 2/Windows [NTFS]/[root]/$Recycle.Bin/S-1-5-21-3227957547-616921804-3487276839-500. 

﻿

4. Extract the six files listed below by holding CTRL and selecting each.

$RJWWJQK.evtx 131,076 (Security)

$R513JPG.evtx 1,028   (WMI-Activity)

$RYT30BW.evtx 8,260  (Task Scheduler)

$RA714GE.evtx 5,188   (Sysmon)

$RQXN6M8.evtx 15,364  (Powershell)

$R2DJFWL.evtx 14,404  (System)

﻿

The original filenames can be found within FTK by selecting the filename and viewing the hex content in the viewer below the filename.

![image](https://github.com/user-attachments/assets/bdfb1578-32e8-4dec-9ef9-7c3a434d1780)

7. Select Action > Open Saved Log and add each exported log.
﻿
![image](https://github.com/user-attachments/assets/bb1327d9-c963-4853-8124-cd84938e6487)



NOTE: While outside the lab scope, it is worth noting that during an investigation of the event logs, an external tool such as Chainsaw or EvtxECmd is typically used rather than trying to open and search each event log individually.


8. Select $RJWWJQK.evtx. Select Action > Filter Current Log. In the new window, select Logged and Custom Range. In both the From and To drop-down menus, select Events On and choose the dates and times as shown in Figure 16.3-28.

![image](https://github.com/user-attachments/assets/de955a0b-9d08-4832-82b4-f8ca9a8dc861)


﻿9. Open the log $RJWWJQK.evtx (Security.evtx), which shows users that recently attempted to log in to the system. Searching for the recent users on the system is the first thread to pull to know where to look next for additional details. Look for suspicious activity such as a few sequential failed logins followed by a successful one. The successful login provides a timestamp used as a reference point for the events that took place afterward. The event IDs for this type of activity are 4624 and 4625.


NOTE: Ignore the logins from the user trainee as this account is not associated with malicious behavior.


10. Select Action > Filter Current Log. There is a textbox that contains <All Event IDs>. Replace the text with the following and select OK to close the window:
4624,4625



11. Locate the two events where the usernames defaultuser and bob have failed logins followed by a successful login by the administrator account. This is typical of a password spraying attempt followed by the actor eventually finding a successful username/password combination. Focus on the time range of May 26, 2022 from 16:09:21 to 16:17:30.


The next step focuses on investigating suspicious activity that occurred once the actor obtained access to the host.


12. Investigate the following logs and event IDs:
    
$R2DJFWL.evtx log (System.evtx) and event ID 7045: Indicates a new service was created.
$RA714GE.evtx (Sysmon.evtx) and event ID 11: Indicates a new file being created.
$RYT30BW.evtx (Microsoft-Windows-TaskScheduler%4Operational.evtx) and event ID 129: Indicates a new task being created/registered.
$RQXN6M8.evtx (Microsoft-Windows-PowerShell%4Operational.evtx) and event ID 24577: Indicates that a PowerShell script was executed from within the Integrated Scripting Engine (ISE) editor.
$R513JPG.evtx (Microsoft-Windows-WMI-Activity%4Operational.evtx) and event ID 5861: Indicates recent WMI subscriptions.


![image](https://github.com/user-attachments/assets/927a1ecf-b3a9-4e7a-91e2-eeeb4935c45f)


![image](https://github.com/user-attachments/assets/b2ddb98e-3c69-4407-b547-ef77f87decab)


Searching Through Slack Space
This second lab focuses on using Autopsy’s keyword search tool to determine where in a forensic image the already identified slack space is located so that it can later on be exported.

﻿

Workflow
﻿

1. Log in to the ubuntu20 VM with the following credentials, if necessary.

Username: trainee
Password: CyberTraining1!
﻿

2. Open a terminal and change directory to the trainee desktop:

cd ~/Desktop
﻿

3. Enter the following command to view all items in slack space using blkls:

blkls -s suspicious-mod.dd.sda1
﻿

Part of the output from step 3 will be used in step 5, below. 

﻿

4. Return to the Autopsy window that was opened in the previous lab and select Keyword Search at the top.


﻿

5. In the search field, enter any set of characters from the output in step 3, for example "h.i.d.d.e.n," then select Search.

﻿

6. Select Ascii and review the result in the right pane, as indicated in the image below:


![image](https://github.com/user-attachments/assets/c0a3c937-1c2c-479f-b3a7-ad1bb5e601f1)

![image](https://github.com/user-attachments/assets/48734f34-0127-48d5-86e5-e98d10945854)

![image](https://github.com/user-attachments/assets/583f9b2d-2e7d-49e7-b8fa-4bc567410aa0)


Exporting Slack Space Content
This lab demonstrates how to export data that has been identified as residing in slack space.

﻿

Workflow
﻿

1. Log in to the ubuntu20 VM with the following credentials, if necessary.

Username: trainee
Password: CyberTraining1!
﻿

2. Return to the Autopsy window that was opened in the previous lab and select File Analysis from the menu at the top.

﻿

3. Open the item that has slack space by selecting 12 on the far right under the Meta column.

﻿

4. Scroll to the bottom and select 2049.

﻿

5. Select Export Contents and make a note of the filename, as shown in the image below:
![image](https://github.com/user-attachments/assets/e51703d9-0d52-40f5-80f3-6a3f7bcec077)

6. In a terminal window, read the file that was just downloaded to see that the contents of slack space were exported successfully:
cat /home/trainee/Downloads/vol1-Fragment2049.raw



############CDAH-M17L2-Bootkits#############

MBR Fundamentals
The Boot Process
﻿

Figure 17.2-1 illustrates the generic boot process. This process starts with the Basic Input/Output System (BIOS) loading the MBR during the PC boot. The master boot code locates the active partition of the drive. This partition is known as either the Volume Boot Record (VBR) or the first stage bootloader. The active partition then activates the second stage bootloader and runs the respective bootloader for the installed Operating System (OS) kernel. 
Common bootloaders for the various operating systems are listed in Table 17.2-1, below:

![image](https://github.com/user-attachments/assets/28afa7ab-d699-4b12-8e12-5aa5d93a2477)

Unified Extensible Firmware Interface (UEFI)


This lesson focuses on the traditional BIOS/MBR boot process described above. However, another option, UEFI, offers better security in modern systems, so it is worth introducing briefly. 


UEFI is a modern mechanism for preventing unauthorized code from executing prior to the boot process. The primary component is the GUID Partition Table (GPT), which replaces the MBR in the boot process. The GPT is responsible for validating the firmware prior to executing the bootloader. If a bootkit is successful in bypassing the firmware validation, the security mechanisms provided by UEFI are rendered useless.


Even with UEFI available, modern computers still support the traditional boot process. The rest of this lesson focuses on the older MBR format for the traditional process. 


The MBR Layout


The MBR is the section of the hard disk that contains the location of the bootloader for the primary OS. Figure 17.2-2, below, illustrates the layout of the MBR, which comprises the following sections:
Master Boot Code (MBC): Contains the first 446 bytes of the MBR (starting with 0). This section locates the OS bootloader within the disk partition table. 
Disk Partition Table: Contains four partitions that each contain 16 bytes, for a total of 64 bytes in the table. Each functioning partition table ends with 0x55AA.
Signature: Contains the "magic number" that indicates that the MBR checksum is valid. This must provide a value of 0x55AA. Any other value indicates the MBR may be corrupt, infected by malware, or unbootable.

![image](https://github.com/user-attachments/assets/9fa63afa-7b83-4694-8b38-75612064eeff)

Partition Table Analysis


The partition table starts at byte offset 446 (0x1BE) and continues until byte offset 510 (0x1FE). The partition table entry contains information about the partition boot status, format, its physical location on the drive, and its size. Table 17.2-2, below, displays the byte values of the partition table entries.

![image](https://github.com/user-attachments/assets/c5d7c3ca-8b02-4f9a-bac4-1eccd1f971ba)



Methods of MBR Analysis


The information presented above in Table 17.2-2 can be collected and analyzed using a variety of tools, both command line and graphical. On Linux, command line tools such as strings or xxd extract human-readable data from the forensic disk image to include the MBR. The human-readable output of these tools allows a trained analyst to conduct a visual inspection of the data. On Windows, graphical applications such as Autopsy or the Forensic Tool Kit (FTK) analyze a disk image and MBR. These tools provide the most effective methods, but any application that displays the contents of a forensic disk image is also sufficient.

![image](https://github.com/user-attachments/assets/0db654c6-551d-4728-8378-da6ba5b6ee98)

Analyzing Local Disk MBR
Bootkits
﻿

A bootkit is a bootable type of malware that modifies the standard boot process. The common goal for most bootkits is to bypass OS signature validation so that other malware can be installed without bringing attention to the user. Bootkits work below the operating system layer by infecting the Master Boot Record (MBR) or Globally Unique Identifier Partition Table (GPT). Installation typically involves overwriting the portion of the boot code responsible for executing the OS bootloader.

﻿

Bootkits are a type of rootkit. A rootkit is any malware that modifies a running OS. An example of a rootkit modification is hiding network connections and processes. Rootkits may also create hidden directories and entire file systems. Detecting a bootkit is significantly more difficult than detecting a rootkit infection. As an example, when performing dynamic analysis of a bootkit, the PC may not boot at all. Static analysis allows analysts to view the MBR using tools such as FTK Imager on Windows, or by using the Linux utility dd to carve out the MBR for inspection.

﻿

The MITRE Adversarial Tactics, Techniques, and Common Knowledge (ATT&CK®) framework identifies bootkits as a pre-OS boot technique with technique ID T1542.003. 

﻿

Analyze a Local Disk MBR
﻿

Load disk images for MBR analysis and pull out various information about the MBR. Observe features of an uninfected MBR.

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) win-hunt with the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Start the application FTK Imager by selecting it from the Windows Start menu.

﻿

3. Load the local disk image by selecting Add All Attached Devices in the File menu.

﻿

FTK Imager loads the physical drive and any logical drives that it detects. 

﻿

4. Load the bottom right pane with the hex dump of the disk image selected by selecting the drive \\.\PHYSICALDRIVE0 from the Evidence Tree pane.

﻿

Figure 17.2-4, below, displays the ASCII hex dump of the MBR.

![image](https://github.com/user-attachments/assets/fa4739d2-a51a-4f5a-8dff-6ae5e1a45cba)

Analyze Disk Image MBR
In most cases, an analyst conducting a forensic investigation may uncover a bootkit unintentionally, while analyzing an image with another type of malware, such as ransomware. While it is difficult to definitively claim there is a bootkit in the following lab, it is still worthwhile to identify the differences between the forensic disk image and a clean MBR. For bootkit detections, the more experience an analyst has with viewing MBRs, the more familiar they will be with identifying entries that are out of the ordinary.

﻿

﻿

Analyze the MBR of a Disk Image
﻿

Load a forensic disk image that contains a bootkit. Compare features of the bootkit-infected MBR with the clean MBR from the previous lab. Continue working in the VM win-hunt to complete the following lab. 

﻿

Workflow
﻿

1. Open a second instance of FTK Imager from the Windows Start menu.﻿﻿

﻿

2. Add the image bootkit.bin as an evidence item by navigating to File and selecting Add Evidence Item…﻿

﻿

3. In the window Select Source, select Image File, then select Next. 

﻿

4. On the page Select File, select Browse, then select the file bootkit.bin on the desktop at the path C:\Users|trainee\Desktop\bootkit.bin﻿

﻿

5. Load the bootkit image into the Evidence Tree by selecting Finish.

﻿

The boot code section of the local disk image from the previous lab and the infected disk image from this lab have different sizes. The boot code section of the local disk image stops at offset 0x1BD with no padding. Although the infected disk image also stops at offset 0x1BD, it has 58 bytes of 0x00 padding to ensure that the MBR checksum remains valid. Figure 17.2-5, below, displays the values of offset 0x1FE and 0x1FF.

﻿![image](https://github.com/user-attachments/assets/40c9c933-7377-4c93-afb7-0474abf856fc)

Timeline Overview
A timeline is a list of events that occurred on a system and the times that they occurred. During a forensic examination, a timeline can be the most valuable tool available to an analyst due to the number of files involved and their organization within the forensic image. A forensic image may comprise thousands of files. In addition, Operating Systems (OS) do not store data in a structure well organized by date; instead, folders and files are nested within other folders. A timeline is helpful when a particular date or time is known or suspected to be associated with malicious activity, as it can filter an entire forensic image to just a few events based on the time range of interest. This greatly reduces the time required for analysis. For example, if 2022-01-01 12:00:00 is the determined date of suspicious activity, a timeline can show all files and events that were created, modified, or deleted at that time across the entire forensic image. Such a focus helps the analyst more efficiently determine the actions of the malicious actor.

﻿

Timeline Types
﻿

Two types of timelines are available during a forensic investigation:

Forensic timeline: A file that contains all dates and actions that occurred within an entire forensic image.
Analysis timeline: The cumulative result of analysis; it includes each important finding, to be used in a forensic report or to illustrate the process of events that occurred. 
﻿Table 17.3-1 provides an example of an analysis timeline:
﻿

﻿![image](https://github.com/user-attachments/assets/ce1f50f5-a6d4-49a8-90f8-5337dfd69902)

 Tools for Creating Timelines
Manually creating a timeline can be quite time consuming, but tools are available that create timelines automatically with supplied parameters. Examples of such tools are Autopsy, KAPE, and Plaso. Plaso is the industry standard for creating forensic timelines, and, in fact, Autopsy and KAPE both require a Plaso plug-in that enables them to automatically create a timeline.

﻿

Autopsy
﻿

The forensic tool Autopsy is available for both Windows and Linux, but its functions differ between the two OSs. The Windows version of Autopsy creates a timeline as part of its ingestion and analysis whenever a forensic image is added into the tool; the Linux version does not. 

﻿

﻿

KAPE
﻿

The forensic tool KAPE runs only on Windows. It can create a timeline from a Windows host in fewer than 5 minutes when it is focused only on the Master File Table (MFT), registry files, and event logs.

﻿

﻿

Plaso
﻿

As stated earlier, Plaso, formerly called log2timeline, is the industry standard for creating timelines of forensic images. Plaso supports analysis of Windows, Linux, macOS, Android, and iOS forensic images. Plaso can parse many files that typically require additional tools to be read. For example, Plaso can read Windows Event Logs, registry files, databases, volume shadow copies, and MFTs. Plaso reads all these files; extracts the metadata of files, dates, and times of events that have occurred; places the output into a .plaso file; and creates a Comma-Separated Values (CSV) file. Plaso is installed on the SIFT Workstation, a collection of incident response and forensic tools, by default. 
Table 17.3-2 provides descriptions of Plaso’s three main command-line tools, pinfo.py, psteal.py, and psort.py:

﻿![image](https://github.com/user-attachments/assets/eb57f407-00bb-4080-92ce-fb2ed33bd7c2)



The following command creates a timeline from Plaso:
psteal.py --source image.raw -o dynamic -w timeline.csv



The .plaso file is a Structured Query Language (SQL) database that contains more details than the resulting CSV file and, like the CSV file, can be loaded into other tools, including Timesketch, Splunk, Elastic, or any queryable tool, for analysis. However, because the .plaso file is generally larger than the CSV file and may require an hour or more to be indexed (depending on its size), the recommendation is to ingest the CSV file instead. 


Even though CSV files are typically smaller than their corresponding .plaso files, they are often too large to be opened by spreadsheet-processing software. For example, Microsoft Excel can read only about the first 20,000 lines of a 3-Gigabyte (GB) CSV timeline file. Timesketch is the most common tool used for timeline analysis, as it is built strictly for this purpose. Other solutions are to use Eric Zimmerman’s EZViewer tool or Timeline Explorer in Windows or to use the command line to grep through and filter the file in Linux.


Timesketch


Timesketch has seven main parameters on which data can be filtered: Datetime (UTC), message, display_name, parser, source, timestamp_desc, and Timeline name.
![image](https://github.com/user-attachments/assets/8e8d3a56-553f-4a27-adf1-e6d770497518)

Time Zones


By default, Plaso generates a timeline in Coordinated Universal Time (UTC), not in the time zone that was in use in the forensic image. The local time zone of the forensic image must be known before beginning analysis. When running Plaso, it is possible to specify in the command which time zone to output the results in. The following examples specify that the timeline’s CSV file should be written in the Eastern time zone.


To create the timeline with the time zone specified, run the following command: 
psteal.py --source image.raw --output-time_zone EST5EDT -o dynamic -w timeline.csv



When reading from a .plaso file in an already created timeline and to convert the timeline to a different time zone and write to a new CSV file, run the following command:
psort.py --output-time_zone EST5EDT timeline.plaso -w timeline.csv



Adding Efficiency


If only a few files or artifacts are of interest in a forensic image, Plaso can be provided with those filenames instead of the whole forensic image, resulting in a much smaller .plaso file and, therefore, more rapid processing times. If, instead, the entire forensic image must have a timeline generated for it but a select date range is known to contain items of interest, the timeline can be minimized for faster analysis.


A timeline can be filtered by the following command. The UTC time must be specified, even if the .plaso file is generated with a different time zone.
psort.py -q filename.plaso "date > '2022-05-26 20:09:00' and date < '2022-05-26 20:18:00'" -w filtered.csv



This command extracts only the dates between those specified to greatly reduce the physical size of the timeline and to contain only events in that range. 

Timeline Event Types
Timelines gather multiple files and event information. A timeline can include such actions as the following:

System events
System reboots
Crashes
Log rotations
Service restarts
Software installs
Network connections
System configuration modifications
User activity events
Logon/logoff
Web browsing
File creation/deletion/modification times
Recent files
﻿
![image](https://github.com/user-attachments/assets/d0a78b86-00ec-4cc0-ac0d-67b04bd012aa)

Create and Filter a Timeline
In the following lab series, create a forensic timeline using the provided Security.evtx file, filter the timeline, and view the results. A single Windows Event Log is used for time efficiency. The log focuses on 2022-05-26 16:09:00 through 2022-05-26 16:18:00 EST.

﻿

Complete the steps in the following workflow to create the timeline and filter it using a spreadsheet-processing application and the command-line tool grep. 

2. Open a Terminal window, and run the following command to change to the directory where the Security.evtx file is located:
cd /home/trainee/Desktop/Lab1

![image](https://github.com/user-attachments/assets/d255ae79-6d69-43dd-ad5a-0824ed46be4b)



3. Run the following command to create a timeline from the Security.evtx file. This requires approximately 6 minutes.
psteal.py --source Security.evtx --output_time_zone EST5EDT -o dynamic -w security.csv

The result is a .plaso file:

![image](https://github.com/user-attachments/assets/c2b17be4-eb1d-434c-9298-6b8e930f2860)

NOTE: The .plaso file is named according to the date and time the psteal.py command was run. Thus, the .plaso filename in Figure 17.3-2 is not the same as the filename generated during this workflow. 

4. Run the following command to verify creation of the new files and their sizes:
ls -alh

![image](https://github.com/user-attachments/assets/3efae42e-3810-4ca1-9b79-89f0608a2a3d)



Because the .plaso file is a database generated by Plaso that includes all the timeline information, it is much larger than the Security.evtx file. The security.csv file is even larger than the .plaso file, which is uncommon, but for ease of reading the contents, the best timeline file to use is the CSV file.


As stated earlier, using a spreadsheet application for timeline analysis of a CSV file can be difficult. To understand this more fully, open the CSV file in LibreOffice Calc, and attempt a search.


5. Run the following command to view the contents of the security.csv file in LibreOffice Calc. Ignore any errors in terminal and when LibreOffice Calc opens, select OK.

libreoffice.calc security.csv

![image](https://github.com/user-attachments/assets/7dacb75c-3b48-4810-b80a-3929d0b52020)

6. The security.csv file can be edited, sorted, and modified in LibreOffice Calc. Perform a search for 4625 (failed login attempts), and try to read the events linked to the results. Reading the events is challenging, as the rows are long, and the search term is found in other fields beyond the message field. 


7. Close the file, and return to the Terminal. 


8. Run the following commands to view the date/time range of events in the security.csv file:
head -n 2 security.csv
tail -n 2 security.csv

![image](https://github.com/user-attachments/assets/658498e9-ac69-492f-936f-23ca780a868b)

9. The results of the previous command show that the date range in this timeline is 2022-03-27 through the day the timeline is created. (Figure 17.3-5 shows a different end date, as its timeline was created on 2022-07-05.)


10. Run the following command to create a smaller timeline that includes only the window of time during which the malicious activity occurred: 
psort.py -q (plaso file date)-Security.evtx.plaso "date > '2022-05-26 20:09:00' and date < '2022-05-26 20:18:00'" -w filteredsecurity.csv --output_time_zone EST5EDT



NOTE: The EST time window is 16:09 through 16:18, but, by default, Plaso ignores that the .plaso file was output in the EST timezone and reads it back in as UTC. If the times of 16:09 and 16:18 are used, no data is exported.


11. Run the following command to filter the log for failed and successful logins during the new time window:

cat filteredsecurity.csv |egrep -v "'trainee'|ANONYMOUS|DC01\\$"|egrep "4624|4625"

4624 is successful logon
![image](https://github.com/user-attachments/assets/d930c394-2c8e-43dc-84b1-195c28d9a071)

![image](https://github.com/user-attachments/assets/4566d016-1944-41a8-b368-4d1cab84676b)

Six events are found: four for failed logins and two for successful logins. In this timeline view, it is difficult to determine why the same user had two failures when they attempted to log in only once. The reason is that Plaso shows both when the security log was last modified and when it was created. Another filter would need to be added to the grep to remove the creation logs.


As stated earlier, the tool Timesketch is built strictly for timeline analysis. Complete the steps in the following workflow to filter a timeline and view the results using Timesketch.

Workflow 


1. Open a Terminal, and run the following commands. Enter the trainee password CyberTraining1! when prompted.

cd /home/trainee/Desktop/


sudo ./StartTimesketch.sh

Locate Log Discrepancies via a Timeline
Read the scenario for the following lab, and use the information gained to determine the actions that the malicious actor took. Keep a running log in LibCalc to track the timeline. Then create an analysis timeline showing those events. 
﻿﻿

﻿

Scenario: A malicious actor obtained access to a Windows server, established two methods of persistence on the host, and attempted to cover their tracks. The time range of this activity is 2022-05-26 16:09:00 to 2022-05-26 16:18:00 EST. 

﻿

Complete the steps in the following workflow to verify that needed logs for analysis are captured.

﻿

Workflow
﻿


1. Log in to the VM win-hunt using the following credentials: 

Username: trainee
Password: CyberTraining1!
﻿

2. Open FTK® Imager to begin the forensic examination. User Access Control (UAC) appears, as this application is being run with administrative privileges. Select Yes to open FTK Imager.

﻿

3. Add the forensic image E:\winserv.001 into FTK Imager: 


![image](https://github.com/user-attachments/assets/8d48ef73-339c-4eb8-956d-767df8a10372)

![image](https://github.com/user-attachments/assets/e7756519-9d4b-4d93-b72d-118f1b4a7b2b)

4. Expand winserv.001 to Partition 2/Windows [NTFS]/[root]/Windows/System32/winevt/Logs:

![image](https://github.com/user-attachments/assets/10127643-082f-422e-8b79-57bb0c557904)

![image](https://github.com/user-attachments/assets/81406c2a-50a8-43eb-83f1-4aaf9d962655)

5. Search for the following logs in the Logs directory:
System
Security
Microsoft-Windows-Sysmon%4Operational.evtx
Microsoft-Windows-WMI-Activity%4Operational
Microsoft-Windows-TaskScheduler%4Operational
Microsoft-Windows-PowerShell%4Operational


The above event logs are not present in the forensic image. This indicates that the attacker potentially attempted to cover their tracks; it also shows the importance of sending logs to Elastic before they can be cleared locally. 


NOTE: Alternatively, this log data can be extracted by obtaining a timeline from the forensic image.


Before creating the timeline, the logs in Elastic should be verified to ensure that the above logs have been captured and are available for analysis even though they are not present in the forensic image. 


6. Open Chrome, and select the Discover - Elastic bookmark.



7. Select Advanced, and on the page stating Your connection is not private, select the link to Proceed to 199.63.64.92 (unsafe).


NOTE: The message Your connection is not private appears because the Security Onion certificate is not installed in the browser; it is not a security concern. 


8. Log in to Elastic using the following credentials:

Username: trainee@jdmss.lan
Password: CyberTraining1!



9. Set the Kibana filter dates as May 26, 2022 @ 16:08:00.000 to May 26, 2022 @ 16:18:00.000.


10. Query for the hostname of the device being analyzed to filter the logs, using agent.name:dc01.


11. Change the view filter to see only the Windows Event Logs. In the search box, enter event.module. Select the plus (+) icon to the right of event.module to add the field as a column. 

12. Search for the fields event.provider, winlog.channel, and winlog.event_id, and add them as available columns.


NOTE: The winlog.event_id shows a warning icon, but the lab works properly, regardless.

13. Search for the sysmon events using the query event.module:"sysmon" to confirm they are available in Elastic.


Logs were cleared from the forensic image but are present in Elastic. If, in this scenario, logs were not being sent to Elastic, all these events would appear not to be available for analysis. Also, Elastic is capturing only Windows Event Logs, not others. This is where the creation of a timeline of the forensic image is needed.


Complete the steps in the following workflow to create a timeline of the forensic image.


![image](https://github.com/user-attachments/assets/9d0c23d1-ab5b-4a30-bfd0-144474f7916d)

Workflow


1. Log in to the VM ubuntu20 using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open a Terminal, and run the following command to go to the Lab2 folder on the desktop:

cd ~/Desktop/Lab2



A complete timeline for a Windows server is located in the Lab2 folder. The timeline file is large and would require significant time to investigate. 


3. Run the following command to generate a smaller timeline that includes only the window of time during which the malicious activity occurred. The timeline generation requires about 15 minutes to complete.

psort.py -q 20220701T160413-winserv.001.plaso "date > '2022-05-26 20:09:00' and date < '2022-05-26 20:18:00'" -w filtered.csv --output_time_zone EST5EDT

Workflow


1. Log in to the VM ubuntu20 using the following credentials:
Username: trainee
Password: CyberTraining1!



2. Open a Terminal, and run the following command to go to the Lab2 folder on the desktop:

cd ~/Desktop/Lab2



A complete timeline for a Windows server is located in the Lab2 folder. The timeline file is large and would require significant time to investigate. 


3. Run the following command to generate a smaller timeline that includes only the window of time during which the malicious activity occurred. The timeline generation requires about 15 minutes to complete.

psort.py -q 20220701T160413-winserv.001.plaso "date > '2022-05-26 20:09:00' and date < '2022-05-26 20:18:00'" -w filtered.csv --output_time_zone EST5EDT




4. Open Firefox, browse to localhost, and select Logout in Timesketch. (This is necessary to avoid a Cross-Site Request Forgery [CSRF] token error, which may occur when Timesketch remains dormant for several minutes.)

http://localhost




5. Log in to Timesketch using the following credentials:
Username: trainee
Password: CyberTraining1!



6. Select New Investigation, and name it filtered date range.


7. Upload the filtered.csv file from /home/trainee/Desktop/Lab2/ into Timesketch.


8. Once indexing is complete, select Begin to explore your data and enable the filters Datetime, message, display_name, parser, source, and timestamp_desc.


9. Begin analyzing the events in Timesketch (in the VM ubuntu20) and Elastic (in the VM win-hunt) to identify eight events that indicate malicious behavior. To scope the search, the events contain the following:
Logins/failures
Scheduled tasks
Windows Management Instrumentation (WMI)
Services
Remote access
A malicious binary


These events cannot be found in the forensic image and rely on Elastic or the timeline to discover them. As a reminder, Elastic contains only Windows Event Logs, and Timesketch has information, such as file table information, registry events, and web history, that Elastic does not.


10. Create an analysis timeline that outlines the events discovered in chronological order.


Use this lab to answer the following question.



4. Open Firefox, browse to localhost, and select Logout in Timesketch. (This is necessary to avoid a Cross-Site Request Forgery [CSRF] token error, which may occur when Timesketch remains dormant for several minutes.)

http://localhost




5. Log in to Timesketch using the following credentials:
Username: trainee
Password: CyberTraining1!



6. Select New Investigation, and name it filtered date range.


7. Upload the filtered.csv file from /home/trainee/Desktop/Lab2/ into Timesketch.


8. Once indexing is complete, select Begin to explore your data and enable the filters Datetime, message, display_name, parser, source, and timestamp_desc.

![image](https://github.com/user-attachments/assets/09869aeb-efd8-448d-a45f-0cf593b38cb4)

![image](https://github.com/user-attachments/assets/6d128bd8-0ca6-4044-a8d5-c9bf2031418c)


9. Begin analyzing the events in Timesketch (in the VM ubuntu20) and Elastic (in the VM win-hunt) to identify eight events that indicate malicious behavior. To scope the search, the events contain the following:
Logins/failures
Scheduled tasks
Windows Management Instrumentation (WMI)
Services
Remote access
A malicious binary


These events cannot be found in the forensic image and rely on Elastic or the timeline to discover them. As a reminder, Elastic contains only Windows Event Logs, and Timesketch has information, such as file table information, registry events, and web history, that Elastic does not.


10. Create an analysis timeline that outlines the events discovered in chronological order.


Use this lab to answer the following question.

![image](https://github.com/user-attachments/assets/461b561e-18d0-46d1-9400-18ef8d43eec3)

![image](https://github.com/user-attachments/assets/702fdf04-0323-4323-89c5-ce856035bea5)

![image](https://github.com/user-attachments/assets/95b2aa73-c049-4fcc-8a4b-c40e630d0ca8)

![image](https://github.com/user-attachments/assets/937d1d9b-fc33-48f1-a655-2e5ad31b514e)

![image](https://github.com/user-attachments/assets/62c8a6b5-ae3f-4e7f-a055-214fd4885e8f)


2. Select the FTK Imager icon on the taskbar:

3. Select Yes when the prompt Do you want to allow this app to make changes to your device? appears.


4. Within FTK Imager, select File, and select Capture Memory…. 


NOTE: A Capture Memory icon is also located on the toolbar:

5. Within the Memory Capture dialog box, under Destination Path, select Browse.


6. Create a new folder on the path E:/Evidence, and name the folder Memory Dump.


NOTE: In the field, RAM data is typically dumped into a separate drive on the host. In this host, the E: drive is used for evidence collection. During an investigation, it is imperative to record the time, date, and description of the device when the memory dump is collected. 


7. In the Destination filename input field, enter memdump1.mem.


8. Select Capture Memory.


FTK Imager captures a memory dump of the host’s RAM. The memory dump requires up to 5 minutes to complete. 


9. When the memory dump is complete, select Close. 


10. Navigate to the newly created folder on the path E:/Evidence. The folder contains the memdump1.mem file. The .mem file type contains the RAM data at the time and date the memory dump was collected. The size of the .mem file varies, based on the data collected from the host’s RAM. However, the file size commonly reaches gigabytes (GB). 


Complete the steps in the following workflow to access LiME and collect a memory dump for a Linux host.

Workflow


1. Log in to the VM it-maint-0 using the following credentials:

Username: trainee
Password: CyberTraining1! 



2. Open Terminal.


3. Navigate to the directory /opt/LiME to access LiME.


4. Enter the following command to view the contents of the folder:

ls



5. Navigate to the /opt/LiME/src folder.



The src folder contains multiple files, one of which is the makefile. The makefile is the assembly instructions for creating a kernel module. The kernel module, a .ko file, is loaded to extract the memory dump from the Linux host. 


6. To create the kernel module, enter the following command within the src folder:

sudo make



When prompted, enter the password for the trainee account:

CyberTraining1!



The make command returns a large amount of output. The bottom of the output contains the newly created kernel module, lime-5.13.0-39-generic.ko.


7. To load the newly created kernel module, enter the following command:

sudo insmod ./lime-5.13.0-39-generic.ko "path=/opt/LiME/t.mem format=lime"




The Linux OS allows users to load kernels. The insmod command in Linux systems is used to insert modules into the kernel. 


The command above loads ./lime-5.13.0-39-generic.ko to execute the memory dump. The results of the memory dump are in a newly created .mem file located on the path /opt/LiME/t.mem. The format of the memory dump is LiME.


8. To verify that the kernel loaded properly, execute the following command:
lsmod | grep lime



The output is as follows:
lime      16384 0 



The file size — 16,384 megabytes (MB) — is the same size as the RAM of the host, indicating that the kernel was loaded properly. 


9. To verify that the memory dump was executed correctly, run the following command to view the opt/LiME directory:
ls /opt/LiME

 
The memory dump is located within the t.mem file in the directory. 

![image](https://github.com/user-attachments/assets/0c99b13d-02d8-4741-9e4e-a167f70aa7b5)

![image](https://github.com/user-attachments/assets/47331bc7-800a-4f73-bc25-eeafb01833bd)

![image](https://github.com/user-attachments/assets/10ebe48b-1c16-4061-a130-07d69c79fef8)

![image](https://github.com/user-attachments/assets/a65b07fa-37b6-428c-88d2-07187a5ad4f9)

![image](https://github.com/user-attachments/assets/dae47fab-3f6e-4c90-92a0-d1f30f59170f)

![image](https://github.com/user-attachments/assets/7af23472-70e9-47e5-bb80-6657ef9824b5)


Analyze a Memory Acquisition with Volatility
In the following workflow, perform forensic memory analysis using Volatility to discover IOCs.

﻿

Workflow
﻿

1. Log in to the Virtual Machine (VM) sift using the following credentials:

Username: trainee
Password: CyberTraining1!
﻿

2. Open Terminal.

﻿

3. Navigate to the acquisitions folder by entering the following command:

cd acquisitions/
﻿

The acquisitions folder contains a memory acquisition file named cridex.vmem. To investigate this memory acquisition with Volatility, it is first necessary to identify which Operating System (OS) profile to use. Volatility needs this information to interpret the format of data in a memory acquisition. By not specifying a profile (or specifying an incorrect profile for the OS associated with the memory acquisition), Volatility would be unable to properly analyze the file. 

﻿

4. Enter the following command to identify the appropriate OS to use:


vol.py -f cridex.vmem imageinfo
﻿

Part of the output identifies the profile to use, as follows:


Suggested Profile(s) : WinXPSP2x86, WinXPSP3x86 (Instantiated with WinXPSP2x86)

![image](https://github.com/user-attachments/assets/750c7c91-caf6-457e-9e9a-db8cf48bf38e)

In this case, WinXPSP2x86 is the profile necessary for the successful execution of the program, indicating that the system from which this memory acquisition was taken had a 32-bit Windows XP OS with Service Pack (SP) 2.


An important first step in memory acquisition analysis is the enumeration of running processes. Volatility can list the processes it can identify as being live in memory, allowing the analyst to observe the relationships between processes (parent and child processes, for example) as well as any odd process names that might be suspicious. 


5. Enter the following command to acquire the process list:

vol.py -f cridex.vmem --profile WinXPSP2x86 pslist



Several processes are listed in the output of the above command. The process named reader_sl.exe is of interest. If this executable is legitimate, it is likely a process known as SpeedLauncher associated with Adobe Acrobat. This could be a legitimate process, but it is worth noting that the parent process for reader_sl.exe is explorer.exe. This suggests that a user process resulted in the execution of reader_sl.exe. This situation is worth investigating further.


If malware is within this memory acquisition, a possibility exists that the malware has made a network connection to a remote system for Command-and-Control (C2) functionality. Active Transmission Control Protocol (TCP) network connections can be observed through the connscan Volatility plug-in. 


6. Enter the following command to invoke the connscan plug-in:

vol.py -f cridex.vmem --profile WinXPSP2x86 connscan

The results of the above command show that the local address for the system this memory acquisition was taken from is 172.16.112.128. There are two active connections in memory to the external addresses 41.168.5.140 and 125.19.103.198, with both communicating over port 8080 and with a Process Identifier (PID) of 1484 — the same PID as the explorer process that is the parent process of reader_sl.exe. This could be an indicator of malware running in memory communicating with an external malicious host.


Given the previous indicators of a possible malware infection, Volatility’s yarascan plug-in can be used to identify known patterns of malware behavior. If yarascan returns matching behavior, this can increase confidence that a process in memory is malicious. 


7. Enter the following command to invoke the yarascan plug-in:

vol.py -f cridex.vmem --profile WinXPSP2x86 yarascan -y malware_rules.yar -p 1484 | sort | uniq

The above command tells Volatility to scan the memory acquisition with YARA, comparing patterns of behavior with signatures found in the file malware_rules.yar. The specification of PID 1484 focuses the YARA analysis on the explorer process identified previously. The results are piped to sort and uniq, which makes the output more readable. Examination of the output reveals the following:

Owner: Process explorer.exe Pid 1484
Rule: FVEY_ShadowBrokers_Jan17_Screen_Strings



This output states that a behavior pattern found within the activities in memory for PID 1484 matches a YARA rule for malicious behavior.


Because YARA identified malicious behavior in the explorer process with PID 1484, further analysis of which other processes associated with PID 1484 can be performed through the pstree plug-in.


8. Enter the following command to invoke the pstree plug-in:

vol.py -f cridex.vmem --profile WinXPSP2x86 pstree | grep 1484



The output of the above command shows that the only child process of PID 1484 is PID 1640: reader_sl.exe. 


Volatility allows for the extraction of an executable from a memory acquisition, such as reader_sl.exe. Because the PID for this executable is known, the following command can be used:

vol.py -f cridex.vmem --profile WinXPSP2x86 procdump -p 1640 –-dump-dir .



9. Run the command above.


The above command extracts reader_sl.exe to the current directory with the file name executable.1640.exe. This file may then be analyzed separately in a dynamic analysis sandbox, or the file hash value may be searched for within a known malware information repository, such as VirusTotal. This lab does not include this process, but the result is that reader_sl.exe is identified as a malware trojan.


Additionally, the addressable memory of the reader_sl.exe process can be extracted for further analysis using the following command:

vol.py -f cridex.vmem --profile WinXPSP2x86 memdump -p 1640 --dump-dir .



10. Run the command above.


The above command results in the creation of 1640.dmp, which can be further analyzed through strings to gather additional information:

strings 1640.dmp



This results in a significant amount of information and is difficult to parse without knowing what to look for. However, the external IP addresses 41.168.5.140 and 125.19.103.198 were previously identified through the connscan plug-in. A search for 125.19.103.198 in the memory dump for the process yields no results, but the following command provides interesting information:

strings 1640.dmp | grep “41.168.5.140” -C 5



11. Run the command above.


The above command results in the following:

POST /zb/v_01_a/in/ HTTP/1.1
Accept: */*
User-Agent: Mozilla/5.0 (Windows; U; MSIE 7.0; Windows NT 6.0; en-US)
Host: 41.168.5.140:8080
Content-Length: 229
Connection: Keep-Alive
Cache-Control: no-cache



The above results show that the reader_sl.exe process communicates out to 41.168.5.140 over port 8080. This matches the communication observed through connscan and is an IOC.


With knowledge that reader_sl.exe is malicious and communicates with an external IP, further investigation is worthwhile to determine if the malware is employing any persistence techniques. By doing so, additional IOCs can be found for identifying malicious artifacts on other systems. Volatility’s filescan plug-in searches the memory acquisition for references to file objects located on a system. In this case, interesting information can be gathered by focusing on executable files.


12. Run the following command to gather information about executable files:

vol.py -f cridex.vmem --profile WinXPSP2x86 filescan | grep exe



The following result from the above command is particularly interesting:

0x00000000024abd80  1  0 R--rw- \Device\HarddiskVolume1\Documents and Settings\Robert\Application Data\KB00207877.exe



This KB00207877.exe file in the result stands out because it is located in a user’s directory and uses Microsoft’s Knowledge Base (KB) naming scheme for an executable usually reserved for software patches. This is an odd location for the file to exist, warranting further investigation.


13. To determine if a relationship exists between KB00207877.exe and the malicious reader_sl.exe, analyze the strings in the 1640.dmp file by running the following command:

strings 1640.dmp | grep "KB00207877.exe"



The results of the above command show that references to KB00207877.exe exist in the memory of the reader_sl.exe process. This means that a connection, and a further IOC, does exist.


14. Run the following command to extract KB00207877.exe from the memory acquisition for further analysis using the dumpfiles plug-in:

vol.py -f cridex.vmem --profile WinXPSP2x86 dumpfiles --dump-dir . -Q 0x00000000024abd80



The -Q 0x00000000024abd80 specifies the memory offset that points to the KB00207877.exe file, as shown in the filescan output. As with reader_sl.exe, this file can also be identified as a malware trojan through further analysis. Given the naming convention used by the malware to hide itself as a benign program, it may be assumed that KB00207877.exe is used for persistent execution on a victim computer.


One common persistence technique used by malware is to abuse the Windows registry so that a malicious executable is run every time the Windows OS starts up. Volatility is capable of inspecting the Windows registry in a memory acquisition through the printkey plug-in.


15. Run the following command to invoke the printkey plug-in:

vol.py -f cridex.vmem --profile=WinXPSP2x86 printkey -K "Software\Microsoft\Windows\CurrentVersion\Run"



The above command results in a definitive reference to the malicious KB00207877.exe, as shown below:

Registry: \Device\HarddiskVolume1\Documents and Settings\Robert\NTUSER.DAT
Key name: Run (S)
Last updated: 2012-07-22 02:31:51 UTC+0000

Subkeys: 

Values:
REG_SZ        KB00207877.exe  : (S) "C:\Documents and Settings\Robert\Application Data\KB00207877.exe"



The above results lead to the conclusion that KB00207877.exe is used to persistently execute malicious code on the infected system through a startup registry key.


The following summarizes the forensic analysis of the memory acquisition through Volatility:
The imageinfo plug-in was used to identify the necessary Windows OS profile to use for analysis of the acquisition.
pslist was used to identify the processes running in memory.
connscan was used to enumerate active network connections.
yarascan was used to fingerprint known malicious activity.
pstree was used to observe the relationships between parent and child processes.
procdump extracted the malicious reader_sl.exe file from memory.
memdump extracted the addressable memory used by reader_sl.exe.
The strings command was used to identify that reader_sl.exe was responsible for communication channels out to external IP addresses identified through connscan.
An additional malicious executable was discovered using filescan.
KB00207877.exe was extracted from memory using dumpfiles.
KB00207877.exe was identified as the persistence mechanism used by the malware through examination of the Windows registry startup key through printkey.

This resulted in the identification of malware within the memory acquisition as well as several forensic artifacts for use in further analysis across other systems in the network.

![image](https://github.com/user-attachments/assets/e3645ca8-c89f-478f-ae84-d443525fcd3f)

![image](https://github.com/user-attachments/assets/d21c7a68-3916-4a3f-9f79-26387bd27809)

pslist


﻿







