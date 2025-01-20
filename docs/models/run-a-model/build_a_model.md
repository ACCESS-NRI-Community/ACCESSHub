!!! danger
    This page is tailored to experienced users and collaborators developing ACCESS models.<br>
    This step is *not* required if you *only* want to run a model. If you are looking for information on how to run a model, refer to the [Run a Model](/models/run-a-model) section.

{% set esm1_5_build_config = "https://github.com/ACCESS-NRI/ACCESS-ESM1.5" %}
{% set spack_setup = "/getting_started/spack" %}
[gadi]: https://opus.nci.org.au/display/Help/0.+Welcome+to+Gadi#id-0.WelcometoGadi-Overview

# Modify an ACCESS model's source code

## About

The instructions below outline how to build an ACCESS model and its dependencies, using the build-from-source package manager [Spack](https://spack.readthedocs.io).<br>
This build workflow is specifically designed to run on the [National Computating Infrastructure (NCI)](https://nci.org.au/about-us/who-we-are) supercomputer [_Gadi_][gadi].

As an example, in the following instructions we will show how to modify [MOM5 component](/models/model_components/ocean/#mom5) for [ACCESS-ESM1.5](/models/configurations/access-esm/#access-esm15) and re-compile the relevant ACCESS-ESM1.5 dependencies. All the other components and packages (i.e., dependencies) will remain the same as the official [ACCESS-ESM1.5 release]({{esm1_5_build_config}}).

!!! tip
    The instructions below remain valid (with simple tweaks) for any model or package.

## Prerequisites

- **NCI account**<br>
  Before building a model, you need to [Set Up your NCI Account](/getting_started/set_up_nci_account).

- **_Spack_**<br>
  To set up _Spack_ on _Gadi_, refer to [Set up Spack for building ACCESS models]({{spack_setup}}).

## Navigate into your Spack directory
Navigate into the directory where you cloned the _Spack_ repositories during the [Spack setup]({{spack_setup}}).<br>
The suggested directory is `/g/data/$PROJECT/$USER/spack/0.22`:
```
cd /g/data/$PROJECT/$USER/spack/0.22
```

## Enable spack

!!! warning
    This step needs to be carried out for any new login or new shell environment.

To add the `spack` command to your shell, as well as other settings, run:
```
module purge
. spack-config/spack-enable.bash
```

!!! warning
    There is a space between the `.` and the path to the file, as we are [sourcing](https://tldp.org/HOWTO/Bash-Prompt-HOWTO/x237.html#:~:text=When%20a%20file%20is%20sourced,the%20file%20they%20are%20in.) the file.

## Create a Spack development environment

_Spack_ has [environments](https://spack.readthedocs.io/en/latest/environments.html) that are, in some ways, similar to _Conda_ environments or _Python_ virtual environments: they create an isolated operating environment where _Spack_ can only see and access specific packages. This allows the build and deployment of new packages in a coherent fashion.

_Spack_ [managed environments](https://spack.readthedocs.io/en/latest/environments.html#creating-a-managed-environment) are located inside `/g/data/$PROJECT/$USER/spack/0.22/environments`, each in a separate directory. Each environment configuration is defined within its directory using the [`spack.yaml`](https://spack.readthedocs.io/en/latest/config_yaml.html#config-yaml) file.
{: id="spack-environment-folder"}

!!! warning
    _Spack_ managed environments' location can be changed within _Spack_ configuration files and the directory specified above represents the default location for a _Spack_ instance that has been set up following the [Spack setup instructions]({{spack_setup}}).

### Create the environment

To ensure we keep all dependecies the same as the official release, we will create our [_Spack_ development environment](https://spack.readthedocs.io/en/latest/environments.html#creating-a-managed-environment) as a copy of the released [ACCESS-ESM1.5 Spack environment]({{esm1_5_build_config}}/blob/main/spack.yaml).<br>
To create a _Spack_ development environment called `mom5_dev`, run:

```
git clone {{esm1_5_build_config}}.git
spack env create mom5_dev ACCESS-ESM1.5/spack.yaml
```

<terminal-window>
  <terminal-line data="input">git clone {{esm1_5_build_config}}.git</terminal-line>
  <terminal-line>Cloning into 'ACCESS-ESM1.5'...</terminal-line>
  <terminal-line>remote: Enumerating objects: 29, done.</terminal-line>
  <terminal-line>remote: Counting objects: 100% (19/19), done.</terminal-line>
  <terminal-line>remote: Compressing objects: 100% (17/17), done.</terminal-line>
  <terminal-line>remote: Total 29 (delta 6), reused 3 (delta 1), pack-reused 10 (from 1)</terminal-line>
  <terminal-line>Receiving objects: 100% (29/29), 16.40 KiB | 2.38 MiB/s, done.</terminal-line>
  <terminal-line>Resolving deltas: 100% (6/6), done.</terminal-line>
  <terminal-line data="input">spack env create mom5_dev ACCESS-ESM1.5/spack.yaml</terminal-line>
  <terminal-line><span class="spack-indigo bold">\==></span> Created environment <span class="spack-cyan">mom5_dev</span> in: <span class="spack-cyan">/g/data/$PROJECT/$USER/spack/0.22/environments/mom5_dev</span></terminal-line>
  <terminal-line><span class="spack-indigo bold">\==></span> Activate with: <span class="spack-cyan">spack env activate mom5_dev</span></terminal-line>
</terminal-window>

The newly-created `mom5_dev` _Spack_ <b>environment folder</b> is `/g/data/$PROJECT/$USER/spack/0.22/environments/mom5_dev`.

### Activate the environment
To activate the `mom5_dev` _Spack_ environment, run:
```
spack env activate -p mom5_dev
```
<terminal-window>
    <terminal-line data="input">spack env activate -p mom5_dev</terminal-line>
    <terminal-line data="input" directory="[mom5_dev]" class="spack" lineDelay=0></terminal-line>
</terminal-window>

## Compile Spack environment packages (optional)

It is recommended to first compile all the packages in the newly created _Spack_ environment as is, without making any changes.

Compiling all the packages present in a _Spack_ environment is referred to as [installing the environment](https://spack.readthedocs.io/en/latest/environments.html#installing-an-environment).

### Concretize the Spack environment
[Concretizing](https://spack.readthedocs.io/en/latest/environments.html#concretizing) the _Spack_ environment is necessary anytime the environment's `spack.yaml` gets changed, to force _Spack_ to update its knowledge of the environment configuration.

To concretize the `mom5_dev` environment, run:
```
spack concretize -f --fresh
```

!!! warning
    This command might take a few minutes to complete

<terminal-window lineDelay=0>
  <terminal-line data="input" lineDelay=200 directory="[mom5_dev]" class="spack">
    spack concretize -f --fresh
  </terminal-line>
  <terminal-line lineDelay=2000>
    <span class="spack-indigo bold">\==></span> Concretized access-esm1p5@git.2024.05.1=2024.05.1
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   nkvasig</span>  access-esm1p5<span class="spack-cyan">@git.2024.05.1=2024.05.1</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=bundle</span> <span class="spack-pink"><span class="spack-pink">arch=linux-rocky8-x86_64_v4</span></span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   hhtnigw    </span> ^cice4<span class="spack-cyan">@git.2024.05.21=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=makefile</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   j6yscmm        </span> ^gmake<span class="spack-cyan">@4.4.1</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">~guile build_system=generic</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   5xcyy2h        </span> ^netcdf-fortran<span class="spack-cyan">@4.5.2</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~doc+pic+shared build_system=autotools patches=b050dbd</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   py3awb7        </span> ^oasis3-mct<span class="spack-cyan">@git.access-esm1.5_2024.05.24=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~deterministic\~optimisation_report build_system=makefile</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   yfo7fum            </span> ^hdf5<span class="spack-cyan">@1.10.11</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~cxx\~fortran+hl\~ipo\~java+mpi+shared\~szip\~threadsafe+tools api=default build_system=cmake build_type=Release generator=make</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[e] </span> <span class="spack-grey keep-blanks">vc4y4c6                </span> ^cmake<span class="spack-cyan">@3.24.2</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~doc+ncurses+ownlibs build_system=generic build_type=Release</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   ugenh6g                </span> ^pkgconf<span class="spack-cyan">@2.2.0</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   h45fvyw                </span> ^zlib-ng<span class="spack-cyan">@2.1.6</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">+compat+new_strategies+opt+pic+shared build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[e] </span>  <span class="spack-grey keep-blanks">ikhujrk        </span> ^openmpi<span class="spack-cyan">@4.0.2</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~atomics\~cuda\~cxx\~cxx_exceptions\~gpfs\~internal-hwloc\~internal-libevent\~internal-pmix\~java\~legacylaunchers\~lustre\~memchecker\~openshmem\~orterunprefix\~romio+rsh\~singularity\~static+vt+wrapper-rpath build_system=autotools fabrics=none patches=073477a,60ce20b romio-filesystem=none schedulers=none</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[e] </span>  <span class="spack-grey keep-blanks">mqjolvb    </span> ^glibc<span class="spack-cyan">@2.28</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   oopqoqg    </span> ^mom5<span class="spack-cyan">@git.access-esm1.5_2024.08.23=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~deterministic\~optimisation_report+restart_repro build_system=makefile type=ACCESS-CM</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   725rz7c        </span> ^netcdf-c<span class="spack-cyan">@4.7.4</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~blosc\~byterange\~dap\~fsync\~hdf4\~jna+mpi\~nczarr_zip+optimize\~parallel-netcdf+pic+shared\~szip\~zstd build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   udr7pbn    </span> ^um7<span class="spack-cyan">@git.2024.07.03=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=generic optim=high</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   qy5w2d7        </span> ^dummygrib<span class="spack-cyan">@1.0</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=makefile</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   ho2ie66        </span> ^fcm<span class="spack-cyan">@2021.05.0</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=generic site=none</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   xalavwv        </span> ^gcom4<span class="spack-cyan">@git.2024.05.28=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">+mpi build_system=generic</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
</terminal-window>

### Install the Spack environment

To compile the packages in the `mom5_dev` environment, run:
```
spack install 
```

!!! tip
    It takes 30-40 minutes to compile all the packages for the first time.<br>
    Subsequent installations, however, will compile quicker as the built dependencies are reused.

<terminal-window>
  <terminal-line data="input" directory="[mom5_dev]" class="spack">spack install</terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">glibc-2.28-mqjolvbeskcnhz5chvtdshk4x4sfnycs</span> <span class="bold">[1/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/glibc-2.28-mqjolvbeskcnhz5chvtdshk4x4sfnycs
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">cmake-3.24.2-vc4y4c64s55j5u6kp37ciw2hcghuxhhc</span> <span class="bold">[2/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/cmake-3.24.2-vc4y4c64s55j5u6kp37ciw2hcghuxhhc
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">openmpi-4.0.2-ikhujrkyukytbkxxyk3mub44v63vuzfz</span> <span class="bold">[3/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/openmpi-4.0.2-ikhujrkyukytbkxxyk3mub44v63vuzfz
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">gmake-4.4.1-j6yscmmcn3qws7n35klote7rivw7foa6</span> <span class="bold">[4/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/gmake-4.4.1-j6yscmmcn3qws7n35klote7rivw7foa6
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">fcm-2021.05.0-ho2ie66tizhxpjjiilnrjnlnbi6safwq</span> <span class="bold">[5/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/fcm-2021.05.0-ho2ie66tizhxpjjiilnrjnlnbi6safwq
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">zlib-ng-2.1.6-h45fvywj47wc4uwa37mfzkdsqrgcqxux</span> <span class="bold">[6/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/zlib-ng-2.1.6-h45fvywj47wc4uwa37mfzkdsqrgcqxux
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">dummygrib-1.0-qy5w2d7tmsbmvnqng2xlopdkd4m2grvb</span> <span class="bold">[7/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/dummygrib-1.0-qy5w2d7tmsbmvnqng2xlopdkd4m2grvb
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">pkgconf-2.2.0-ugenh6g4dnhti4p6ktbkfku6pzlq5fkr</span> <span class="bold">[8/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/pkgconf-2.2.0-ugenh6g4dnhti4p6ktbkfku6pzlq5fkr
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">gcom4-git.2024.05.28=access-esm1.5-xalavwvyp3jv6emsnj7yecrqprwp3kag</span> <span class="bold">[9/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/gcom4-git.2024.05.28_access-esm1.5-xalavwvyp3jv6emsnj7yecrqprwp3kag
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">hdf5-1.10.11-yfo7fumh2agj6itfzqa6l2dpccrypp2l</span> <span class="bold">[10/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/hdf5-1.10.11-yfo7fumh2agj6itfzqa6l2dpccrypp2l
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">netcdf-c-4.7.4-725rz7cn7qupsi4egyeaix2crssvtoxp</span> <span class="bold">[11/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/netcdf-c-4.7.4-725rz7cn7qupsi4egyeaix2crssvtoxp
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">netcdf-fortran-4.5.2-5xcyy2h34vaq77ouwsgd6lfes5zycoii</span> <span class="bold">[12/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/netcdf-fortran-4.5.2-5xcyy2h34vaq77ouwsgd6lfes5zycoii
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">oasis3-mct-git.access-esm1.5_2024.05.24=access-esm1.5-py3awb76nw3lwjw5ea3uktmh2nm254gi</span> <span class="bold">[13/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/oasis3-mct-git.access-esm1.5_2024.05.24_access-esm1.5-py3awb76nw3lwjw5ea3uktmh2nm254gi
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">mom5-git.access-esm1.5_2024.08.23=access-esm1.5-oopqoqgqu65cybqht23l6m6coxbrpzqh</span> <span class="bold">[14/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/mom5-git.access-esm1.5_2024.08.23_access-esm1.5-oopqoqgqu65cybqht23l6m6coxbrpzqh
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">um7-git.2024.07.03=access-esm1.5-udr7pbnflpwzuawejuuc4xpmfuwtpc4x</span> <span class="bold">[15/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/um7-git.2024.07.03_access-esm1.5-udr7pbnflpwzuawejuuc4xpmfuwtpc4x
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">cice4-git.2024.05.21=access-esm1.5-hhtnigwxdyz7ta4dv3gvhwulze6hxqra</span> <span class="bold">[16/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/cice4-git.2024.05.21_access-esm1.5-hhtnigwxdyz7ta4dv3gvhwulze6hxqra
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">access-esm1p5-git.2024.05.1=2024.05.1-nkvasig2zrq2ocz6evva6bmurdq7nh3h</span> <span class="bold">[17/17]</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/access-esm1p5-git.2024.05.1_2024.05.1-nkvasig2zrq2ocz6evva6bmurdq7nh3h
  </terminal-line>
  <terminal-line>
    <span class="spack-indigo bold">\==></span> Updating view at /g/data/$PROJECT/$USER/spack/0.22/environments/mom5_dev/.spack-env/view
  </terminal-line>
</terminal-window>

!!! info
    The full output has been truncated for brevity.

## Create development package

When you develop a package within a _Spack_ environment, _Spack_ needs to know that the desired package is marked as "in development", and be able to access its source code.<br>
This is done through the [`spack develop`](https://spack.readthedocs.io/en/latest/command_index.html#spack-develop) command.

### Mark package as a development package

There are, in general, two cases that influence the command to run to mark a package as "in development", depending on the state of the package's source code:

1. [The new source code does not exist](#code-does-not-exist)
2. [The new source code already exists](#code-exists)

#### The new source does not exist {: id='code-does-not-exist'}
If the source code does not yet exist, _Spack_ can create it as a copy of the current source code version, that can be later modified.

In this case we need to tell _Spack_ which package tag (version) to copy as a starting base for the new source code.

For example, to retrieve the tag for the original `mom5` package used in the `mom5_dev` _Spack_ environment, we can run:
```
spack find mom5
```
<terminal-window>
  <terminal-line data="input" directory="[mom5_dev]" class="spack">spack find mom5</terminal-line>
  <terminal-line>mom5@git.access-esm1.5_2024.08.23=access-esm1.5</terminal-line>
</terminal-window> 

To mark `mom5` as a development package and copy the `mom5@git.access-esm1.5_2024.08.23=access-esm1.5` version as a base copy, we can run:

```
spack develop mom5@git.access-esm1.5_2024.08.23=access-esm1.5
```
!!! tip
    This command should not display any output

This adds the following lines at the end of the `spack.yaml` file inside the [environment's folder](#spack-environment-folder):
```
develop:
    mom5:
      spec: mom5@git.access-esm1.5_2024.08.23=access-esm1.5
```
The source code is automatically copied inside the [environment's folder](#spack-environment-folder).

!!! warning
    As opposed to the case where the [code already exists](#code-exists), there is no `path` specification inside the `develop` portion of the `spack.yaml` environment configuration file.<br>
    This means that _Spack_ automatically expects to find the source code inside the [environment's folder](#spack-environment-folder).

#### The new source code already exists {: id='code-exists'}
If the new source code for the development package already exists in the filesystem (this also includes cases when the new source code is in a `git` repo that can be cloned), to mark a package as "in development" we will need two things:

- A name for the package's new tag
- The path of the new source code

For example, to mark `mom5` as a development package using the `development_version` tag and with the new source code in the `/path/to/mom5/new/source/code` folder, we can run:
```
spack develop --path /path/to/mom5/new/source/code mom5@development_version
```
!!! tip
    This command should not display any output

This adds the following lines at the end of the `spack.yaml` file inside the [environment's folder](#spack-environment-folder):
```
develop:
    mom5:
      spec: mom5@=development_version
      path: /path/to/mom5/new/source/code
```

!!! warning
    Care needs to be taken when multiple _Spack_ development environments point to the same source code location. If these environments require different independent changes of the source code, the user needs to make sure to sync the source code version (e.g., using different `git` branches for the different versions of the source code) with the desired one when switching between development environments.<br>
    This would still prevent building both environments simultaneously.


## Compile modified Spack environment packages

After setting a development package the _Spack_ environment needs to be re-concretized (because the `spack.yaml` file changed). The _Spack_ environment can be concretized following the same steps listed in [Concretize the Spack environment](#concretize-the-spack-environment). Then, the new package can be built following the steps listed in [Compile Spack environment packages](#compile-spack-environment-packages-optional):

```
spack concretize -f --fresh
spack install
```
!!! warning
    Although this time the `spack install` command will only build the development package, it might still take a long time to complete, depending on the specific package.

<terminal-window lineDelay=0>
  <terminal-line data="input" lineDelay=200 directory="[mom5_dev]" class="spack">
    spack concretize -f --fresh
  </terminal-line>
  <terminal-line lineDelay=2000>
    <span class="spack-indigo bold">\==></span> Concretized access-esm1p5@git.2024.05.1=2024.05.1
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   aysea5r</span>  access-esm1p5<span class="spack-cyan">@git.2024.05.1=2024.05.1</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=bundle</span> <span class="spack-pink"><span class="spack-pink">arch=linux-rocky8-x86_64_v4</span></span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">hhtnigw    </span> ^cice4<span class="spack-cyan">@git.2024.05.21=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=makefile</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">j6yscmm        </span> ^gmake<span class="spack-cyan">@4.4.1</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~guile build_system=generic</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">5xcyy2h        </span> ^netcdf-fortran<span class="spack-cyan">@4.5.2</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~doc+pic+shared build_system=autotools patches=b050dbd</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">py3awb7        </span> ^oasis3-mct<span class="spack-cyan">@git.access-esm1.5_2024.05.24=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~deterministic\~optimisation_report build_system=makefile</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">yfo7fum            </span> ^hdf5<span class="spack-cyan">@1.10.11</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~cxx\~fortran+hl\~ipo\~java+mpi+shared\~szip\~threadsafe+tools api=default build_system=cmake build_type=Release generator=make</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[e] </span> <span class="spack-grey keep-blanks">vc4y4c6                </span> ^cmake<span class="spack-cyan">@3.24.2</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~doc+ncurses+ownlibs build_system=generic build_type=Release</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">ugenh6g                </span> ^pkgconf<span class="spack-cyan">@2.2.0</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">h45fvyw                </span> ^zlib-ng<span class="spack-cyan">@2.1.6</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">+compat+new_strategies+opt+pic+shared build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[e] </span>  <span class="spack-grey keep-blanks">ikhujrk        </span> ^openmpi<span class="spack-cyan">@4.0.2</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~atomics\~cuda\~cxx\~cxx_exceptions\~gpfs\~internal-hwloc\~internal-libevent\~internal-pmix\~java\~legacylaunchers\~lustre\~memchecker\~openshmem\~orterunprefix\~romio+rsh\~singularity\~static+vt+wrapper-rpath build_system=autotools fabrics=none patches=073477a,60ce20b romio-filesystem=none schedulers=none</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[e] </span>  <span class="spack-grey keep-blanks">mqjolvb    </span> ^glibc<span class="spack-cyan">@2.28</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-grey keep-blanks"> -   oopqoqg    </span> ^mom5<span class="spack-cyan">@git.access-esm1.5_2024.08.23=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~deterministic\~optimisation_report+restart_repro build_system=makefile dev_path=/path/to/source/code/for/mom5 type=ACCESS-CM</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">725rz7c        </span> ^netcdf-c<span class="spack-cyan">@4.7.4</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">\~blosc\~byterange\~dap\~fsync\~hdf4\~jna+mpi\~nczarr_zip+optimize\~parallel-netcdf+pic+shared\~szip\~zstd build_system=autotools</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">udr7pbn    </span> ^um7<span class="spack-cyan">@git.2024.07.03=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=generic optim=high</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">qy5w2d7        </span> ^dummygrib<span class="spack-cyan">@1.0</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=makefile</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">ho2ie66        </span> ^fcm<span class="spack-cyan">@2021.05.0</span><span class="spack-green">%intel@19.0.3.199</span> <span class="spack-indigo">build_system=generic site=none</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line>
    <span class="spack-green">[+]</span> <span class="spack-grey keep-blanks">xalavwv        </span> ^gcom4<span class="spack-cyan">@git.2024.05.28=access-esm1.5</span><span class="spack-green">%intel@19.0.3.199</span><span class="spack-indigo">+mpi build_system=generic</span> <span class="spack-pink">arch=linux-rocky8-x86_64_v4</span>
  </terminal-line>
  <terminal-line data="input" directory="[mom5_dev]" lineDelay=200 class="spack">spack install</terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/glibc-2.28-mqjolvbeskcnhz5chvtdshk4x4sfnycs
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/cmake-3.24.2-vc4y4c64s55j5u6kp37ciw2hcghuxhhc
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/openmpi-4.0.2-ikhujrkyukytbkxxyk3mub44v63vuzfz
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/gmake-4.4.1-j6yscmmcn3qws7n35klote7rivw7foa6
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/fcm-2021.05.0-ho2ie66tizhxpjjiilnrjnlnbi6safwq
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/zlib-ng-2.1.6-h45fvywj47wc4uwa37mfzkdsqrgcqxux
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/dummygrib-1.0-qy5w2d7tmsbmvnqng2xlopdkd4m2grvb
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/pkgconf-2.2.0-ugenh6g4dnhti4p6ktbkfku6pzlq5fkr
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/gcom4-git.2024.05.28_access-esm1.5-xalavwvyp3jv6emsnj7yecrqprwp3kag
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/hdf5-1.10.11-yfo7fumh2agj6itfzqa6l2dpccrypp2l
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/netcdf-c-4.7.4-725rz7cn7qupsi4egyeaix2crssvtoxp
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/netcdf-fortran-4.5.2-5xcyy2h34vaq77ouwsgd6lfes5zycoii
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/oasis3-mct-git.access-esm1.5_2024.05.24_access-esm1.5-py3awb76nw3lwjw5ea3uktmh2nm254gi
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">mom5-git.access-esm1.5_2024.08.23=access-esm1.5-l34w7is54xzer7s4ztvb5ymgjbtduknh</span> <span class="bold">[14/17]</span>
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/mom5-git.access-esm1.5_2024.08.23_access-esm1.5-l34w7is54xzer7s4ztvb5ymgjbtduknh
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/um7-git.2024.07.03_access-esm1.5-udr7pbnflpwzuawejuuc4xpmfuwtpc4x
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/cice4-git.2024.05.21_access-esm1.5-hhtnigwxdyz7ta4dv3gvhwulze6hxqra
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-indigo bold">\==></span> <span class="bold">Installing</span> <span class="spack-green">access-esm1p5-git.2024.05.1_2024.05.1-nkvasig2zrq2ocz6evva6bmurdq7nh3h</span> <span class="bold">[17/17]</span>
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-green">[+]</span> /g/data/$PROJECT/$USER/spack/0.22/restricted/ukmo/release/linux-rocky8-x86_64_v4/intel-19.0.3.199/access-esm1p5-git.2024.05.1_2024.05.1-nkvasig2zrq2ocz6evva6bmurdq7nh3h
  </terminal-line>
  <terminal-line lineDelay=200>
    <span class="spack-indigo bold">\==></span> Updating view at /g/data/$PROJECT/$USER/spack/0.22/environments/mom5_dev/.spack-env/view
  </terminal-line>
</terminal-window>

!!! info
    The full output has been truncated for brevity.

!!! tip
    From now on, the source code can be modified and the _Spack_ environment installed without repeating the concretization step.<br>
    The _Spack_ environment will need to be re-concretized only if further changes occur in the `spack.yaml` file.

## Output directory for compiled packages
For the Spack instance obtained through the [Spack setup instructions]({{spack_setup}}), all compiled packages will be placed in directories having the following format: `<install_tree.root>/<architecture>/<compiler.name>-<compiler.version>/<name>-<version>-<hash>`.

`<install_tree.root>` depends on the [`install_tree.root`](https://spack.readthedocs.io/en/latest/config_yaml.html#install-tree-root) configuration field. _Spack_ reads this configuration field from files in several directories, following [Spack's configuration scopes](https://spack.readthedocs.io/en/latest/configuration.html#configuration-scopes).

!!! warning
    For instances of _Spack_ on _Gadi_ you should ignore the **system** scope.

For the example above, `mom5_dev` _Spack_ environment's configuration file (`spack.yaml`) contains the following lines that fall in the **environment** scope:
```yaml
config:
    install_tree:
      root: $spack/../restricted/ukmo/release
```
!!! tip
    For the _Spack_ instance obtained through the [Spack setup instructions]({{spack_setup}}), `$spack` (referred to as `$(prefix)` in _Spack_ configuration scopes documentation above) corresponds to the `/g/data/$PROJECT/$USER/spack/0.22/spack` directory.

This means the packages built in this example can be found in `/g/data/$PROJECT/$USER/spack/0.22/spack/../restricted/ukmo/release/<architecture>/<compiler.name>-<compiler.version>/<name>-<version>-<hash>`.

## Troubleshooting build errors

Sometimes you might encounter errors while compiling the packages.<br>
_Spack_ prints out the error message and generates a full build log that can be viewed by the user. The location of the build log is shown at the end of the error message.

For example, if we try to install the `mom5_dev` environment with an error in the new `mom5` source code (in this example a `use` statement in the `<new-mom5-source-code-folder>/src/accessom_coupler/ocean_solo.F90` file has been purposely commented out to force an error in compilation), we might get an output error similar to the following:

<pre><code><spack class="spack-grey bold">...</spack>
<span class="spack-red"> >> 415    /g/data/$PROJECT/$USER/spack/0.22/environments/mom5_dev/mom5/src/access_coupler/ocean_solo.F90:224: undefined reference to `constant s_init_'</span>
<span class="spack-red"> >> 416    make: *** [Makefile:931: fms_ACCESS-CM.x] Error 1</span>
<spack class="spack-grey bold">...</spack>
See build log for details:
  /scratch/$PROJECT/$USER/tmp/path/to/the/spack-stage-mom5-git.access-esm1.5_2024.08.23_access-esm1.5-l34w7is54xzer7s4ztvb5ymgjbtduknh/spack-build-out.txt
<spack class="spack-grey bold">...</spack>
<span class="spack-red">==></span> Error: access-esm1p5-git.2024.05.1=2024.05.1-aysea5r7rbwy22lluvl64baperlokktv: Package was not installed
<span class="spack-red">==></span> Error: Installation request failed.  Refer to reported errors for failing package(s).</code></pre>

If the error is not obvious from the error message, see the build log for more information.

<custom-references>
- [https://spack.readthedocs.io/en/latest/](https://spack.readthedocs.io/en/latest/)
- [https://spack-tutorial.readthedocs.io/en/latest/tutorial_developer_workflows.html](https://spack-tutorial.readthedocs.io/en/latest/tutorial_developer_workflows.html)
</custom-references>
