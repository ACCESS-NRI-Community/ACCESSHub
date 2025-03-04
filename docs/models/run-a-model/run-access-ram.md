{% set model = "ACCESS-RAM3" %}
{% set ancillary_model = "ACCESS-RAS" %}
{% set regional_model = "ACCESS-RNS" %}
{% set ras_id = "u-dg767" %}
{% set rns_id = "u-dg768" %}
{% set mosrs_config_ras = "https://code.metoffice.gov.uk/trac/roses-u/browser/d/g/7/6/7/trunk" %}
{% set mosrs_config_rns = "https://code.metoffice.gov.uk/trac/roses-u/browser/d/g/7/6/8/trunk" %}
[PBS job]: https://opus.nci.org.au/display/Help/4.+PBS+Jobs
[model components]: /models/configurations/access-ram/#model-components
[model configurations]: /models/configurations/access-ram
[gadi]: https://opus.nci.org.au/display/Help/0.+Welcome+to+Gadi#id-0.WelcometoGadi-Overview

[![Met Office](/assets/met_office_logo.png){: class="icon-before-text  white-background"} {{ ancillary_model }} configuration]({{mosrs_config_ras}}){: class="text-card" style=""}
[![Met Office](/assets/met_office_logo.png){: class="icon-before-text  white-background"} {{ regional_model }} configuration]({{mosrs_config_rns}}){: class="text-card"}

# Run ACCESS-RAM3

!!! info
    {{ model }} is a place-holder name until the final release name is chosen.

## About

{{ model }} is an ACCESS-NRI-supported configuration of the [UK Met Office (UKMO)](https://www.metoffice.gov.uk/) regional nesting suite for high-resolution regional atmosphere modelling.<br>
A description of the model and its components is available in the [ACCESS-RAM3 overview][model configurations].

{{ model }} comprises two suites: a [Regional Ancillary Suite (RAS)](#ras), which generates ancillary files for the domain of interest, and a [Regional Nesting Suite (RNS)](#rns) which runs the regional forecast.

The instructions below outline how to run {{ model }} using ACCESS-NRI's supported configuration, specifically designed to run on the [National Computating Infrastructure (NCI)](https://nci.org.au/about-us/who-we-are) supercomputer [_Gadi_][gadi].

If you are unsure whether {{ model }} is the right choice for your experiment, take a look at the overview of [ACCESS Models](/models).

All {{model}} configurations are open source, licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/?ref=chooser-v1")![CC icon](https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1){: style="height:1em;margin-left:0.2em;vertical-align:text-top;"}![BY icon](https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1){: style="height:1em;margin-left:0.2em;vertical-align:text-top;"} and available on [ACCESS-NRI GitHub]({{github_configs}}).

{{ model }} release notes are available on the ACCESS-Hive Forum and are updated when new releases are made available.
<!-- TODO: Include link to the ACCESS-Hive Forum when the 1.0 release occurs -->


## Prerequisites

- **NCI Account**<br> 
    Before running {{ model }}, you need to [Set Up your NCI Account](/getting_started/set_up_nci_account).

- **_MOSRS_ account**<br>
    The [Met Office Science Repository Service (MOSRS)](https://code.metoffice.gov.uk) is a server run by the UK Met Office (UKMO) to support collaborative development with other partners organisations. MOSRS contains the source code and configurations for some model components in {{ model }} (e.g., the [UM](/models/model_components/atmosphere/#unified-model-um)).<br>
    To apply for a _MOSRS_ account, please contact your [local institutional sponsor](https://opus.nci.org.au/display/DAE/Prerequisites).
    {: id='mosrs-account'}

- **Join NCI projects**<br>
    Join the following projects by requesting membership on their respective NCI project pages:

    - [access](https://my.nci.org.au/mancini/project/access/join)
    - [hr22](https://my.nci.org.au/mancini/project/hr22/join)
    - [ki32](https://my.nci.org.au/mancini/project/ki32/join)
    - [ki32_mosrs](https://my.nci.org.au/mancini/project/ki32_mosrs/join)
    - [rt52](https://my.nci.org.au/mancini/project/rt52/join)
    - [zz93](https://my.nci.org.au/mancini/project/zz93/join)
    - [vk83](https://my.nci.org.au/mancini/project/vk83/join)

    !!! tip
        To request membership for the _ki32_mosrs_ subproject, you need to:
        
        - already be member of the _ki32_ project
        {: style="list-style-type: disc"}
        - have a [MOSRS account](#mosrs-account)
        {: style="list-style-type: disc"}

    For more information on joining specific NCI projects, refer to [How to connect to a project](https://opus.nci.org.au/display/Help/How+to+connect+to+a+project).

- **Connection to an ARE VDI Desktop (optional)**<br>
    To run {{ model }}, start an [Australian Research Environment (ARE) VDI Desktop](https://are.nci.org.au/pun/sys/dashboard/batch_connect/sys/desktop_vnc/ncigadi/session_contexts/new) session.<br>
    If you are not familiar with ARE, check out the [Getting Started on ARE](/getting_started/are) section.

## Quick Start guide

The following *Quick Start* guide is aimed at experienced users wanting to run {{ model }}. For more detailed instructions, please refer to the [Detailed guide](#detailed-guide).

### Required setup for running {{ model }}

- **Start a new [persistent session](https://opus.nci.org.au/display/Help/Persistent+Sessions)**<br> 
    using a *Gadi* login node or an ARE terminal instance:
    ```
    persistent-sessions start <name>
    ```
    
    This will use your default project. Use option `-p` to assign a different project to the persistent session:
    ```
    persistent-sessions start -p <project> <name>
    ```

- **Assign the persistent session to Rose/Cylc workflows**<br>
    Run the following command:
    ```
    echo "<name>.${USER}.<project>.ps.gadi.nci.org.au" > ~/.persistent-sessions/cylc-session
    ```
    substituting `<name>` with the name given to your persistent session, and `<project>` with the project assigned to it.

    !!! tip
        This step should only be done once

- **Rose/Cylc setup**<br>
    To get the required _Rose/Cylc_ setup, run:
    ```
    module use /g/data/hr22/modulefiles
    module load cylc7
    ```

- **MOSRS authentication**<br>
    Authenticate using your MOSRS credentials:
    ```
    mosrs-auth
    ```

### Regional Ancillary Suite (RAS)
1. **Copy the RAS from UKMO**<br>
    Local-only copy: 
    ```
    rosie checkout {{ ras_id }}
    ```
    
    New copy, both local and remote: 
    ```
    rosie copy {{ ras_id }}
    ```

2. **Run the RAS**<br>
    From within the RAS directory:
    ```
    rose suite-run
    ```

### Regional Nesting Suite (RNS)
1. **Copy the RNS from UKMO**<br>
    Local-only copy: 
    ```
    rosie checkout {{ rns_id }}
    ```

    New copy, both local and remote: 
    ```
    rosie copy {{ rns_id }}
    ```

2. **Run the RNS**<br>
    From within the RNS directory:
    ```
    rose suite-run
    ```

!!! tip
    You've completed all steps required to run {{model}}. For more comprehensive instructions, refer to the [Detailed guide](#detailed-guide).

---

## Detailed guide

### Set up an ARE VDI Desktop (optional)
!!! info 
    If you want to skip this step and run {{ model }} from _Gadi_ login node instead, refer directly to the instructions on how to [Set up persistent session](#set-up-persistent-session).

#### Launch ARE VDI Session
Go to the [ARE VDI](https://are.nci.org.au/pun/sys/dashboard/batch_connect/sys/desktop_vnc/ncigadi/session_contexts/new) page and launch a session with the following entries:

- **Walltime (hours)** &rarr; `2`<br>
    This is the amount of time the ARE VDI session will stay active for.<br>
    {{ model }} does not run directly on ARE.<br>
    This means that the ARE VDI session only needs to carry out setup steps as well as starting the run itself. All these tasks can be done within 2 hours.
    
- **Queue** &rarr; `normalbw`
    
- **Compute Size** &rarr; `tiny` (1 CPU)<br>
    As mentioned above, the ARE VDI session is only needed for setup and startup tasks, which can be easily accomplished with 1 CPU.

- **Project** &rarr; a project of which you are a member.<br>
    The project must have allocated _Service Units (SU)_ to run your simulation. Usually, but not always, this corresponds to your `$PROJECT`.<br>
    For more information, refer to [Join relevant NCI projects](/getting_started/set_up_nci_account#join-relevant-nci-projects).

- **Storage** &rarr; `gdata/access+gdata/hr22+gdata/ki32+gdata/rt52+gdata/zz93+gdata/vk83` (minimum)<br>
    This is a list of all project data storage, joined by plus (`+`) signs, needed for the {{ model }} simulation. In ARE, storage locations need to be explicitly defined to access data from within a VDI instance.<br>
    Every {{ model }} simulation can be unique and input data can originate from various sources. Hence, if your simulation requires data stored in project folders other than the ones listed in the minimum storage above, you need to add those projects to the storage path.<br>
    For example, if your {{ model }} simulation requires data stored in `/g/data/gx60` and `/scratch/gx60`, the following should be added to the minimum storage above: `+gdata/gx60+scratch/gx60`
    
Launch the ARE session and, once it starts, click on _Launch VDI Desktop_.

![Launch ARE VDI session example](/assets/run_access_cm/launch_are_vdi.gif){: class="example-img" loading="lazy"}

#### Open the terminal in the VDI Desktop
Once the new tab opens, you will see a Desktop with a few folders on the left.<br>
To open the terminal, click on the black terminal icon at the top of the window. You should now be connected to a _Gadi_ computing node.

![Open ARE VDI terminal example](/assets/run_access_cm/open_are_vdi_terminal.gif){: class="example-img" loading="lazy"}

### Set up persistent session 
To support the use of long-running processes, such as ACCESS model runs, NCI provides a service on _Gadi_ called [persistent sessions](https://opus.nci.org.au/display/Help/Persistent+Sessions).

To run {{ model }}, you need to start a persistent session and set it as the target session for the model run.

#### Start a new persistent session
To start a new persistent session on _Gadi_, using either a login node or an ARE terminal instance, run the following command:
```
persistent-sessions start <name>
```

This will start a persistent session with the given `name` that runs under your [default project](/getting_started/set_up_nci_account#change-default-project-on-gadi).<br>
If you want to assign a different project to the persistent session, use the option `-p`:
```
persistent-sessions start -p <project> <name>
```

!!! tip
    While the project assigned to a persistent session does not have to be the same as the project used to run the {{ model }} configuration, it does need to have allocated _Service Units (SU)_.<br>
    For more information, check how to [Join relevant NCI projects](/getting_started/set_up_nci_account#join-relevant-nci-projects).

<terminal-window data="input">
    <terminal-line>persistent-sessions start &lt;name&gt;</terminal-line>
    <terminal-line data="output">session &lt;persistent-session-uuid&gt; running - connect using</terminal-line>
    <terminal-line data="output">&emsp;ssh &lt;name&gt;.&lt;$USER&gt;.&lt;project&gt;.ps.gadi.nci.org.au</terminal-line>
</terminal-window>

To list all active persistent sessions run:
```
persistent-sessions list
```

<terminal-window data="input">
    <terminal-line>persistent-sessions list</terminal-line>
    <terminal-line data="output">&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&ensp;UUID&emsp;&emsp;PROJECT&emsp;&ensp;&ensp;ADDRESS&emsp;&emsp;&emsp;&emsp;CPUTIME&emsp;MEMORY</terminal-line>
    <terminal-line data="output">&lt;persistent-session-uuid&gt;&emsp;&lt;project&gt;&emsp;10.9.0.62&emsp;00:00:05.213&emsp;30.5M</terminal-line>
</terminal-window>


The label of a newly-created persistent session has the following format: <br>
`<name>.<$USER>.<project>.ps.gadi.nci.org.au`.

#### Specify target persistent session

After starting the persistent session, it is essential to assign it to the {{ model }} run.<br>
The easiest way to create a file `~/.persistent-sessions/cylc-session` that contains the target of the persistent session.<br>
You can do it manually, or by running the following command (by substituting `<name>` with the name given to the persistent session, and `<project>` with the project assigned to it):

```
echo "<name>.${USER}.<project>.ps.gadi.nci.org.au" > ~/.persistent-sessions/cylc-session
```

For example, if the user `abc123` started a persistent session named `cylc` under the project `xy00`, the command will be:

<terminal-window data="input">
    <terminal-line>echo "cylc.abc123.xy00.ps.gadi.nci.org.au" > ~/.persistent-sessions/cylc-session
    </terminal-line>
    <terminal-line data="input" linedelay="1000">cat ~/.persistent-sessions/cylc-session</terminal-line>
    <terminal-line data="output">cylc.abc123.xy00.ps.gadi.nci.org.au</terminal-line>
</terminal-window>

For more information on how to specify the target session, refer to [Specify Target Session with Cylc7 Suites](https://opus.nci.org.au/display/DAE/Run+Cylc7+Suites#RunCylc7Suites-SpecifyTargetSession).

!!! tip
    You can simultaneously submit multiple {{ model }} runs using the same persistent session without needing to start a new one. Hence, the process of specifying the target persistent session for {{ model }} should only be done once. Then, to run {{ model }}, you just need to ensure that you have an active persistent session named like the target one you specified above. If the persistent session is not active, simply [start one](#start-a-new-persistent-session).

#### Terminate a persistent session
!!! tip
    Logging out of a *Gadi* login node or an ARE terminal instance will not affect your persistent session. 

To stop a persistent session, run:
```
persistent-sessions kill <persistent-session-uuid>
```
!!! warning
    When you terminate a persistent session, any model running on that session will stop. Therefore, you should check whether you have any active model runs before terminating a persistent session.

### Rose/Cylc/MOSRS setup

To run {{ model }}, access to multiple software and MOSRS authentication is needed.

#### Cylc setup {: id="cylc"}

[_Cylc_](https://cylc.github.io/cylc-doc/7.8.8/html/index.html) (pronounced ‘silk’) is a workflow manager that automatically executes tasks according to the model's main cycle script `suite.rc`. _Cylc_ controls how the job will be run and manages the time steps of each model component. It also monitors all tasks, reporting any errors that may occur.

To get the _Cylc_ setup required to run {{ model }}, execute the following commands:
```
module use /g/data/hr22/modulefiles
module load cylc7
```
<terminal-window data="input">
    <terminal-line>module use /g/data/hr22/modulefiles</terminal-line>
    <terminal-line>module load cylc7</terminal-line>
    <terminal-line data="output">Using the cylc session &lt;name&gt;.&lt;$USER&gt;.&lt;project&gt;.ps.gadi.nci.org.au</terminal-line>
    <terminal-line data="output"></terminal-line>
    <terminal-line data="output">Loading cylc7/24.03</terminal-line>
    <terminal-line data="output">&emsp;Loading requirement: mosrs-setup/2.0.1</terminal-line>
</terminal-window>

!!! warning
    It is recommended not to specify any version when loading _Cylc_, as versions earlier than `23.09` do not support the persistent sessions workflow.<br>
    Also, before loading the _Cylc_ module, make sure to have started a persistent session and assigned it to the {{ model }} workflow. For more information about these steps, refer to [Set up persistent session](#set-up-persistent-session).

#### Rose setup {: id="rose"}
[Rose](http://metomi.github.io/rose/doc/html/index.html) is a toolkit that can be used to view, edit, or run an ACCESS modelling suite.

By completing the [_Cylc_ setup](#cylc), also _Rose_ will be automatically available. Therefore, there is no additional step required.

#### MOSRS authentication
To authenticate using your _MOSRS_ credentials, run:
```
mosrs-auth
```
<terminal-window>
    <terminal-line data="input">mosrs-auth</terminal-line>
    <terminal-line lineDelay=500><span style="color: #559cd5;">INFO</span>: You need to enter your MOSRS credentials here so that GPG can cache your password.</terminal-line>
    <terminal-line>Please enter the MOSRS password for &lt;MOSRS-username&gt;:</terminal-line>
    <terminal-line lineDelay=1500>Checking your credentials using Subversion. Please wait.</terminal-line>
    <terminal-line lineDelay=500><span style="color: #559cd5;">INFO</span>: Successfully accessed Subversion with your credentials.</terminal-line>
    <terminal-line lineDelay=100><span style="color: #559cd5;">INFO</span>: Checking your credentials using rosie. Please wait.</terminal-line>
    <terminal-line lineDelay=500><span style="color: #559cd5;">INFO</span>: Successfully accessed rosie with your credentials.</terminal-line>
</terminal-window>

### {{ model }} configuration
{{ model }} is comprised of 2 different suites: a [Regional Ancillary Suite (RAS)](#ras) and a [Regional Nesting Suite (RNS)](#rns).

Each {{ model }} suite has a `suite-ID` in the format `u-<suite-name>`, where `<suite-name>` is a unique identifier.<br>
Typically, an existing suite is copied and then edited as needed for a particular run.

For more information on {{ model }}, refer to the [{{model}} configuration][model configurations] page.

!!! info 
    Many of the steps that follow are to be repeated almost identically for the RAS and RNS. For this reason, details on these steps will be provided only within the RAS section below, whereas in the following RNS section they will only be linked for reference.

### Regional Ancillary Suite (RAS) {: id="ras"}

The RAS produces a set of input (ancillary) files, which are then used by the RNS.

The `suite-ID` of the RAS is `{{ ras_id }}`.
<!--
TODO
{: style="color:red"}
<!-- Add short description of what the RAS does -->

#### Get the RAS configuration
[Rosie](http://metomi.github.io/rose/doc/html/tutorial/rose/rosie.html) is an [SVN](https://subversion.apache.org) repository wrapper with a set of options specific for ACCESS modelling suites. It is automatically available within the [_Rose_ setup](#rose).

The RAS configuration can be obtained as a copy of the MOSRS one, following 2 approaches:

- [Local-only copy](#local-copy)
- [Remote and local copy](#remote-copy)

Suites are, by default, created in the user's home directory on _Gadi_ under `~/roses/<suite-ID>`.
This path will be referred to as the *suite directory*.
{: id="suitedir" }

The suite directory contains multiple subdirectories and files, including:

- `app` &rarr; directory containing the configuration files for various tasks within the suite.
- `meta` &rarr; directory containing the GUI metadata.
- `rose-suite.conf` &rarr; main suite configuration file.
- `rose-suite.info` &rarr; suite information file.
- `suite.rc` &rarr; _Cylc_ control script file (Jinja2 language).

##### Local-only copy {: id="local-copy"}
To create a _local copy_ of the RAS from the UKMO repository, run:
```
rosie checkout {{ ras_id }}
```
<terminal-window>
    <terminal-line data="input">rosie checkout {{ ras_id }}</terminal-line>
    <terminal-line>[INFO] create: /home/565/&lt;$USER&gt;/roses</terminal-line>
    <terminal-line>[INFO] &lt;suite-ID&gt;: local copy created at /home/565/&lt;$USER&gt;/roses/{{ ras_id }}</terminal-line>
</terminal-window>
This option is mostly used for testing and examining existing suites.
    
##### Remote and local copy {: id="remote-copy"}
To create a new copy of the RAS both _locally_ and _remotely_ in the UKMO repository, run: 
```
rosie copy {{ ras_id }}
```
<terminal-window>
    <terminal-line data="input">rosie copy {{ ras_id }}</terminal-line>
    <terminal-line>Copy "{{ ras_id }}/trunk@&lt;trunk-ID&gt;" to "u-?????"? [y or n (default)]</terminal-line> <terminal-line data="input">y</terminal-line>
    <terminal-line>[INFO] &lt;new-suite-ID&gt;: created at https://code.metoffice.gov.uk/svn/roses-u/&lt;suite-n/a/m/e/&gt;</terminal-line>
    <terminal-line>[INFO] &lt;new-suite-ID&gt;: copied items from {{ ras_id }}/trunk@&lt;trunk-ID&gt;</terminal-line>
    <terminal-line>[INFO] {{ ras_id }}: local copy created at /home/565/&lt;$USER&gt;/roses/&lt;new-suite-ID&gt;</terminal-line>
</terminal-window>
When a new suite is created in this way, a _unique_ `<suite-ID>` is generated within the repository and populated with descriptive information about the suite and its initial configuration.

For additional `rosie` options, run:
```
rosie help
```

#### Run the suite
{{ model }} suites run on [_Gadi_](https://opus.nci.org.au/display/Help/0.+Welcome+to+Gadi#id-0.WelcometoGadi-Overview) through a [PBS job] submission.<br>
When a suite runs, its configuration files are copied on _Gadi_ inside `/scratch/$PROJECT/$USER/cylc-run/<suite-ID>` and a symbolic link to this directory is also created in the `$USER`'s home directory under `~/cylc-run/<suite-ID>`.<br>
{{ model }} suites comprise several tasks, such as checking out code repositories, compiling and building the different model components, running the model, etc. The workflow of these tasks is controlled by [_Cylc_](#cylc).

To run the RAS, execute the following command from within the [suite directory](#suitedir):
```
rose suite-run
```

After the initial tasks are executed, the _Cylc_ GUI will open. You can now view and control the different tasks in the suite as they are run. <!--<img src="/assets/run&UnderBar;access_cm/Cylc_GUI_are.png" alt="Cylc GUI" imageTime="inf" loading="lazy"> -->
<!--
TODO
{: style="color:red"}
<!-- Replace image with RAS one -->

You are done!

If you do not get any errors, you can check the suite output files after the run is complete.<br>
You can now close the _Cylc_ GUI. To open it again, run the following command from within the [suite directory](#suitedir):
```
rose suite-gcontrol
```

#### Monitor the suite runs

It is quite common, especially during the first few runs, to experience errors and job failures. Running a suite involves the execution of several tasks, and any of these tasks could fail. When a task fails, the suite is halted and a red icon appears next to the respective task name in the _Cylc_ GUI.<br>
To investigate the cause of a failure, we can look at the logs `job.err` and `job.out` from the suite run. There are two main ways to do so:

##### Using the _Cylc_ GUI
Right-click on the task that failed and click on _View Job Logs (Viewer) &rarr; job.err_ (or _job.out_).<br>
To access a specific task, click on the arrow next to the task to extend the drop-down menu with all the subtasks.

<!--
![Investigate Error GUI example](/assets/run_access_cm/investigate_error_gui_are.gif){: class="example-img" loading="lazy"}

TODO
{: style="color:red"}
<!-- Replace with a RAS video/gif -->


##### Through the suite directory
The suite's log directories are stored in `~/cylc-run/<suite-ID>` as `log.<TIMESTAMP>`, and the latest set of logs are also symlinked in the `~/cylc-run/<suite-ID>/log` directory.<br>
The logs for the main job can be found in the `~/cylc-run/<suite-ID>/log/job` directory.<br>
Logs are separated into simulation cycles according to their starting dates, then divided into subdirectories according to the task name. They are then further separated into "attempts" (consecutive failed/successful tasks), where `NN` is a symlink to the most recent attempt.

For the above *Lismore* default example, the `<cycle-basedate>` was set to `1` and no real starting date was used. Since the suite failed, the `job.err` and `job.out` files can be found in the `~/cylc-run/<suite-ID>/log/job/NN/<cycle-basedate>/TASK_NAME/NN` directory.

For a subsequent run, the `<cycle-basedate>` directory gets compressed to `job-<cylc-basedate>.tar.gz` and stored in the `~/cylc-run/<suite-ID>/log/` directory.

<!-- 
TODO
{: style="color:red"}
<!-- Replace TASK_NAME with the actual task name according to the video/gif above -->

#### Stop, restart and reload suites
In some cases, you may want to control the running state of a suite.<br>
If your _Cylc_ GUI has been closed and you are unsure whether your suite is still running, you can scan for active suites and reopen the GUI if desired.<br>
To scan for active suites, run:
```
cylc scan
```
To reopen the _Cylc_ GUI, run the following command from within the [suite directory](#suitedir):
```
rose suite-gcontrol
```
<!--
<terminal-window>
    <terminal-line data="input">cylc scan</terminal-line>
    <terminal-line>&lt;suite-ID&gt; &lt;$USER&gt;@&lt;gadi-cpu&gt;.nci.org.au:&lt;port&gt;</terminal-line>
    <terminal-line data="input">cd ~/roses/&lt;suite-ID&gt;</terminal-line>
    <terminal-line data="input" directory="~/roses/&lt;suite-ID&gt;">rose suite-gcontrol</terminal-line>
    <img src="/assets/run_access_cm/Cylc_GUI_are.png" alt="Cylc GUI" imageTime="inf" loading="lazy">
</terminal-window>

TODO
{: style="color:red"}
<!-- Replace with a RAS video/gif -->

##### STOP a suite
To shutdown a suite in a safe manner, run the following command from within the [suite directory](#suitedir):
```
rose suite-stop -y
```
Alternatively, you can directly kill the [PBS jobs][PBS job] connected to your run. To do so:

1. Check the status of all your PBS jobs:
```
qstat -u $USER
```

1. Delete any job related to your run:
```
qdel <job-ID>
```

##### RESTART a suite
There are two main ways to restart a suite:

- **_SOFT_ restart**<br>
    To reinstall the suite and reopen _Cylc_ in the same state it was prior to being stopped, run the following command from within the [suite directory](#suitedir):
    ```
    rose suite-run --restart
    ```

    !!! warning
        You may need to manually trigger failed tasks from the _Cylc_ GUI.

<!--
<terminal-window lineDelay="50">
    <terminal-line data="input" lineDelay="300">cylc</terminal-line>
    <terminal-line data="input" lineDelay="300">cd ~/roses/&lt;suite-ID&gt;</terminal-line>
    <terminal-line data="input" directory="~/roses/&lt;suite-ID&gt;" lineDelay="300">rose suite-run --restart</terminal-line>
    <terminal-line>[INFO] export CYLC_VERSION=7.9.7</terminal-line>
    <terminal-line>[INFO] export ROSE_ORIG_HOST=&lt;gadi-cpu&gt;.nci.org.au</terminal-line>
    <terminal-line>[INFO] export ROSE_SITE=nci</terminal-line>
    <terminal-line>[INFO] export ROSE_VERSION=2019.01.2</terminal-line>
    <terminal-line>[INFO] delete: log/rose-suite-run.conf</terminal-line>
    <terminal-line>[INFO] symlink: rose-conf/&lt;timestamp&gt;-restart.conf <= log/rose-suite-run.conf</terminal-line>
    <terminal-line>[INFO] delete: log/rose-suite-run.version</terminal-line>
    <terminal-line>[INFO] symlink: rose-conf/&lt;timestamp&gt;-restart.version <= log/rose-suite-run.version</terminal-line>
    <terminal-line>[INFO] chdir: log/</terminal-line>
    <terminal-line>[WARN] Using the cylc session &lt;persistent-session-full-name&gt;</terminal-line>
    <terminal-line>[WARN]</terminal-line>
    <terminal-line>[WARN] Loading cylc7/23.09</terminal-line>
    <terminal-line>[WARN] &emsp;Loading requirement: mosrs-setup/1.0.1</terminal-line>
    <terminal-line>[INFO] &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&thinsp;&thinsp;._.</terminal-line>
    <terminal-line>[INFO] &emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&thinsp;&thinsp;| |&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;The Cylc Suite Engine [7.9.7]</terminal-line>
    <terminal-line>[INFO] ._____._. ._| |_____.&emsp;&emsp;&emsp;&emsp;&emsp;&thinsp;Copyright (C) 2008-2019 NIWA</terminal-line>
    <terminal-line>[INFO] | .___| | | | | .___|&emsp;& British Crown (Met Office) & Contributors.</terminal-line>
    <terminal-line>[INFO] | !___| !_! | | !___. _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _ _</terminal-line>
    <terminal-line>[INFO] !_____!___. |_!_____! This program comes with ABSOLUTELY NO WARRANTY;</terminal-line>
    <terminal-line>[INFO] &emsp;&emsp;&emsp;&thinsp;.___! | &emsp;&emsp;&emsp;&emsp;&emsp;see `cylc warranty`. &thinsp;It is free software, you</terminal-line>
    <terminal-line>[INFO] &emsp;&emsp;&emsp;&thinsp;!_____! &emsp;&emsp;&emsp;&emsp;&emsp;&thinsp;are welcome to redistribute it under certain</terminal-line>
    <terminal-line>[INFO]</terminal-line>
    <terminal-line>[INFO] *** listening on https://&lt;persistent-session-full-name&gt;:&lt;port&gt;/ ***</terminal-line>
    <terminal-line>[INFO]</terminal-line>
    <terminal-line>[INFO] To view suite server program contact information:</terminal-line>
    <terminal-line>[INFO]  $ cylc get-suite-contact &lt;suite-ID&gt;</terminal-line>
    <terminal-line>[INFO]</terminal-line>
    <terminal-line>[INFO] Other ways to see if the suite is still running:</terminal-line>
    <terminal-line>[INFO]  $ cylc scan -n '&lt;suite-ID&gt;' &lt;persistent-session-full-name&gt;</terminal-line>
    <terminal-line>[INFO]  $ cylc ping -v --host=&lt;persistent-session-full-name&gt; &lt;suite-ID&gt;</terminal-line>
    <terminal-line>[INFO]  $ ps -opid,args &lt;PID&gt;  # on &lt;persistent-session-full-name&gt;</terminal-line>
    <img src="/assets/run_access_cm/Cylc_GUI_are.png" alt="Cylc GUI" imageTime="inf" loading="lazy">
</terminal-window>

TODO
{: style="color:red"}
<!-- Replace with a RAS image -->

- **HARD restart**<br>
    To overwrite any previous runs of the suite and start afresh, run the following command from within the [suite directory](#suitedir):
    ```
    rose suite-run --new
    ```

    !!! warning
        This will overwrite all existing model output and logs for the same suite.

##### RELOAD a suite
In some cases the suite needs to be updated without necessarily having to stop it (e.g., after fixing a typo in a file). Updating an active suite is called a _reload_, where the suite is _re-installed_ and _Cylc_ is updated with the changes. This is similar to a _SOFT_ restart, except new changes are installed, so you may need to manually trigger failed tasks from the _Cylc_ GUI.

To reload a suite, run the following command from within the [suite directory](#suitedir):
```
rose suite-run --reload
```

#### RAS output files
    
The RAS output ancillary files can be found in `/scratch/$PROJECT/$USER/cylc-run/<suite-ID>/share/data/ancils`. The ancillaries are divided for each nested region, with each of the region subdivided in directories named according to the nests' names. Therefore, the path of the ancillaries for a specific nest is `/scratch/$PROJECT/$USER/cylc-run/<suite-ID>/share/data/ancils/<nested_region_name>/<nest_name>`.
<!--
TODO
{: style="color:red"}
<!-- There is no information before about nests (e.g., nest structure, what is the difference between nested region name and nest name, etc.) It would be good to add something in the suite description and link it here. -->

In addition to these nests, a subdirectory named according to the driving model data can be found within the `nested_region_name` directory.<br>
The 2-level (i.e., two nests) example above has one `nested_region_name` called `Lismore`. Driven by ERA5 data, the outer and inner nests are named `d1000` and `d0198`, respectively.<br>
Thus, the ancillary files directory `/scratch/$PROJECT/$USER/cylc-run/<suite-ID>/share/data/ancils/` contains the following folders:
- `Lismore/d1000`
- `Lismore/d0198`
- `Lismore/era5`

The ancillary data files are typically in the [UM fieldsfile](https://code.metoffice.gov.uk/doc/um/latest/papers/umdp_F03.pdf) format.

#### Edit the RAS configuration
This section describes how to modify the RAS configuration.<br>
The modifications discussed in this section can change the way the RAS is run and, as a result, control the ancillaries that are generated.

In general, ACCESS modelling suites can be edited either by directly modifying the configuration files within the [suite directory](#suitedir), or by using the [_Rose_ GUI](#rosegui).

!!! warning 
    Unless you are an experienced user, direct modification of configuration files is discouraged to avoid errors.

##### Rose GUI {: id="rosegui"}
To open the [_Rose_](#rose) GUI, run the following command from within the [suite directory](#suitedir): 
```
rose edit &
```

!!! tip
    The `&` is optional. It allows the terminal prompt to remain active while running the `Rose` GUI as a separate process in the background.
<!--
<terminal-window>
    <terminal-line data="input">cd ~/roses/&lt;suite-ID&gt;</terminal-line>
    <terminal-line data="input" directory="~/roses/&lt;suite-ID&gt;">rose edit &</terminal-line>
    <terminal-line class="ls-output-format">[&lt;N&gt;] &lt;PID&gt;</terminal-line>
    <terminal-line data="input" directory="~/roses/&lt;suite-ID&gt;"></terminal-line>
    <img src="/assets/run_access_cm/Rose_GUI_are.png" alt="Rose GUI" imageTime="inf" loading="lazy">
</terminal-window>

TODO
{: style="color:red"}
<!-- Change image with a RAS one -->

##### Change the region centre and name
In {{model}}, it is possible to perform simulations for a specific region on Earth, by setting specific parameters for the region of interest.<br>
In the {{ancillary_model}}, the following parameters can be set:

- `rg01_name`<br>
  Name for the region of interest.

- `rg01_centre`<br>
  Set of latitude/longitude coordinates defining the centre of the region of interest.
<!--
TODO
What about the extent of the region? -->

!!! tip
    In `{{ras_id}}` these parameters are set, by default, to the *Lismore Flood* example. 
<!--
TODO
Can we add a link for the Lismore Flood example? Anything that makes it a bit clearer what that is. -->

To change these parameters, edit the `rg01_name` and `rg01_centre` fields in _suite conf &rarr; Regional Ancillary Suite &rarr; Nested region 1 setup_, and click the _Save_ button ![Save button](/assets/run_access_cm/save_button.png){: style="height:1em"}.

For example, to run an {{ model }} suite for a region with centre (-12.4, 130.8) and name "Darwin":

![Rose change region centre example](/assets/run_access_ras/ras_edit_region_center_and_name.gif){: class="example-img" loading="lazy"}

<!--
TODO
{: style="color:red"}
<!-- Add a gif/video for the exmple -->

### Regional Nesting Suite (RNS) {: id="rns"}
<!--
The RNS is ...
-->
The `suite-ID` of the RNS is `{{ rns_id }}`.

<!--
TODO
{: style="color:red"}
<!-- Add short description of what the RAS does -->

#### Get and run RNS configuration
Steps to obtain and run the RNS configuration, as well as monitoring the runs, are analogous to those listed above for the [RAS](#ras).<br>
The only difference is the `suite-ID` that, for the RNS is `{{ rns_id }}`.

To get the RNS configuration, please follow the steps listed in [Get the RAS configuration](#get-the-ras-configuration), making sure you use the correct RNS `suite-ID` `{{ rns_id }}` when copying the suite.

To run the RNS configuration, please follow the steps listed in [Run the suite](#run-the-suite).

To monitor the RNS suite run, please follow the steps listed in [Monitor the suite runs](#monitor-the-suite-runs).

#### RNS output files

All the RNS output files are available on _Gadi_ inside `/scratch/$PROJECT/$USER/cylc-run/<suite-ID>`. They are also symlinked in `~/cylc-run/<suite-ID>`.

The RNS output data can be found in the `/scratch/$PROJECT/$USER/cycl-run/<suite-ID>/share/cycle` directory, grouped for each [cycle](#change-run-length-and-cycling-frequency).<br>
Within the cycle directory, outputs are divided into multiple nested subdirectories in the format `<nested_region>/<science_choice>/<nest_name>`, with [nested_region](), [science_choice]() and [nest_name]() referring to the respective configurable options in the [RAS](#edit-the-ras-configuration).

<!--
TODO
{: style="color:red"}
<!-- Use the same names for `nested_region` `science_choice` and `nest_name` as the one used above for the RAS and make sure all of the are referenced in the RAS and there are clear instructions on how to modify them. There is no reference for how to change the `science_choice`. If it cannot be changed, I would call it in a different way. Add links -->

Each `<nest_name>` directory has the following subdirectories:

- `ics` &rarr; initial conditions
- `lbcs` &rarr; lateral boundary conditions
- `um` &rarr; model output data

<!--
TODO
{: style="color:red"}
<!-- Use the same names for `nested_region` `science_choice` and `nest_name` as the one used above for the RAS and make sure all of the are referenced in the RAS and there are clear instructions on how to modify them. Add links -->

The model output data for the `Lismore` `nested_region`, using a `RAL3P2` `science_choice` and `d0198` as a `nest_name`, will be stored in `/scratch/$PROJECT/$USER/cylc-run/<suite-ID>/share/cycle/20220226T0000Z/Lismore/d0198/RAL3P2/um`.

{{regional}}'s atmosphere output data files are typically in the [UM fieldsfile](https://code.metoffice.gov.uk/doc/um/latest/papers/umdp_F03.pdf) format.

#### Edit the RNS configuration

This section describes how to modify the RNS configuration.
The modifications discussed in this section can change the way the RNS is run, or how its specific [model components] are configured and coupled together.

In general, ACCESS modelling suites can be edited either by directly modifying the configuration files within the suite directory, or by using the [_Rose_ GUI](#rosegui).

!!! warning
    Unless you are a very expert user, directly modifying configuration files is usually discouraged to avoid incurring in errors.

To open the _Rose_ GUI, you can follow the steps listed in [Rose GUI](#rosegui).

#### Change where the output from run is stored project
To change where the output from the model run is stored, edit the _NCI_STORAGE_ field in _suite conf &rarr; Nesting Suite &rarr; General run options _, and click the _Save_ button ![Save button](/assets/run_access_cm/save_button.png){: style="height:1em"}. 

<!--
TODO
{: style="color:red"}
<!-- I couldn't restructure this title/text because from them I am not really understanding what `NCI_STORAGE` is. From its default value in the rose-suite.conf (scratch/$PROJECT) and how it's used inside the suite, it seems to be a project folder added to the `storage` option for the PBS jobs submission of many tasks. Therefore is not necessarily related to model outputs (is also related to model input), but to filesystem mounts that the model need to be able to access. Why is the text mentioning changing where the output is stored? -->

#### Change run length and cycling frequency
The RNS is run in multiple steps, each one constituting a cycle. The job scheduler resubmits the suite every chosen cycling frequency, until the total run length is reached.

For the RNS:

- `CYCLE_INT_HR` is the cycling frequency (in hours)
- The total run length is the time span between the `INITIAL_CYCLE_POINT` and `FINAL_CYCLE_POINT`

To modify these parameters, navigate to _suite conf &rarr; Nesting Suite &rarr; Cycling options_, edit the `INITIAL_CYCLE_POINT`, `FINAL_CYCLE_POINT` and the `CYCLE_INT_HR` fields (using [ISO 8601 Duration](https://en.wikipedia.org/wiki/ISO_8601#Durations) format) and click the _Save_ button ![Save button](/assets/run_access_cm/save_button.png){: style="height:1em"}.

<!--
TODO
{: style="color:red"}
<!-- Are there any other options more specific to the RNS that users may want to change here? -->

<!--
## Get Help
If you have questions or need help regarding {{ model }}, consider creating a topic in the [Earth System Model category of the ACCESS-Hive Forum](https://forum.access-hive.org.au/c/esm/earth-system-model/72).<br>
For assistance on how to request help from ACCESS-NRI, follow the [guidelines on how to get help](/about/user_support/#still-need-help).

TODO
{: style="color:red"}
<!-- Check and change link details if needed -->


<custom-references>
- [https://nespclimate.com.au/wp-content/uploads/2020/10/Instruction-document-Getting_started_with_ACCESS.pdf](https://nespclimate.com.au/wp-content/uploads/2020/10/Instruction-document-Getting_started_with_ACCESS.pdf)
- [https://code.metoffice.gov.uk/doc/um/latest/um-training/rose-gui.html](https://code.metoffice.gov.uk/doc/um/latest/um-training/rose-gui.html)
- [https://opus.nci.org.au/display/DAE/Run+Cylc7+Suites](https://opus.nci.org.au/display/DAE/Run+Cylc7+Suites)
- [https://opus.nci.org.au/display/Help/Persistent+Sessions](https://opus.nci.org.au/display/Help/Persistent+Sessions)
</custom-references>
