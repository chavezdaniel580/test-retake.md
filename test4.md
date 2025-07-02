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








