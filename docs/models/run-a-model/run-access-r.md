{% set model = "ACCESS-R" %}
[PBS job]: https://opus.nci.org.au/display/Help/4.+PBS+Jobs

# Run {{ model }}

!!! warning
    **Important for _accessdev_ users!**<br>
    If you were an _accessdev_ user, make sure you are a member of [hr22](https://my.nci.org.au/mancini/project/hr22/join) and [ki32](https://my.nci.org.au/mancini/project/ki32/join) projects.<br>
    Then, refer to instructions on how to [Set up persistent session workflow for {{ model }}]({{ '#set-up-%s-persistent-session'%model.lower() }}), and how to [port suites from accessdev](#port-suites-from-accessdev).

## Prerequisites

### General prerequisites

Before running {{ model }}, you need to [Set Up your NCI Account](/getting_started/set_up_nci_account).

If you are unsure whether {{ model }} is the right choice for your experiment, take a look at the overview of [ACCESS Models](/models).

### Model-specific prerequisites

- **_MOSRS_ account**<br>
    The [Met Office Science Repository Service (MOSRS)](https://code.metoffice.gov.uk) is a server run by the UK Met Office (UKMO) to support collaborative development with other partners organisations. MOSRS contains the source code and configurations for some model components in {{ model }} (e.g., the [UM](/models/model_components/atmosphere/#unified-model-um)).<br>
    To apply for a _MOSRS_ account, please contact your [local institutional sponsor](https://opus.nci.org.au/display/DAE/Prerequisites).

- **Join the _access_, _hr22_, _ki32_, _ki32\_mosrs_, _rt52_, _zz93_, _hh5_ and vk83_ projects at NCI**<br>
    To join these projects, request membership on the respective [access](https://my.nci.org.au/mancini/project/access/join), [hr22](https://my.nci.org.au/mancini/project/hr22/join), [ki32](https://my.nci.org.au/mancini/project/ki32/join) and 
[ki32_mosrs](https://my.nci.org.au/mancini/project/ki32_mosrs/join), [rt52](https://my.nci.org.au/mancini/project/rt52/join), [zz93](https://my.nci.org.au/mancini/project/zz93/join), [hh5](https://my.nci.org.au/mancini/project/hh5/join) and [vk83](https://my.nci.org.au/mancini/project/vk83/join) 
NCI project pages.

    !!! tip
        To request membership for the _ki32_mosrs_ subproject you need to be member of the _ki32_ project first.
    
    For more information on how to join specific NCI projects, refer to [How to connect to a project](https://opus.nci.org.au/display/Help/How+to+connect+to+a+project).

- **Connection to an ARE VDI Desktop (optional)**<br>
    To run {{ model }}, start an [Australian Research Environment (ARE) VDI Desktop](https://are.nci.org.au/pun/sys/dashboard/batch_connect/sys/desktop_vnc/ncigadi/session_contexts/new) session.<br>
    If you are not familiar with ARE, check out the [Getting Started on ARE](/getting_started/are) section.

## Set up an ARE VDI Desktop (optional)
To skip this step and instead run {{ model }} from _Gadi_ login node, refer to instructions on how to [Set up {{ model }} persistent session]({{ '#set-up-%s-persistent-session'%model.lower() }}).

### Launch ARE VDI Session
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
    For more information, refer to how to [Join relevant NCI projects](/getting_started/set_up_nci_account#join-relevant-nci-projects).

- **Storage** &rarr; `gdata/access+gdata/hr22+gdata/ki32+gdata/rt52+gdata/zz93+gdata/hh5+gdata/vk83` (minimum)<br>
    This is a list of all project data storage, joined by plus (`+`) signs, needed for the {{ model }} simulation. In ARE, storage locations need to be explicitly defined to access data from within a VDI instance.<br>
    Every {{ model }} simulation can be unique and input data can originate from various sources. Hence, if your simulation requires data stored in project folders other than `access`, `hh5`, `hr22` or `ki32`, you need to add those projects to the storage path.<br>
    For example, if your {{ model }} simulation requires data stored in `/g/data/tm70` and `/scratch/w40`, your full storage path will be: `gdata/access+gdata/hr22+gdata/ki32+gdata/rt52+gdata/zz93+gdata/hh5+gdata/vk83+gdata/tm70+scratch/w40`
    
Launch the ARE session and, once it starts, click on _Launch VDI Desktop_.

![Launch ARE VDI session example](/assets/run_access_cm/launch_are_vdi.gif){: class="example-img" loading="lazy"}

### Open the terminal in the VDI Desktop
Once the new tab opens, you will see a Desktop with a few folders on the left.<br>
To open the terminal, click on the black terminal icon at the top of the window. You should now be connected to a _Gadi_ computing node.

![Open ARE VDI terminal example](/assets/run_access_cm/open_are_vdi_terminal.gif){: class="example-img" loading="lazy"}

## Set up {{ model }} persistent session
To support the use of long-running processes, such as ACCESS model runs, NCI provides a service on _Gadi_ called [persistent sessions](https://opus.nci.org.au/display/Help/Persistent+Sessions).

To run {{ model }}, you need to start a persistent session and set it as the target session for the model run.

### Start a new persistent session
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
`<name>.<$USER&gt;.<project>.ps.gadi.nci.org.au`.

### Specify {{ model }} target persistent session

After starting the persistent session, it is essential to assign it to the {{ model }} run.<br>
The easiest way to do this is to insert the persistent session label into the file `~/.persistent-sessions/cylc-session`.<br>
You can do it manually, or by running the following command (by substituting `<name>` with the name given to the persistent session, and `<project>` with the project assigned to it):
```
cat > ~/.persistent-sessions/cylc-session <<< "<name>.${USER}.<project>.ps.gadi.nci.org.au"
```

For example, if the user `abc123` started a persistent session named `cylc` under the project `xy00`, the command will be:

<terminal-window data="input">
    <terminal-line>cat > ~/.persistent-sessions/cylc-session <<< cylc.abc123.xy00.ps.gadi.nci.org.au</terminal-line>
    <terminal-line data="input" linedelay="1000">cat ~/.persistent-sessions/cylc-session</terminal-line>
    <terminal-line data="output">cylc.abc123.xy00.ps.gadi.nci.org.au</terminal-line>
</terminal-window>

For more information on how to specify the target session, refer to [Specify Target Session with Cylc7 Suites](https://opus.nci.org.au/display/DAE/Run+Cylc7+Suites#RunCylc7Suites-SpecifyTargetSession).

!!! tip
    You can simultaneously submit multiple {{ model }} runs using the same persistent session without needing to start a new one. Hence, the process of specifying the target persistent session for {{ model }} should only be done once.<br>
    After specifying the {{ model }} target persistent session the first time, to run {{ model }} you just need to make sure to have an active persistent session named like the specified {{ model }} target persistent session.

### Terminate a persistent session
To stop a persistent session, run:
```
persistent-sessions kill <persistent-session-uuid>
```
!!! warning
    When you terminate a persistent session, any model running on that session will stop. Therefore, you should check whether you have any active model runs before terminating a persistent session.


## Get {{ model }} suite
{{ model }} comprises the model components [UM](/models/model_components/atmosphere#unified-model-um) and [LAND](/models/model_components/land). These components, which have different model parameters, input data and computer-related information, need to be packaged together as a _suite_ in order to run.<br>
Each {{ model }} suite has a `suite-ID` in the format `u-<suite-name>`, where `<suite-name>` is a unique identifier.<br>
Typically, an existing suite is copied and then edited as needed for a particular run.

For this example you can use `u-dg767` and `u-dg768`, which are regional ancillary and regional nesting suites respectively.<br>

### Copy {{ model }} suite with Rosie
[Rosie](http://metomi.github.io/rose/doc/html/tutorial/rose/rosie.html) is an [SVN](https://subversion.apache.org) repository wrapper with a set of options specific for {{ model }} suites.<br>
To copy an existing suite on _Gadi_ you need to follow three main steps:

#### Get Cylc7 setup
To get the Cylc7 setup required to run {{ model }}, execute the following commands:
```
module use /g/data/hr22/modulefiles
module load cylc7/23.09
```
<terminal-window data="input">
    <terminal-line>module use /g/data/hr22/modulefiles</terminal-line>
    <terminal-line>module load cylc7/23.09</terminal-line>
    <terminal-line data="output">Using the cylc session &lt;name&gt;.&lt;$USER&gt;.&lt;project&gt;.ps.gadi.nci.org.au</terminal-line>
    <terminal-line data="output"></terminal-line>
    <terminal-line data="output">Loading cylc7/23.09</terminal-line>
    <terminal-line data="output">&emsp;Loading requirement: mosrs-setup/1.0.1</terminal-line>
</terminal-window>

!!! warning
    Make sure to load _Cylc_ version `23.09` (or later), as earlier versions do not support the persistent sessions workflow.<br>
    Also, before loading the _Cylc_ module, make sure to have started a persistent session and assigned it to the {{ model }} workflow. For more information about these steps, refer to instructions on how to [Set up {{ model }} persistent session]({{ '#set-up-%s-persistent-session'%model.lower() }}).

#### MOSRS authentication
To authenticate using your _MOSRS_ credentials, run:
```
mosrs-auth
```
<terminal-window>
    <terminal-line data="input">mosrs-auth</terminal-line>
    <terminal-line lineDelay=500><span style="color: #559cd5;">INFO</span>: You need to enter your MOSRS credentials here so that GPG can cache your password.</terminal-line>
    <terminal-line>Please enter the MOSRS password for &lt;MOSRS-username&gt;:</terminal-line>
    <terminal-line lineDelay=1500><span style="color: #559cd5;">INFO</span>: Checking your credentials using Subversion. Please wait.</terminal-line>
    <terminal-line lineDelay=500><span style="color: #559cd5;">INFO</span>: Successfully accessed Subversion with your credentials.</terminal-line>
    <terminal-line lineDelay=100><span style="color: #559cd5;">INFO</span>: Checking your credentials using rosie. Please wait.</terminal-line>
    <terminal-line lineDelay=500><span style="color: #559cd5;">INFO</span>: Successfully accessed rosie with your credentials.</terminal-line>
</terminal-window>

#### Copy a suite

  - **Local-only copy**<br>
      To create a _local copy_ of the `<suite-ID>` from the UKMO repository, run:
      ```
      rosie checkout <suite-ID>
      ```
      <terminal-window>
          <terminal-line data="input">rosie checkout &lt;suite-ID&gt;</terminal-line>
          <terminal-line>[INFO] create: /home/565/&lt;$USER&gt;/roses</terminal-line>
          <terminal-line>[INFO] &lt;suite-ID&gt;: local copy created at /home/565/&lt;$USER&gt;/roses/&lt;suite-ID&gt;</terminal-line>
      </terminal-window>
      This option is mostly used for testing and examining existing suites.
      
  - **Remote and local copy**<br> 
      Alternatively, to create a new copy of an existing `<suite-ID>` both _locally_ and _remotely_ in the UKMO repository, run: 
      ```
      rosie copy <suite-ID>
      ```
      <terminal-window>
          <terminal-line data="input">rosie copy &lt;suite-ID&gt;</terminal-line>
          <terminal-line>Copy "&lt;suite-ID&gt;/trunk@&lt;trunk-ID&gt;" to "u-?????"? [y or n (default)]</terminal-line> <terminal-line data="input">y</terminal-line>
          <terminal-line>[INFO] &lt;new-suite-ID&gt;: created at https://code.metoffice.gov.uk/svn/roses-u/&lt;suite-n/a/m/e/&gt;</terminal-line>
          <terminal-line>[INFO] &lt;new-suite-ID&gt;: copied items from &lt;suite-ID&gt;/trunk@&lt;trunk-ID&gt;</terminal-line>
          <terminal-line>[INFO] &lt;suite-ID&gt;: local copy created at /home/565/&lt;$USER&gt;/roses/&lt;new-suite-ID&gt;</terminal-line>
      </terminal-window>
      When a new suite is created in this way, a _unique_ `<suite-ID>` is generated within the repository and populated with descriptive information about the suite and its initial configuration.

For additional `rosie` options, run:
```
rosie help
```

Suites are created in the user's home directory on _Gadi_ under `~/roses/<suite-ID>`.
The suite directory contains many directories and files, some of which are:

- `app` &rarr; directory containing the configuration files for various tasks within the suite.
- `meta` &rarr; directory containing the GUI metadata.
- `rose-suite.conf` &rarr; main suite configuration file.
- `rose-suite.info` &rarr; suite information file.
- `suite.rc` &rarr; _Cylc_ control script file (Jinja2 language).

## Edit {{ model }} suite configuration

### Rose
[Rose](http://metomi.github.io/rose/doc/html/index.html) is a configuration editor which can be used to view, edit, or run an {{ model }} suite.

To edit a suite configuration, run the following command from within the suite directory (i.e., `~/roses/<suite-ID>`) to open the _Rose_ GUI:
```
rose edit &
```

!!! tip
    The `&` is optional. It allows the terminal prompt to remain active while running the `Rose` GUI as a separate process in the background.

<terminal-window>
    <terminal-line data="input">cd ~/roses/&lt;suite-ID&gt;</terminal-line>
    <terminal-line data="input" directory="~/roses/&lt;suite-ID&gt;">rose edit &</terminal-line>
    <terminal-line class="ls-output-format">[&lt;N&gt;] &lt;PID&gt;</terminal-line>
    <terminal-line data="input" directory="~/roses/&lt;suite-ID&gt;"></terminal-line>
    <img src="/assets/run_access_cm/Rose_GUI_are.png" alt="Rose GUI" imageTime="inf" loading="lazy">
</terminal-window>
    

Regional forecasts are produced in two separate steps:

1) Ancillary files specific to the domain of interest need to be generated:

[Produce ancillary files](/models/run-a-model/run-access-r-ras.md)  <br>

2) A regional forecast/hindcast is generated specific to that domain of interest, although the timing can be changed:

[Produce regional forecast/hindcast](/models/run-a-model/run-access-r-rns.md)  <br>


<custom-references>
- [https://nespclimate.com.au/wp-content/uploads/2020/10/Instruction-document-Getting_started_with_ACCESS.pdf](https://nespclimate.com.au/wp-content/uploads/2020/10/Instruction-document-Getting_started_with_ACCESS.pdf)
- [https://code.metoffice.gov.uk/doc/um/latest/um-training/rose-gui.html](https://code.metoffice.gov.uk/doc/um/latest/um-training/rose-gui.html)
- [https://opus.nci.org.au/display/DAE/Run+Cylc7+Suites](https://opus.nci.org.au/display/DAE/Run+Cylc7+Suites)
- [https://opus.nci.org.au/display/Help/Persistent+Sessions](https://opus.nci.org.au/display/Help/Persistent+Sessions)
</custom-references>
