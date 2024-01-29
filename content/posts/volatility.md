+++
title = 'Memory forensics with Volatility'
date = 2023-11-12T18:45:35+01:00
draft = true
author = "tmwProjects"
description = "This post covers the Volatility framework, focusing on Volatility2 and Volatility3, their setup in Linux, memory image analysis, and additional plugin features, catering to digital forensics enthusiasts."
tags = [
    "Forensics",
    "Volatility",
    "Memory Forensics",
    "Linux",
    "Dump"
]
+++

In this post, we explore the world of memory forensics through the lens of the Volatility framework. We delve into 
the differences between Volatility2 and Volatility3, providing insights into their unique features and capabilities. 
Setting up Volatility on Linux systems is detailed, covering both versions. The article also touches on the process of 
memory dumping, highlighting common tools used in this practice. We dive into the analysis of memory images with an emphasis 
on MemLabs, and discuss additional plugins that extend Volatility's functionality. Additionally, we cover the use of 
Volshell and the utility of Volatility3 as a library, offering a comprehensive guide for users in the field of digital forensics.

[![License](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-6B783D)](http://creativecommons.org/licenses/by-nc-sa/4.0/)

***

## Contents

* [What ist Volatility?](#)
* [Differences between Volatility2 and Volatility3](#)
* [Set up Volatility (Linux)](#)
  * [Volatility2](#)
  * [Volatility3](#)
* [Memory dumps](#)
  * [Process tools](#)
  * [Download samples](#)
* [Analysing memory image (MemLabs - Lab 1)](#)
* [Additional plugins](#)
* [Bottom line](#)
* [References](#references)
* [License](#license)

***

### What is Volatility?

Volatility is an open-source memory forensics framework for incident response and malware analysis. It is used for the 
extraction of digital artifacts from volatile memory (RAM) samples. Volatility supports memory dumps from all major 
operating systems, including Windows, Linux, and MacOS. Known for its versatility, it allows investigators to analyze 
RAM images to uncover significant details about system state, processes, network connections, and more, which are crucial 
in digital investigations, especially in scenarios where malware attempts to hide its presence on disk storage.

[**[Back to content]**](#contents)

***

### Differences between Volatility2 and Volatility3

The comparison between Volatility 2 and Volatility 3 reveals some significant differences and improvements in the 
functionality and user-friendliness of these forensic tools.

**Custom profiles and kernel support**: A major issue with Volatility 2 was the need to create a custom profile for each custom kernel version. 
custom kernel version. This was not only time consuming, but also 
problematic if the target computer did not have the necessary packages to create these profiles. Volatility 3 
on the other hand, no longer uses fixed profiles and has an extensive library of symbol tables, which makes it 
automatically generate new symbol tables for most Windows memory images. This makes the 
identification of structures within an operating system.

**Library and context**: Volatility 3 was designed from the ground up as a library. Components are independent, and the context 
required for the operation of a particular plugin at a particular time is contained in a state derived from a context interface. 
object derived from a context interface.

**Object model changes**: The object model in Volatility 3 has also changed. Objects inherit directly from their 
Python counterparts, which means that an integer object is actually a Python integer. In contrast to this 
Volatility 2 constructed complex proxy objects that were not compatible in all situations.

**Performance improvements**: Volatility 3 offers significant speed improvements. While Volatility 2 allowed access to 
access to live memory images, Volatility 3 reads the data once when the object is created and keeps it static 
static, even if the underlying layer changes. This significantly increases efficiency.

**Layers and layer dependencies**: The address spaces in Volatility 2 are designed as translation layers in Volatility 3, 
which can have multiple "dependencies". This enables the integration of functions such as swap space.

**Automagic functions**: Volatility 3 defines automagic processes more clearly and allows them to be activated or deactivated as required for each run. 
activate or deactivate for each run. This improves usability and efficiency.

**Scanning and output rendering**: Scanning remains very similar to Volatility 2, with the use of scanner objects. 
Output is via a TreeGrid object, which allows the library to be used independently of the interface.

**Windows and Linux support**: For Windows memory images, Volatility 3 provides automatic download of symbol tables, while 
symbol tables, while a specific symbol table is still required for Linux.

**Plugins in Volatility 2 vs. Volatility 3**

**Plugin development**
* **Volatility 2**: The development of new plugins requires a deeper understanding of the internal architecture of Volatility 2. Plugins are often specific to certain operating system versions or configurations.
* **Volatility 3**: Provides a more modern and flexible API for plugin development. It allows developers to implement more functionality with less code, simplifying development and maintenance.

**Community contribution**
* **Volatility 2**: Has an extensive collection of community-contributed plugins that cover a wide range of use cases.
* **Volatility 3**: Is still relatively new and therefore has a smaller but growing collection of community-contributed plugins. The move from Volatility 2 to 3 also means that some popular plugins from Volatility 2 are not yet available or have been rewritten for Volatility 3.

**Specific plugin features**
* **Volatility 2**: Includes some specific plugins that are still missing in Volatility 3. This includes certain forensic analysis functions that may be important for specific investigations.
* **Volatility 3**: Focuses on extending the plugin architecture to facilitate future development and customisation, which means that some specialised features of Volatility 2 are still under development or planned.

**Plugin compatibility**
* **Volatility 2 and 3**: Due to structural changes in Volatility 3, plugins from Volatility 2 are not directly compatible with Volatility 3. Developers will need to rewrite or adapt plugins for the new architecture.

**Efficiency and speed of the plugins**
* **Volatility 3**: Benefits from overall improvements in memory management and efficiency, which has a positive impact on plugin execution speed.

**Scope and flexibility**
* **Volatility 3**: Offers greater flexibility in terms of plugin scope and customisability through its new design. It enables more complex and versatile analysis options compared to Volatility 2.

[**[Back to content]**](#contents)

***

### Set up Volatility (Linux)

#### Volatility2

**Standalone Image Usage**

* Download the standalone executable from the [Volatility releases page](https://www.volatilityfoundation.org/releases).

* Unzip the folder.

* Rename the image for easier use to "vol2"

* Run the executable directly from the command line:

```Bash
./vol2 -h
```

[**[Back to content]**](#contents)

***

**Installation**

**Clone the Volatility repository**

```Bash
git clone https://github.com/volatilityfoundation/volatility.git
```

**Navigate to the directory** 

```Bash
cd volatility
```

**Install dependencies**: 

```Bash
sudo apt-get install pcregrep libpcre++-dev python-dev -y
```

Run `python3 setup.py install` to install.

For more detailed instructions, please visit the [Volatility Installation Wiki](https://github.com/volatilityfoundation/volatility/wiki/Installation).

[**[Back to content]**](#contents)

***

#### Volatility3

Navigate to the /opt directory: 

```Bash
cd /opt
```

Clone Volatility 3 from GitHub: 

```Bash
git clone https://github.com/volatilityfoundation/volatility3.git.
```

Navigate to the cloned directory: 

```Bash
cd volatility3.
```

Install minimal dependencies: 

```Bash
pip3 install -r requirements-minimal.txt. 
```

For full functionality, use: 

```Bash
pip3 install -r requirements.txt.
```

Build and install (optional): 

```Bash
python3 setup.py build
python3 setup.py install
```

To check available options:

```Bash
python3 vol.py -h.
```

Symbol tables are essential for memory analysis in Volatility3, and specific packs for different operating systems are 
available for download. After downloading, the symbol table zip files should be placed in the volatility3/symbols directory. 
Windows symbols not found in the pack will be downloaded and cached automatically, while Mac and Linux symbols need manual 
generation. The first use of new symbol files in Volatility will require cache updating, which might take some time but 
is a one-time process. Comprehensive symbol tables for Linux are hard to supply due to the variability in kernel compilation. 
For verification of the symbol packs, SHA256, SHA1, and MD5 hashes are provided.

The Volatility 3 documentation on symbol tables explains their role in memory forensics and provides guidance on obtaining 
and utilizing them. It highlights the need for specific symbol tables for different operating systems and offers detailed 
steps for downloading these symbol packs. For situations where new symbol tables are required, especially for Linux, 
the documentation recommends using the tool `dwarf2json`. This tool aids in manually generating symbol tables, addressing 
the challenge of diverse Linux kernel versions. More information can be found on the Volatility 3 Documentation page.

For more detailed instructions, refer to the [Volatility 3 GitHub page](https://github.com/volatilityfoundation/volatility3).

[**[Back to content]**](#contents)

***

### Memory dump process

#### Process Tools

The process of memory dumps is an important technique in computer forensics and software debugging. Various tools offer 
specific functions and methods for creating and analysing memory dumps. Here is more detailed information on the tools 
mentioned:

**LiME (Linux Memory Extractor)**: LiME is a tool specifically for Linux systems that makes it possible to capture the contents of the RAM of a running Linux system. It is particularly valuable for forensic purposes as it extracts the memory contents in a way that enables forensic analyses.

**memdump**: This tool is a classic instrument for memory extraction. It is often used in computer forensics to extract the contents of working memory and make them available for further analyses.

**pcileech**: pcileech is a tool that focuses on direct memory access (DMA) attack software. It enables the capture of memory via PCIe DMA with specialised hardware such as FPGA and USB3380. It is available for both Windows and Linux and can handle different types of memory images, including Raw, Full Microsoft CrashDump and others.

**Creating a complete memory image under Windows 10**: Windows 10 offers built-in functions for creating memory images. These functions can be configured to automatically create memory images in the event of system crashes or other critical events, which is useful for error analysis and system diagnostics.

**ProcDump**: ProcDump, part of Microsoft's Sysinternals Suite, is an advanced command line utility for Windows. It enables the creation of memory dumps of processes based on various criteria such as CPU utilisation, memory usage or process end. ProcDump offers a variety of options to control the dump process, making it an important tool for developers and system administrators.

These tools play an important role in various areas such as IT security, system administration and forensic investigations. Each tool offers specific functions and methods to create and analyse memory images, making them valuable resources in their respective application areas.

[**[Back to content]**](#contents)

***

#### Download samples

MemLabs is an excellent resource for anyone interested in computer forensics and, in particular, analysing memory dumps. 
Designed as a training environment for Capture The Flag (CTF) challenges, MemLabs provides a collection of memory dumps 
specifically designed for education and training in digital forensics.

Each lab in MemLabs presents unique challenges based on real-world scenarios. These labs are designed to be suitable for 
both beginners and advanced users by covering basic concepts as well as complex forensic techniques. Participants can 
improve their memory analysis skills by solving various tasks ranging from identifying malware to reconstructing user activity.

In addition to MemLabs, the [Volatility Foundation](https://github.com/volatilityfoundation/volatility/wiki/Memory-Samples) stands 
as another crucial resource for memory samples in digital forensics and malware analysis. Their repository offers a diverse 
collection of memory dumps, ideal for understanding the intricacies of Volatility, a leading memory analysis framework. 
These samples encompass a variety of scenarios, including malware infections and system anomalies, making them invaluable 
for practitioners in cybersecurity and forensic research.

[**[Back to content]**](#contents)

***

### [Analysing memory image (MemLabs)

The command vol -f MemoryDump_Lab1.raw windows.info is used to extract basic information about the Windows system from 
the memory dump MemoryDump_Lab1.raw. This is a typical step in forensic analysis to get an overview of the analysed system. 
However, problems occur here because Volatility 3 cannot write necessary symbol files due to authorisation problems. 
These symbol files are essential for the analysis as they contain the necessary information about the structure of the 
operating system.

```Bash
vol -f MemoryDump_Lab1.raw windows.info     
                                                                               
Volatility 3 Framework 2.4.1
WARNING  volatility3.framework.symbols.windows.pdbutil: Cannot write necessary symbol file, please check permissions on /usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/symbols/windows/ntkrnlmp.pdb/3844DBB920174967BE7AA4A2C20430FA-2.json.xz
WARNING  volatility3.framework.symbols.windows.pdbutil: Cannot write necessary symbol file, please check permissions on /usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/framework/symbols/windows/ntkrnlmp.pdb/3844DBB920174967BE7AA4A2C20430FA-2.json.xz
WARNING  volatility3.framework.symbols.windows.pdbutil: Cannot write downloaded symbols, please add the appropriate symbols or add/modify a symbols directory that is writable
Progress:  100.00               PDB scanning finished                        
Unsatisfied requirement plugins.Info.kernel.symbol_table_name: 

A symbol table requirement was not fulfilled.  Please verify that:
        The associated translation layer requirement was fulfilled
        You have the correct symbol file for the requirement
        The symbol file is under the correct directory or zip file
        The symbol file is named appropriately or contains the correct banner

Unable to validate the plugin requirements: ['plugins.Info.kernel.symbol_table_name']
```

The command vol -v -f MemoryDump_Lab1.raw windows.info repeats the first step, but with verbose mode (-v) activated to 
obtain more detailed information about the process. This step serves to better understand the cause of the problems. 
Despite the verbose mode, the problem with the symbol files remains.

```Bash
vol -v -f MemoryDump_Lab1.raw windows.info

Volatility 3 Framework 2.4.1
INFO     volatility3.cli: Volatility plugins path: ['/usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/plugins', '/usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/framework/plugins']
INFO     volatility3.cli: Volatility symbols path: ['/usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/symbols', '/usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/framework/symbols']
INFO     volatility3.framework.automagic: Detected a windows category plugin
INFO     volatility3.framework.automagic: Running automagic: ConstructionMagic
INFO     volatility3.framework.automagic: Running automagic: SymbolCacheMagic
INFO     volatility3.framework.automagic: Running automagic: LayerStacker
INFO     volatility3.framework.automagic: Running automagic: WinSwapLayers
INFO     volatility3.framework.automagic: Running automagic: KernelPDBScanner
WARNING  volatility3.framework.symbols.windows.pdbutil: Cannot write necessary symbol file, please check permissions on /usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/symbols/windows/ntkrnlmp.pdb/3844DBB920174967BE7AA4A2C20430FA-2.json.xz
WARNING  volatility3.framework.symbols.windows.pdbutil: Cannot write necessary symbol file, please check permissions on /usr/local/lib/python3.11/dist-packages/volatility3-2.4.1-py3.11.egg/volatility3/framework/symbols/windows/ntkrnlmp.pdb/3844DBB920174967BE7AA4A2C20430FA-2.json.xz
WARNING  volatility3.framework.symbols.windows.pdbutil: Cannot write downloaded symbols, please add the appropriate symbols or add/modify a symbols directory that is writable
INFO     volatility3.framework.symbols.windows.pdbutil: The symbols can be downloaded later using pdbconv.py -p ntkrnlmp.pdb -g 3844DBB920174967BE7AA4A2C20430FA2
INFO     volatility3.framework.automagic: Running automagic: SymbolFinder    
INFO     volatility3.framework.automagic: Running automagic: KernelModule

Unsatisfied requirement plugins.Info.kernel.symbol_table_name: 

A symbol table requirement was not fulfilled.  Please verify that:
        The associated translation layer requirement was fulfilled
        You have the correct symbol file for the requirement
        The symbol file is under the correct directory or zip file
        The symbol file is named appropriately or contains the correct banner

Unable to validate the plugin requirements: ['plugins.Info.kernel.symbol_table_name']
```

The problems with the symbol files are solved by running the following command. This command uses Volatility's pdbconv.py 
script to generate the required symbol file (ntkrnlmp.pdb) based on the GUID (3844DBB920174967BE7AA4A2C20430FA2).

```Bash
sudo python3 /opt/volatility3/volatility3/framework/symbols/windows/pdbconv.py -p ntkrnlmp.pdb -g 3844DBB920174967BE7AA4A2C20430FA2
```

After the correct symbol files have been generated and the authorisation issues have been resolved, the command 
'vol -f MemoryDump_Lab1.raw -s /opt/volatility3/volatility3/framework/symbols/windows/ windows.info' is successfully executed. 
This command specifies the path to the correct symbol files and enables Volatility 3 to successfully analyse the memory 
dump and provide detailed information about the Windows system.

```Bash
vol -f MemoryDump_Lab1.raw -s /opt/volatility3/volatility3/framework/symbols/windows/ windows.info

Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished                        
Variable        Value

Kernel Base     0xf8000261f000
DTB     0x187000
Symbols file:///opt/volatility3/volatility3/framework/symbols/windows/3844DBB920174967BE7AA4A2C20430FA-2.json.xz
Is64Bit True
IsPAE   False
layer_name      0 WindowsIntel32e
memory_layer    1 FileLayer
KdDebuggerDataBlock     0xf800028100a0
NTBuildLab      7601.17514.amd64fre.win7sp1_rtm.
CSDVersion      1
KdVersionBlock  0xf80002810068
Major/Minor     15.7601
MachineType     34404
KeNumberProcessors      1
SystemTime      2019-12-11 14:38:00
NtSystemRoot    C:\Windows
NtProductType   NtProductWinNt
NtMajorVersion  6
NtMinorVersion  1
PE MajorOperatingSystemVersion  6
PE MinorOperatingSystemVersion  1
PE Machine      34404
PE TimeDateStamp        Sat Nov 20 09:30:02 2010
```

We could have gone into every single process, but for the purposes of this article we will only focus on three processes 
that are necessary for the CTF game. Analysing the memory dump with the aforementioned command in Volatility reveals that 
"cmd", "mspaint" and "WinRAR" were active processes at the time of the memory dump. The presence of "cmd" indicates the 
use of the command line, possibly for system administration or specific tasks. "mspaint" as an active process indicates 
image editing or graphical activities, while "WinRAR" indicates use for file compression or archiving.

```
vol -f MemoryDump_Lab1.raw -s /opt/volatility3/volatility3/framework/symbols/windows/ windows.pslist

Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished                        
PID     PPID    ImageFileName   Offset(V)       Threads Handles SessionId       Wow64   CreateTime      ExitTime        File output

4       0       System  0xfa8000ca0040  80      570     N/A     False   2019-12-11 13:41:25.000000      N/A     Disabled
248     4       smss.exe        0xfa800148f040  3       37      N/A     False   2019-12-11 13:41:25.000000      N/A     Disabled
320     312     csrss.exe       0xfa800154f740  9       457     0       False   2019-12-11 13:41:32.000000      N/A     Disabled
368     360     csrss.exe       0xfa8000ca81e0  7       199     1       False   2019-12-11 13:41:33.000000      N/A     Disabled
376     248     psxss.exe       0xfa8001c45060  18      786     0       False   2019-12-11 13:41:33.000000      N/A     Disabled
416     360     winlogon.exe    0xfa8001c5f060  4       118     1       False   2019-12-11 13:41:34.000000      N/A     Disabled
424     312     wininit.exe     0xfa8001c5f630  3       75      0       False   2019-12-11 13:41:34.000000      N/A     Disabled
484     424     services.exe    0xfa8001c98530  13      219     0       False   2019-12-11 13:41:35.000000      N/A     Disabled
492     424     lsass.exe       0xfa8001ca0580  9       764     0       False   2019-12-11 13:41:35.000000      N/A     Disabled
500     424     lsm.exe 0xfa8001ca4b30  11      185     0       False   2019-12-11 13:41:35.000000      N/A     Disabled
588     484     svchost.exe     0xfa8001cf4b30  11      358     0       False   2019-12-11 13:41:39.000000      N/A     Disabled
652     484     VBoxService.ex  0xfa8001d327c0  13      137     0       False   2019-12-11 13:41:40.000000      N/A     Disabled
720     484     svchost.exe     0xfa8001d49b30  8       279     0       False   2019-12-11 13:41:41.000000      N/A     Disabled
816     484     svchost.exe     0xfa8001d8c420  23      569     0       False   2019-12-11 13:41:42.000000      N/A     Disabled
852     484     svchost.exe     0xfa8001da5b30  28      542     0       False   2019-12-11 13:41:43.000000      N/A     Disabled
876     484     svchost.exe     0xfa8001da96c0  32      941     0       False   2019-12-11 13:41:43.000000      N/A     Disabled
472     484     svchost.exe     0xfa8001e1bb30  19      476     0       False   2019-12-11 13:41:47.000000      N/A     Disabled
1044    484     svchost.exe     0xfa8001e50b30  14      366     0       False   2019-12-11 13:41:48.000000      N/A     Disabled
1208    484     spoolsv.exe     0xfa8001eba230  13      282     0       False   2019-12-11 13:41:51.000000      N/A     Disabled
1248    484     svchost.exe     0xfa8001eda060  19      313     0       False   2019-12-11 13:41:52.000000      N/A     Disabled
1372    484     svchost.exe     0xfa8001f58890  22      295     0       False   2019-12-11 13:41:54.000000      N/A     Disabled
1416    484     TCPSVCS.EXE     0xfa8001f91b30  4       97      0       False   2019-12-11 13:41:55.000000      N/A     Disabled
1508    484     sppsvc.exe      0xfa8000d3c400  4       141     0       False   2019-12-11 14:16:06.000000      N/A     Disabled
948     484     svchost.exe     0xfa8001c38580  13      322     0       False   2019-12-11 14:16:07.000000      N/A     Disabled
1856    484     wmpnetwk.exe    0xfa8002170630  16      451     0       False   2019-12-11 14:16:08.000000      N/A     Disabled
480     484     SearchIndexer.  0xfa8001d376f0  14      701     0       False   2019-12-11 14:16:09.000000      N/A     Disabled
296     484     taskhost.exe    0xfa8001eb47f0  8       151     1       False   2019-12-11 14:32:24.000000      N/A     Disabled
1988    852     dwm.exe 0xfa8001dfa910  5       72      1       False   2019-12-11 14:32:25.000000      N/A     Disabled
604     2016    explorer.exe    0xfa8002046960  33      927     1       False   2019-12-11 14:32:25.000000      N/A     Disabled
1844    604     VBoxTray.exe    0xfa80021c75d0  11      140     1       False   2019-12-11 14:32:35.000000      N/A     Disabled
2064    816     audiodg.exe     0xfa80021da060  6       131     0       False   2019-12-11 14:32:37.000000      N/A     Disabled
2368    484     svchost.exe     0xfa80022199e0  9       365     0       False   2019-12-11 14:32:51.000000      N/A     Disabled
1984    604     cmd.exe 0xfa8002222780  1       21      1       False   2019-12-11 14:34:54.000000      N/A     Disabled
2692    368     conhost.exe     0xfa8002227140  2       50      1       False   2019-12-11 14:34:54.000000      N/A     Disabled
2424    604     mspaint.exe     0xfa80022bab30  6       128     1       False   2019-12-11 14:35:14.000000      N/A     Disabled
2660    484     svchost.exe     0xfa8000eac770  6       100     0       False   2019-12-11 14:35:14.000000      N/A     Disabled
2760    2680    csrss.exe       0xfa8001e68060  7       172     2       False   2019-12-11 14:37:05.000000      N/A     Disabled
2808    2680    winlogon.exe    0xfa8000ecbb30  4       119     2       False   2019-12-11 14:37:05.000000      N/A     Disabled
2908    484     taskhost.exe    0xfa8000f3aab0  9       158     2       False   2019-12-11 14:37:13.000000      N/A     Disabled
3004    852     dwm.exe 0xfa8000f4db30  5       72      2       False   2019-12-11 14:37:14.000000      N/A     Disabled
2504    3000    explorer.exe    0xfa8000f4c670  34      825     2       False   2019-12-11 14:37:14.000000      N/A     Disabled
2304    2504    VBoxTray.exe    0xfa8000f9a4e0  14      144     2       False   2019-12-11 14:37:14.000000      N/A     Disabled
2524    480     SearchProtocol  0xfa8000fff630  7       226     2       False   2019-12-11 14:37:21.000000      N/A     Disabled
1720    480     SearchFilterHo  0xfa8000ecea60  5       90      0       False   2019-12-11 14:37:21.000000      N/A     Disabled
1512    2504    WinRAR.exe      0xfa8001010b30  6       207     2       False   2019-12-11 14:37:23.000000      N/A     Disabled
2868    480     SearchProtocol  0xfa8001020b30  8       279     0       False   2019-12-11 14:37:23.000000      N/A     Disabled
796     604     DumpIt.exe      0xfa8001048060  2       45      1       True    2019-12-11 14:37:54.000000      N/A     Disabled
2260    368     conhost.exe     0xfa800104a780  2       50      1       False   2019-12-11 14:37:54.000000      N/A     Disabled
```

After identifying the active processes in the memory image, the next step is to analyse the command line parameters of 
these processes. This provides a deeper insight into the exact actions or commands that were executed in the command line. 
Such an analysis can be informative in order to understand the specific activities within the "cmd" process. It also 
provides information about any parameters or file paths used in "mspaint" or "WinRAR". These additional details are 
particularly valuable for forensic analyses as they help to paint a more accurate picture of user interactions and system 
usage at the time of the memory dump.

```Bash
vol -f MemoryDump_Lab1.raw -s /opt/volatility3/volatility3/framework/symbols/windows/ windows.cmdline        

Volatility 3 Framework 2.4.1
Progress:  100.00               PDB scanning finished                        
PID     Process Args

4       System  Required memory at 0x20 is not valid (process exited?)
248     smss.exe        \SystemRoot\System32\smss.exe
320     csrss.exe       %SystemRoot%\system32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,20480,768 Windows=On SubSystemType=Windows ServerDll=basesrv,1 ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=winsrv:ConServerDllInitialization,2 ServerDll=sxssrv,4 ProfileControl=Off MaxRequestThreads=16
368     csrss.exe       %SystemRoot%\system32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,20480,768 Windows=On SubSystemType=Windows ServerDll=basesrv,1 ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=winsrv:ConServerDllInitialization,2 ServerDll=sxssrv,4 ProfileControl=Off MaxRequestThreads=16
376     psxss.exe       %SystemRoot%\system32\psxss.exe
416     winlogon.exe    winlogon.exe
424     wininit.exe     wininit.exe
484     services.exe    C:\Windows\system32\services.exe
492     lsass.exe       C:\Windows\system32\lsass.exe
500     lsm.exe C:\Windows\system32\lsm.exe
588     svchost.exe     C:\Windows\system32\svchost.exe -k DcomLaunch
652     VBoxService.ex  C:\Windows\System32\VBoxService.exe
720     svchost.exe     C:\Windows\system32\svchost.exe -k RPCSS
816     svchost.exe     C:\Windows\System32\svchost.exe -k LocalServiceNetworkRestricted
852     svchost.exe     C:\Windows\System32\svchost.exe -k LocalSystemNetworkRestricted
876     svchost.exe     C:\Windows\system32\svchost.exe -k netsvcs
472     svchost.exe     C:\Windows\system32\svchost.exe -k LocalService
1044    svchost.exe     C:\Windows\system32\svchost.exe -k NetworkService
1208    spoolsv.exe     C:\Windows\System32\spoolsv.exe
1248    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceNoNetwork
1372    svchost.exe     C:\Windows\system32\svchost.exe -k LocalServiceAndNoImpersonation
1416    TCPSVCS.EXE     C:\Windows\System32\tcpsvcs.exe
1508    sppsvc.exe      C:\Windows\system32\sppsvc.exe
948     svchost.exe     C:\Windows\System32\svchost.exe -k secsvcs
1856    wmpnetwk.exe    "C:\Program Files\Windows Media Player\wmpnetwk.exe"
480     SearchIndexer.  C:\Windows\system32\SearchIndexer.exe /Embedding
296     taskhost.exe    "taskhost.exe"
1988    dwm.exe "C:\Windows\system32\Dwm.exe"
604     explorer.exe    C:\Windows\Explorer.EXE
1844    VBoxTray.exe    "C:\Windows\System32\VBoxTray.exe" 
2064    audiodg.exe     C:\Windows\system32\AUDIODG.EXE 0x20c
2368    svchost.exe     C:\Windows\System32\svchost.exe -k LocalServicePeerNet
1984    cmd.exe "C:\Windows\system32\cmd.exe" 
2692    conhost.exe     \??\C:\Windows\system32\conhost.exe
2424    mspaint.exe     "C:\Windows\system32\mspaint.exe" 
2660    svchost.exe     C:\Windows\system32\svchost.exe -k imgsvc
2760    csrss.exe       %SystemRoot%\system32\csrss.exe ObjectDirectory=\Windows SharedSection=1024,20480,768 Windows=On SubSystemType=Windows ServerDll=basesrv,1 ServerDll=winsrv:UserServerDllInitialization,3 ServerDll=winsrv:ConServerDllInitialization,2 ServerDll=sxssrv,4 ProfileControl=Off MaxRequestThreads=16
2808    winlogon.exe    winlogon.exe
2908    taskhost.exe    "taskhost.exe"
3004    dwm.exe "C:\Windows\system32\Dwm.exe"
2504    explorer.exe    C:\Windows\Explorer.EXE
2304    VBoxTray.exe    "C:\Windows\System32\VBoxTray.exe" 
2524    SearchProtocol  "C:\Windows\system32\SearchProtocolHost.exe" Global\UsGthrFltPipeMssGthrPipe_S-1-5-21-3073570648-3149397540-2269648332-10032_ Global\UsGthrCtrlFltPipeMssGthrPipe_S-1-5-21-3073570648-3149397540-2269648332-10032 1 -2147483646 "Software\Microsoft\Windows Search" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT; MS Search 4.0 Robot)" "C:\ProgramData\Microsoft\Search\Data\Temp\usgthrsvc" "DownLevelDaemon"  "1"
1720    SearchFilterHo  "C:\Windows\system32\SearchFilterHost.exe" 0 508 512 520 65536 516 
1512    WinRAR.exe      "C:\Program Files\WinRAR\WinRAR.exe" "C:\Users\Alissa Simpson\Documents\Important.rar"
2868    SearchProtocol  "C:\Windows\system32\SearchProtocolHost.exe" Global\UsGthrFltPipeMssGthrPipe3_ Global\UsGthrCtrlFltPipeMssGthrPipe3 1 -2147483646 "Software\Microsoft\Windows Search" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT; MS Search 4.0 Robot)" "C:\ProgramData\Microsoft\Search\Data\Temp\usgthrsvc" "DownLevelDaemon" 
796     DumpIt.exe      "C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe" 
2260    conhost.exe     \??\C:\Windows\system32\conhost.exe
```

After running the cmdline plugin, we can see that WinRAR.exe gives us a hint. An important archive has probably been created:

```
1512    WinRAR.exe      "C:\Program Files\WinRAR\WinRAR.exe" "C:\Users\Alissa Simpson\Documents\Important.rar"
```

> **HINT:** To obtain a functional command prompt analysis, Volatility2 was employed due to the absence of this feature in the current 
version, Volatility3. This limitation in Volatility3 was addressed in an issue opened in 2022. For practical application, 
the standalone version of Volatility2, specifically designed for Linux, was utilized and renamed to **vol2** for convenience.
Further details about this specific issue in Volatility3 can be found at [Volatility3 Issue #816](https://github.com/volatilityfoundation/volatility3/issues/816).

To get possible hints about what happened in the command line, we can use the consoles-plugin:

In the given output we see an analysis of the console activity on a Windows system. Of particular interest is the section 
relating to the process conhost.exe with process ID 2692, which is attached to cmd.exe (PID 1984). In this console session, 
the command St4G3$1 was executed, which could indicate a specific user interaction or script. The title of the console 
shows "C:\Windows\system32\cmd.exe - St4G3$1", which indicates that the command "St4G3$1" was executed directly in the 
command line.

```Bash
./vol2 -f MemoryDump_Lab1.raw --profile=Win7SP1x64 consoles

Volatility Foundation Volatility Framework 2.6
**************************************************
ConsoleProcess: conhost.exe Pid: 2692
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: %SystemRoot%\system32\cmd.exe
Title: C:\Windows\system32\cmd.exe - St4G3$1
AttachedProcess: cmd.exe Pid: 1984 Handle: 0x60
----
CommandHistory: 0x1fe9c0 Application: cmd.exe Flags: Allocated, Reset
CommandCount: 1 LastAdded: 0 LastDisplayed: 0
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
Cmd #0 at 0x1de3c0: St4G3$1
----
Screen 0x1e0f70 X:80 Y:300
Dump:
Microsoft Windows [Version 6.1.7601]                                            
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.                 
                                                                                
C:\Users\SmartNet>St4G3$1                                                       
ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=                                        
Press any key to continue . . .                                                 
**************************************************
ConsoleProcess: conhost.exe Pid: 2260
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
Title: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
AttachedProcess: DumpIt.exe Pid: 796 Handle: 0x60
----
CommandHistory: 0x38ea90 Application: DumpIt.exe Flags: Allocated
CommandCount: 0 LastAdded: -1 LastDisplayed: -1
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
----
Screen 0x371050 X:80 Y:300
Dump:
  DumpIt - v1.3.2.20110401 - One click memory memory dumper                     
  Copyright (c) 2007 - 2011, Matthieu Suiche <http://www.msuiche.net>           
  Copyright (c) 2010 - 2011, MoonSols <http://www.moonsols.com>                 
                                                                                
                                                                                
    Address space size:        1073676288 bytes (   1023 Mb)                    
    Free space size:          24185389056 bytes (  23064 Mb)                    
                                                                                
    * Destination = \??\C:\Users\SmartNet\Downloads\DumpIt\SMARTNET-PC-20191211-143755.raw                                                                      
                                                                                
    --> Are you sure you want to continue? [y/n] y                              
    + Processing...                                 
```

In the given output we see an analysis of the console activity on a Windows system. Of particular interest is the 
section relating to the process conhost.exe with process ID 2692, which is attached to cmd.exe (PID 1984). In this 
console session, the command St4G3$1 was executed, which could indicate a specific user interaction or script. The title 
of the console shows "C:\Windows\system32\cmd.exe - St4G3$1", which indicates that the command "St4G3$1" was executed 
directly in the command line.

```Bash
C:\Users\SmartNet>St4G3$1                                                       
ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=  
```

If you come across a string that consists of a mixture of upper and lower case letters and numbers, this could be an 
indication of base64 encoding. An additional feature is often the presence of one or two '=' characters at the end, 
which act as padding to bring the length to a multiple of 4. This combination of characters and the characteristic 
length are strong indicators of a base64 encoding, especially if it appears in a context where data transfers or the 
storage of binary data as text are common.

Therefore, let's try to decode the string with the following command:

```Bash
$ echo ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0= | base64 -d

flag{th1s_1s_th3_1st_st4g3!!}
```

The string was successfully decoded with the command "base64 -d", and the result is: "flag{th1s_1s_th3_1st_st4g3!!!}". 
This confirms that it was indeed a base64-encoded message and the decoding resulted in the plain text 
"flag{th1s_1s_th3_1st_st4g3!!!}".


Let's move on to the **mspaint.exe** process. Since this process shows that MS Paint was used, we may be able to 
extract data that could provide a clue:

```Bash
./vol2 -f MemoryDump_Lab1.raw --profile=Win7SP1x64 memdump -p 2424 -D . 

Volatility Foundation Volatility Framework 2.6
************************************************************************
Writing mspaint.exe [  2424] to 2424.dmp
```

After a memory image has been created for the process with the PID 2424, we rename the file "2424.dmp"
to "2424.data" and try to open it in GIMP. We succeed in obtaining a distorted graphic that is displayed with RGB-Alpha,
height, width and offset. This can be seen in the following screenshot:

![dump.data](https://github.com/tmwProjects/Blog/blob/master/content/grafics/volatility_gimp_lab1.png?raw=true)

If the graphic is rotated and inverted, the following message can be recognised:

```Bach
flag{G00d_Boy_good_girL}
```

Now the last process we want to look at is "WinRAR.exe". We had previously determined that there was an "Important" 
archive that was created, where we want to find out what is contained in this archive:

```Bash
./vol2 -f MemoryDump_Lab1.raw --profile Win7SP1x64 cmdline | grep WinRAR

Volatility Foundation Volatility Framework 2.6
WinRAR.exe pid:   1512
Command line : "C:\Program Files\WinRAR\WinRAR.exe" "C:\Users\Alissa Simpson\Documents\Important.rar"
```


We see the same archive "Important.rar" with three different memory addresses in the filescan plugin. Choosing the last 
storage address makes sense, as it usually represents the latest, unchanged version and enables efficient analysis. 
However, this decision depends on the analysis requirements.

```Bash
./vol2 -f MemoryDump_Lab1.raw --profile Win7SP1x64 filescan | grep Important.rar

Volatility Foundation Volatility Framework 2.6
0x000000003fa3ebc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
0x000000003fac3bc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
0x000000003fb48bc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
```

We therefore want to isolate the last one and create a dump:

```Bash
./vol2 -f MemoryDump_Lab1.raw --profile Win7SP1x64 dumpfiles -Q 0x000000003fa3ebc0 -D .

Volatility Foundation Volatility Framework 2.6
DataSectionObject 0x3fa3ebc0   None   \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
```

Now we rename the dump that was created to "Important.rar":

```Bash
mv file.None.0xfffffa8001034450.dat Important.rar
```

Then we want to unpack the isolated archive to find out what is contained and how we can see it, the archive is password 
protected and has a comment. The password is expected as an NTLM hash (in capital letters) of the password  for Alissa's 
user account.

```Bash
unrar e Important.rar

UNRAR 7.00 beta 3 freeware      Copyright (c) 1993-2023 Alexander Roshal

Archive comment:
Password is NTLM hash(in uppercase) of Alissa's account passwd.


Extracting from Important.rar

Enter password (will not be echoed) for flag3.png: 
```

Mit dem hashdump-plugin versuchen wir, Passwort-Hashes aus dem Memory dump zu erhalten.
Wir haben vorab gesehen, dass ein NTLM hash gesucht wird, die zu einem bestimmten Benutzerkonto gehört.

```Bash
./vol2 -f MemoryDump_Lab1.raw --profile Win7SP1x64 hashdump        

Volatility Foundation Volatility Framework 2.6
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SmartNet:1001:aad3b435b51404eeaad3b435b51404ee:4943abb39473a6f32c11301f4987e7e0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f0fc3d257814e08fea06e63c5762ebd5:::
Alissa Simpson:1003:aad3b435b51404eeaad3b435b51404ee:f4ff64c8baac57d22f22edc681055ba6:::
```


The line "Alissa Simpson:1003:aad3b435b51404eeaad3b435b51404ee:f4ff64c8baac57d22f22edc681055ba6:::" contains information about the user account 
about the user account "Alissa Simpson" on the system. The "1003" is the user ID that uniquely identifies the account. 
The section "aad3b435b51404eeaad3b435b51404ee" represents the NTLMv2 hash, which is the encrypted password of the user. 
user's encrypted password. It is worth noting that the LM hash (LAN Manager hash) is not used, as indicated by the empty 
field ":::". The LM hash is an outdated and less secure form of password representation, while the 
NTLMv2 hash is favoured in modern Windows systems to increase security.


```Bash
Alissa Simpson:1003:aad3b435b51404eeaad3b435b51404ee:f4ff64c8baac57d22f22edc681055ba6:::
```

As the password is expected to be an NTLM hash in capital letters, we still need to modify the hash:

```Bash
F4FF64C8BAAC57D22F22EDC681055BA6
```

We can now use this hash as the password for the extraction:

```Bash
unrar e Important.rar

UNRAR 7.00 beta 3 freeware      Copyright (c) 1993-2023 Alexander Roshal

Archive comment:
Password is NTLM hash(in uppercase) of Alissa's account passwd.


Extracting from Important.rar

Enter password (will not be echoed) for flag3.png: 
```

After decompressing the file, we get an image with the flag.

![Third_flag](https://github.com/tmwProjects/Blog/blob/master/content/grafics/flag3.png?raw=true)


[**[Back to content]**](#contents)

***

### Additional plugins

For extended analysis options in the area of memory forensics, the Volatility Framework offers a series of 
plugins. The [Community3 page of the Volatility Foundation](https://github.com/volatilityfoundation/community3) and the [Community page for Volatility 2](https://github.com/volatilityfoundation/community) provide a variety of plugins developed by the user community. 
of plugins that have been developed by the user community. The [Criminalip-Volatility3-Plugins](https://github.com/criminalip/Criminalip-Volatility3-Plugins) are a special addition that 
special addition that combines Volatility 3 with the Criminal IP CTI search engine to analyse suspicious IPs and domains in memory dumps. 
IPs and domains in memory dumps. These plugins open up new dimensions of forensic analysis.

[**[Back to content]**](#contents)

***

### Bottom line

Mastering volatility as a programme is just the beginning. The art of analysing memory for forensic investigations lies 
in the interpretation of the extracted data. It requires a deep understanding of operating system internals, knowledge 
of malware behaviours and anomaly detection skills. Experts must not only collect data, but also make connections between 
them to identify potential threats, evidence of unauthorised activity or malware artefacts. The ability to draw the right 
conclusions from the data obtained is critical.

[**[Back to content]**](#contents)

***

## References

[1] **Volatility2**: The Volatility Foundation - A comprehensive open-source framework for memory forensics, specializing in the analysis of Windows, Linux, and MacOS memory images. It offers a wide range of features for detailed examination of memory images. https://github.com/volatilityfoundation/volatility

[2] **Volatility3**: The Volatility Foundation - The newer version of the Volatility framework, featuring improved architecture and performance. Specifically designed for more efficient analysis of memory images. https://github.com/volatilityfoundation/volatility3

[3] **awesome-memory-forensics**: Digitalisx - A curated list of excellent resources for Memory Forensics, ideal for DFIR (Digital Forensics and Incident Response). https://github.com/Digitalisx/awesome-memory-forensics

[4] **MemLabs**: P. Abhiram Kumar, Ritam Dey - Educational labs styled like Capture The Flag challenges for individuals interested in Memory Forensics. https://github.com/stuxnet999/MemLabs

[5] **Volatility2 Framework Wiki**: The Volatility Foundation - A Wiki page for the Volatility memory forensics framework, offering guides, plugin information, and case studies. https://github.com/volatilityfoundation/volatility/wiki

[6] **Volatility 3 Documentation**: The Volatility Foundation - Official documentation for Volatility 3, covering installation, user instructions, and plugin details. https://volatility3.readthedocs.io/en/stable/index.html

[7] **Volatility Foundation Community3**: A collection of additional plugins for Volatility 3, developed by the community to enhance memory forensics. Available at: https://github.com/volatilityfoundation/community3

[8] **Volatility Foundation Community**: Offers community-contributed plugins for Volatility 2, expanding its forensic analysis capabilities. Available at: https://github.com/volatilityfoundation/community

[9] **Criminalip Volatility3 Plugins**: Integrates Volatility 3 with the Criminal IP CTI search engine for analyzing potential malicious IPs and domains in memory dumps. Available at: https://github.com/criminalip/Criminalip-Volatility3-Plugins

[10] **The Art of Memory Forensics**:  M. Hale Ligh, A. Case, J. Levy, A. Walters - Detecting Malware and Threats in Windows, Linux, and Mac Memory. (2014) [Download book](https://www.wiley.com/en-us/exportProduct/pdf/9781118824993?__cf_chl_tk=EtztUDDo7H5k1hZd4RPB0yQNK9CjPDeJ7hbOOzpUG7E-1703948296-0-gaNycGzNDHs)

[11] **LiME - Linux Memory Extractor**: A tool for memory dump extraction in Linux environments. Available on GitHub. [Access Source](https://github.com/504ensicsLabs/LiME)

[12] **memdump**: A tool for memory dump extraction, included in Kali Linux. [Access Source](https://www.kali.org/tools/memdump/)

[13] **pcileech - Direct Memory Access (DMA) Attack Software**: A tool for DMA attacks and memory extraction, developed by Ulf Frisk. Available on GitHub. [Access Source](https://github.com/ufrisk/pcileech)

[14] **Generating a Complete Memory Dump in Windows 10**: A guide by Bitdefender on how to create a full memory dump on a Windows 10 system. [Access Source](https://www.bitdefender.com/business/support/en/77209-90385-generate-a-complete-memory-dump-on-windows-10.html)

[15] **ProcDump - Creating Dump Files in Windows 10**: A guide by Windows Central on using the ProcDump tool to create memory dumps in Windows 10. [Access Source](https://www.windowscentral.com/how-use-procdump-create-dump-files-windows-10)

[**[Back to content]**](#contents)

***

### License

**CC BY-NC-SA 4.0 Licence**

With this licence, you may use, modify and share the work as long as you credit the original author. However, you may 
not use it for commercial purposes, i.e. you may not make money from it. And if you make changes and share the new work, 
it must be shared under the same conditions.

[**[Back to content]**](#contents)