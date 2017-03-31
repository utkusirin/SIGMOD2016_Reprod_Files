================================================================================
Readme for reproducibility submission of paper ID 375, titled "Micro-architectural Analysis of In-memory OLTP".

A) Source code info

Repository: https://github.com/utkusirin/SIGMOD2016_Reprod_Files/archive/master.zip

Programming Language: C++ and Java.

Compiler Info:

$ gcc --version
gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-17)
Copyright (C) 2010 Free Software Foundation, Inc.

Note that gcc version 4.8 and above cause error in our experiments.

$ java -version
java version "1.7.0_111"
OpenJDK Runtime Environment (rhel-2.6.7.2.el6_8-x86_64 u111-b01)
OpenJDK 64-Bit Server VM (build 24.111-b01, mixed mode)

OS Info:

Linux: Red Hat Enterprise Linux Server release 6.8 (Santiago), with Linux kernel version 2.6.32-642.4.2.el6.x86_64.
Windows: Windows 7 Enterprise.

Packages/Libraries Needed:

The Linux environment needs to have Intel Vtune version 2015 to be installed. Note that VTune 2016 and 2017 produces an error in our experiments.

The Windows environment needs to have MS Office 2010 to be installed.

B) Datasets info

In our experiments, data is re-populated every time for every experiment.

C) Hardware Info

C1) Processor: Intel(R) Xeon(R) CPU E5-2640 v2 (Ivy Bridge) with two sockets and eight cores per socket, operating at 2GHz. HyperThreading is disabled, TurboBoost is enabled.
C2) Caches: Three levels of caches. L1 is seperated for instruction and data, i.e., L1I and L1D, each with 32KB of size. L2 is unified for instruction and data with 256KB of size. L3 is unified for instruction and data with 20MB of size. L1I, L1D and L2 are per core, whereas L3 is per socket, i.e., shared among all the cores on the same socket.
C3) Memory: 1600MHz, 256GB
C4) Secondary Storage: 250GB of disk space is needed.

D) Experimentation Info

All the experiments are conducted in the Linux environments. Then, experimental results are transferred to the Windows environment to reproduce the graphs in the provided MS Office Excel spreadsheets.

Throughout this section "main" folder represents the folder whose link is below, and "/path_to_main/" represents the absolute path to the main folder.

D1) Download the main folder, and extract it:

$ https://github.com/utkusirin/SIGMOD2016_Reprod_Files/archive/master.zip

$ unzip master.zip

D2) PATH configurations: The Linux system needs to have VTune and VoltDB on the system PATH. For that, add the following lines to the ~/.bashrc:

source /path_to_vtune2015/amplxe-vars.sh
export PATH=/path_to_main/voltdb/bin/:$PATH

Moreover, make sure ~/.bashrc is run when you login to the system. For that, add the following lines to the ~/.profile:
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi

To activate the configured ~/.bashrc, log out and log in back to the system.

D3) Permissions: The Linux system needs certain permissions to run VTune. Normally, the ~/.bashrc configurations explained in Section D1 should already grant the required permission for VTune. However, this permission grant fails in certain cases. For that, run the following steps

sudo su
echo 0 > /proc/sys/kernel/kptr_restrict
echo -1 > /proc/sys/kernel/perf_event_paranoid
exit

D4) VTune analysis type: Before running the experiments, the used VTune analysis type needs to be created. To do that, we use VTune GUI. To open VTune GUI, we need to connect to the server that used to run the experiments by ssh -X:

$ ssh -X username@server

Then, type the following command to open the VTune GUI:

$ amplxe-gui &

Then, follow the steps below:

D4.1) Open the VTune project under the submission folder: mbench-analysis.amplxeproj. To do that, click on "Open Project" in VTune GUI (see VTune_Helper/Figure1).

D4.2) Click on "New Analysis" in VTune GUI (see VTune_Helper/Figure2). This will open a new sub-window inside VTune GUI (see VTune_Helper/Figure3).

D4.3) In the new sub-window, right-click on "Custom Analysis", and click on "New Hardware Event-based Sampling Analysis" (see VTune_Helper/Figure4).

D4.4) In the opened window, firstly type the command line name of the analysis as "utku-analysis" to the text box titled "Command line name" placed at the bottom of the window (see VTune_Helper/Figure5). Then, in the same window, click on "Add Event", and add the following events:

BR_INST_EXEC.ALL_BRANCHES
BR_MISP_EXEC.ALL_BRANCHES
CPU_CLK_UNHALTED.THREAD
ICACHE.MISSES
INST_RETIRED.ANY
L2_RQSTS.CODE_RD_MISS
MEM_LOAD_UOPS_LLC_HIT_RETIRED.XSNP_HIT
MEM_LOAD_UOPS_LLC_HIT_RETIRED.XSNP_HITM
MEM_LOAD_UOPS_LLC_HIT_RETIRED.XSNP_MISS
MEM_LOAD_UOPS_LLC_HIT_RETIRED.XSNP_NONE
MEM_LOAD_UOPS_LLC_MISS_RETIRED.LOCAL_DRAM
MEM_LOAD_UOPS_LLC_MISS_RETIRED.REMOTE_DRAM
MEM_LOAD_UOPS_RETIRED.L2_HIT
MEM_LOAD_UOPS_RETIRED.LLC_MISS
RESOURCE_STALLS.ANY
UOPS_RETIRED.ALL
UOPS_RETIRED.CORE_STALL_CYCLES
UOPS_RETIRED.TOTAL_CYCLES

To add an event, you should click on the check box placed at the right-hand-side of the event (see VTune_Helper/Figure6). After adding all the events, click OK on all the opened sub-windows, and close the VTune GUI.

D5) Running the experiments: To run the experiments, do the following:

$ cd /path_to_main/scripts/
$ ./master.sh

This step conducts all the sets of experiments. It takes around 60hours (~2.5 days) in our machine. Do not run anything else on the server while conducting the experiments.

Note that our paper profiles five OLTP systems, two of which are open-source, three of which are commercial. The open-source systems are Shore-MT and VoltDB, and the commercial/closed source systems are HyPer, DBMS M and DBMS D. As we are not allowed to reveal the executables and the names of the commercial systems, in our reproducibility submission we focus only on the open-source systems, i.e., Shore-MT and VoltDB.

Note also that we tried to use ReproZip as suggested, however, ReproZip could not trace our experiments due to VTune's internal process generation mechanism that conflicts with reprozips trace mechanism. This is suggested by the following outputted log from reprozip:

[REPROZIP] 20:04:59.120 INFO: [12502] successfully exec'd /opt/intel/vtune_amplifier_xe_2015.1.0.367959/bin32/amplxe-runss
[REPROZIP] 20:04:59.185 CRITICAL: [12502] process created new process 12503 but we didn't see syscall entry
[REPROZIP] 20:04:59.185 CRITICAL: cleaning up, 16 processes to kill...

showing that we get the error when vtune creates a new process. The first line represents the pid of a vtune process (12502). The second line shows that we get the error when the vtune process creates a new process with pid of 12503.

D6) Extract the performance counter numbers: To extract the VTune performance counter numbers, do the following:

$ cd /path_to_main/scripts
$ ./get_counter_and_thpt_values.sh

D7) Reproducing the graphs: To reproduce the graphs, we need to move from the Linux environment to the Windows enviroment. For that, we zip the spreadsheets folder including the final results, and copy it to the Windows environment.

To zip the spreadsheets folder, do the following:

$ cd /path_to_main/main/
$ zip -r spreadsheets.zip spreadsheets/

To move the spreadsheets.zip to the Windows environment, one can use any way he/she would prefer. We use Cygwin, a Linux emulator in Windows environments. We open a Cygwin shell in the Windows environment, and do the following:

$ scp username@server:/path_to_main/main/spreadsheets.zip .

This transfers the spreadsheets.zip file to the Windows environment. Then, unzip the spreadsheets.zip folder, and open the spreadsheets folder in the Windows environment using related GUIs.

We can now reproduce the figures. For every spreadsheet follow the steps below:
    D7.1) Open the 1st sheet, and click on the "Import VTune Data" and "Import Thpt Data" buttons.
    D7.2) Repeat step D7.1 for all the sheets except the last sheet (the "all results" sheet)
    D7.3) Open the "all results" sheet, and figures are ready to compare with the published figures. Figure numbers are the titles of every figure.

Note that the modules_breakdown.xlsm spreadsheet has only a single sheet. Simply use the "Import Brkdwn Data" button to load the performance counter numbers and reproduce the related figure.

================================================================================

Dear Reproducibility Committee,

Please find attached the following information with regards to my reproducibility submission:

Paper ID: 375

Paper Title: Micro-architectural Analysis of In-memory OLTP

Corresponding Author Name: Utku Sirin

Corresponding Author Email: utku.sirin@epfl.ch

Url with the source code: https://github.com/utkusirin/SIGMOD2016_Reprod_Files/archive/master.zip

Url of the readme: https://github.com/utkusirin/SIGMOD2016_Reprod_Files/blob/master/README.md

Best,
Authors of Paper #375, SIGMOD 2016
