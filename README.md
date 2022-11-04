# Build System Framework User-Manual

This guide is intended for those who are familiar with the UNIX command-line interface but have no prior experience with BSF.

## Prerequisites

### Ruby Instalation

It is necessary to install ruby for the execution of BSF. On Debian or Ubuntu, executing the following command should suffice:

```sh
    sudo apt-get install ruby-full build-essential
```


#### Dependecies
BSF depends on a few ruby gems to be operacional. Execute the following command to install the gem dependencies:

    gem install erb json sinatra terminal-table

Or execute a GemFile within the framework by:

    sudo gem install bundler
    bundle install



Visit for more information:

    https://www.ruby-lang.org/en/documentation/installation/#apt


<!-- ### RUBYLIB Environment Variable

To correctly execute the framework it is necessary to define the RUBYLIB environment variable to the framework lib folder.

Execute the following command and replace `<bsf-path>` to the framework path folder you stored it.

    $ export RUBYLIB=<bsf-path>/lib:$RUBYLIB -->


## Introduction
<!-- Similar to the Git command-line interface, BSF operates with independent command execution. Each command is executed in its own process. Accordingly, each command has a specific scope and can only access the variables it requires. -->

<!-- Similar to the Git command-line interface, BSF operates with independent command execution. Each command is executed in it's own process. As a result, each command has it's own scope and can only access the variables it requires. -->
BSF is a framework for automating task execution that which includes the capability to compare prior execution iteration results to track the development modifications.

Similar to the Git command-line interface, BSF operates with independent command execution. Each command is executed in its own process and as a result, it has its own scope and can only access the variables it requires.

To simplify the execution of commands, there are certain rules to be followed. 
The syntax of a command consists of three elements: 
    

<!-- 1) Character `<>` delimiting a variable is a mandatory argument;
2) Character `{}` delimiting a variable is a optional argument;
3) Character `$`  precendently a variable can be treated as multiple arguments; -->



|  Symbol  | Example                            | Description                                                             |
|:--------:|:-----------------------------------|-------------------------------------------------------------------------|
|   `< >`   | $ bsf sources delete `<`source`>` | Character delimiting a variable is a mandatory argument                 |
|   `{ }`   | $ bsf sources get \$`{`source`}`  | Character delimiting a variable is a optional argument                  |
|    `$`    | $ bsf sources get `$$`{source}    | Character preceding a variable can be treated as multiple arguments  |


<!-- 
|  Symbol  |  Representation                   | Example |
|:--------:|:----------------------------------|-----------------------------|
|   `< >`   | Mandatory Argument                | $ bsf sources delete `<`source`>`
|   `{ }`   | Option Argument                   | $ bsf sources get \$`{`source`}`
|    `$`    | Capability of multiple Arguments  | $ bsf sources get `$$`{source}


|  Symbol  |  Representation  |
|:--------:|:-----------------|
|    { }   | Option Argument  |
|    < >   | Mandatory Argument  |
|     $    | Capability of multiple Arguments  | -->

## Configuration File
The configuration for BSF is pretty straight forward, although there are some rules to be followed in order to work with BSF. In the `config.json` file are defined a set of parameters which are mandatory for its funcionality.

The following examples demonstrastes the structure of the JSON format configuration file

```json
{
    "sources": {
        "source1": {
            "repo": "",
            "branch": "" // Not Mandatory
        }
    },
    "tasks": {
         "task1": {
            "description": "",
            "pre_condition": "",
            "execute": "",
            "publish_header": "",
            "comparator": "",
            "report": ""  // Not Mandatory
        }
    } 
}
```

### Sources
What is a Source? 

A Source is a repository (such as arc-gnu-toolchain, gcc, etc) that needs to be cloned in order to execute a certain task. A task may depend on a specific repository to operate. These sources have to be defined within the `config.file` and cannot be modified later on.

In the example below we have a `sources` parameter within a `config.json` file. In the parameter we have 2 sources, arc-gnu-toolchain and gcc. It is possible to specify a branch or leave it as the default.

```json
{
    "sources": {
        "arc-gnu-toolchain": {
            "repo":"https://github.com/foss-for-synopsys-dwc-arc-processors/arc-gnu-toolchain",
            "branch": "arc64"
        },
        "gcc": {
            "repo":"https://github.com/foss-for-synopsys-dwc-arc-processors/gcc"
        }
    }
    ...
}
```


### Tasks
What is a task?

<!-- A task is where is defined a execution of a certain operation. Each task contains parameters to a successful execution. Each parameter, except for description which takes single string, takes bash command as input, single or array of strings if you wish. This is intended as the core execution, for instance the tests, are not ruby dependent and can execute different types of scripts through bash commands invokes. These srcipts must be within the config folder of on the initialization of BSF. -->
A task is a defined execution of a certain operation. Each task comprises parameters that must be satisfied in order for the task to be completed successfully. Except for description, which accepts a single string, each parameter accepts a bash command as input, which can be a single string or an array of strings. This is designed since the core execution, for example, the tests, are not ruby dependant and may execute many types of scripts using bash command invocations. On BSF's initialization, these scripts must be in the config folder.

```json
{
    "tasks": {
        "task": {
            "description": "",
            "pre_condition": "",
            "execute": "",
            "publish_header": "",
            "comparator": "",
            "report": ""
        }
    }
}
```

### Description Parameter
The *description* parameter is essentially a mechanism to describe the current task's purpose in more depth. When executing `$ bsf tasks` command to display a list of all tasks in the system, this parameter's value, which accepts a string as an input, will be displayed.

### Pre-Conditions Parameter
<!-- The Pre-Condition parameter is a parameter that execute a N number of validations for the current task, if the dependencies to the current task is verified, the execution will precced. Otherside it will print the fail. -->
The *Pre-Condition* parameter performs N different validations when executing `$ bsf execute` for the current task; if the dependencies are confirmed, execution will proceed. If not, the failure will be printed. It accepts either a single string or an array of strings as input.

### Execute Parameter
<!-- The *Execute* parameter is responsable for the main purpose of the task. Here is where is defined all operations, for instance, the procedures for the hs dejagnu baremetal testing. It accepts a single string or array of strings as input and bash as language. This means that the actual task's execution is not Ruby dependent and can invoke independent scripts if needed. -->
The true objective of the tasks is handled by the *Execute* parameter. All procedures, such as the steps for the hs dejagnu baremetal testing, are defined here and executed within an unique workspace folder. It takes bash as the language and a single string or array of strings as input. This implies that the execution of the job itself is independent of Ruby and may, if necessary, call independent scripts.

### Publish Header Parameter
<!-- This parameter is responsble for defining the commit message to be stored in the git commit through the `bsf publish` command. It is mandatory to be a JSON format as input and capable of accepting multiple JSON format as the BSF will contactenate all JSON into one single JSON. -->

The *Publish Header* parameter is in control of stablishing the commit message that will be stored in the git commit through the `bsf publish` command. It must take a JSON format as input and can accept several JSON formats since BSF will concatenate all JSON into one unified JSON format.

### Comparator Parameter
<!-- The Comparator parameter is in charge of executing the command that is responsable for creating the comparasion between past execution iterations. There's a Internal Variable ($var(@OPTIONS)) that are defined by BSF with a specific syntax accordingly with the input provided in the execution of `bsf comparator` command. See *REFERENCIA* for more information regarding this. -->

The *Comparator* parameter performs the appropriate operation for the comparison between prior execution iterations. There is an Internal Variable ($var(@OPTIONS)) that is defined by BSF with a specific syntax based on the input supplied in the `bsf comparator` command execution. More information may be found at *REFERENCIA*.

### Report Parameter
The *Report* parameter is responsible for executing a bash command for the task's report. There is an Internal Variable ($var(@OPTIONS)) that is defined by BSF with a specific syntax based on the input supplied in the `bsf report` command execution. More information may be found at *REFERENCIA*.


## Framework Initialization


BSF is initialized with a folder containing a mandatory `config.json` file that defines all of it's configuration and execution. Other dependencies, such as a script that is invoked from the `config.json` file, must also be present in the folder.

<!-- The framework initialization depends on the folder containing a mandatory `config.json` with all the tasks' configuration and execution. 
The folder can also contaion other type of dependencies, for instance, a script that is called within the `config.json` file. -->

The prebuilt configuration for BSF can be found in the following link

    https://github.com/luiss-synopsys/tbsf_config


<!-- To initialize the framework by specifying a folder execute the following command.

    $ bsf init <folderpath>

| Example                                       | Definition                                      |
|-----------------------------------------------|-------------------------------------------------|
| *$ bsf init /home/user/folder*                | Initializes BSF from given folder path          | -->

<!-- Another alternatively is to clone a BSF environment. -->

<!-- Test results can be shared among developers. It is possible to clone a developer's results when they are published in a git repository so that you could work in the same environment. To do so, execute the following command. -->

Another approach is to clone a BSF environment. This helps to share test results among developers, as well as reproduce a developer's work in the same environment on which it was originally conducted. To so do, execute the following command.

```sh
    $ bsf clone <gitrepo> <dir>

    $ bsf clone https://github.com/user/repo /home/user/BSF     
```

| Example                                                       | Definition                                                       |
|---------------------------------------------------------------|------------------------------------------------------------------|
| *$ bsf clone https://github.com/user/repo* /home/user/BSF     | Clones BSF Environment from Git Repository to the given path     |


## Variables

BSF supports a specific type of variables within the `config.json` file. This variables allow the exectutions to be modified without altering the `config.json` file.

There are two types of variables: 1) Input Variables, which are defined after BSF has been initialized; and 2) Internal Variables, which are defined internally by the framework.

| Varaible  |   Example   | Description                                  |
|-----------|-------------|----------------------------------------------|
| Input     |  $var(VAR)  | Variables to be defined                      |
| Internal  |  $var(@VAR) | Variables defined internally by the framework |

### Internal variables
| Variable                   | Definition                                                   |
|----------------------------|--------------------------------------------------------------|
| $var(@ROOT)                | Path to framework environment                                |
| $var(@SOURCE)              | Path to all the repositories                                 |
| $var(@WORKSPACE)           | Path to all the tasks' execution files                       |
| $var(@CONFIG_SOURCE_PATH)  | Path to the internal config folder used in the initizalition |


To list all input variables in the `config.json` file execute the following command

    $ bsf vars

To define the Input Variables execute the following command specifying the variable to define and it's value.

    $ bsf set <var>=<value>

<!-- Example: *bsf set PREFIX=/home/user/install* -->
| Example                                       | Definition                                      |
|-----------------------------------------------|-------------------------------------------------|
| *$ bsf set PREFIX=/home/user/install*         | Initializes input variable                      |

# Commands

## Start a working BSF Environment
BSF is initialized with a folder containing a mandatory config.json file, the one previously mentioned. Other dependencies, such as a script that is invoked from the `config.json` file, must also be present in the folder.

The example that follows illustrates a potential folder's contents for BSF's initialization.
```
folder
├── config.json
├── scripts
│   ├──compare.rb
│   └── report.rb
├── site-exp
│   └── report.rblinux-qemu.siteexp.erb
```

To initialize BSF by specifying the folder containing the `config.json`, execute the following command.
    
```sh
$ bit init <folderpath>


$ bsf init /home/user/folder        # Initializes BSF from given folder path
```
<!-- | Example                                       | Definition                                      |
|-----------------------------------------------|-------------------------------------------------|
| *$ bsf init `/home/user/folder`*              | Initializes BSF from given folder path          | -->

-------

Another approach is to clone a BSF environment. This helps to share test results among developers, as well as reproduce a developer's work in the same environment on which it was originally conducted. To so do, execute the following command.
```sh
$ bsf clone <gitrepo> <dir>


$ bsf clone https://github.com/user/repo /home/user/BSF     # Clones BSF Environment from Git Repository to the given path
```

<!-- | Example                                                       | Definition                                                       |
|---------------------------------------------------------------|------------------------------------------------------------------|
| *$ bsf clone `https://github.com/user/repo` `/home/user/BSF`* | Clones BSF Environment from Git Repository to the given path     |
 -->


## Work with Sources

### Clone a Source
To clone a source that is defined in the `config.json` file. Execute the following command
```sh
$ bsf sources get ${name} 


$ bsf sources get                       # Clones all sources in the config.json file
$ bsf sources get gcc                   # Clones gcc source into the BSF Environment
$ bsf sources get gcc toolchain         # Clones gcc & toolchain into the BSF Environment
```
<!-- | Example                                       | Definition                                      |
|-----------------------------------------------|-------------------------------------------------|
| *$ bsf sources get*                           | Clones all sources in the config.json file      |
| *$ bsf sources get `gcc`*                       | Clones gcc source into the BSF Environment      |
| *$ bsf sources get `gcc toolchain`*             | Clones gcc & toolchain into the BSF Environment | -->

-----
### Delete a Source
To delete a Source after its clonning, execute the following command.
This command will remove the given source's folder within the sources folder in the BSF environment.
```sh
$ bsf sources delete <name>


$ bsf sources delete gcc                # Deletes gcc source from the BSF Environment
```

<!-- | Example                                       | Definition                                      |
|-----------------------------------------------|-------------------------------------------------|
| *$ bsf sources delete `gcc`*                       | Deletes gcc repository from the BSF environment | -->

-----
### List all Sources in config.json
To list all the Sources in the `config.json` execute the following command.
```sh
$ bsf sources list 
```
----
### Show all cloned Sources
Show all cloned the Sources in the BSF environment execute the following command.
```sh
$ bsf sources show
```



## Work with Input Variables
### Define Input Variable 
To define the Input Variables execute the following command specifying the variable to define and it's value.
```sh
    $ bsf set <var>=<value>


    $ bsf set PREFIX=/home/user/install             # Defines Input Variable PREFIX
```

<!-- | Example                                       | Definition                                      |
|-----------------------------------------------|-------------------------------------------------|
| *$ bsf set `PREFIX`=`/home/user/install`*     | Initializes input variable                      | -->


### List all Input Variables

To list all input variables in the `config.json` file execute the following command

    $ bsf vars


## Work with tasks
To start a task's execution, execute the following command
```sh
    $ bsf execute ${task}

    $ bsf execute                                   # Executes all tasks
    $ bsf execute hs-gcc-baremetal                  # Executes hs-gcc-baremetal task
    $ bsf execute hs-gcc-baremetal hs-gcc-linux     # Executes hs-gcc-baremetal & hs-gcc-linux tasks
```
<!-- 
| Example                                       | Definition                                     |
|-----------------------------------------------|------------------------------------------------|
| *$ bsf execute*                               | Executes all tasks                             |
| *$ bsf execute hs-gcc-baremetal*              | Executes hs-gcc-baremetal task                 |
| *$ bsf execute hs-gcc-baremetal hs-gcc-linux* | Executes hs-gcc-baremetal & hs-gcc-linux tasks | -->

------

### Check a log of a task

The `log` command executes an internal `cat` unix command, as the `-f` flag enables an internal `tail` command of the same log file, providing you the log in real time.


<!-- In order to print the log file that was generated by the framework, execute the following command. 

The `log` command executes an internal `cat` unix command, as the `-f` flag enables a tail command of the same log file, allowing you to follow the log in real time.

The `log` command executes an internal `cat` unix command and when given `-f` flag it enables a tail of that same log file, that way it is possible to follow the log in real time.

In order to check the log of a specific task, generated by BSF, execute the following command.
The `log` command executes a internal cat unix command and using the `-f` flag enables a tail of that same log file. That way it is possible to follow the log in realtime -->

```sh
    $ bsf log {-f} <task>


    $ bsf log hs-gcc-baremetal                  # Prints the log file of hs-gcc-baremetal task
    $ bsf log -f hs-gcc-baremetal               # Follows the log file of hs-gcc-baremetal task
```
<!-- | Example                       | Definition                                                   |
    |-------------------------------|--------------------------------------------------------------|
    | *$ bsf log hs-gcc-baremetal*    | Prints the log file of hs-gcc-baremetal task                 |
    | *$ bsf log -f hs-gcc-baremetal* | Follows the log file of hs-gcc-baremetal task                | -->

----

### Check tasks' status
Each task contains a return status on if the execution was successful or failed. To check the current status execute the following command:

```sh
    $ bsf status
```

-----
## Internal Git 
### Git Commands
In order to access the internal git repository inside the framework you can acomplish that by executing the following command. 

The `${gitcommands}` represents native git commands that will be executed within the framework git environment.
```sh
    $ bsf git ${gitcommands}


    $ bsf git init                  # Initializes a Git Repository inside the BSF Environment
    $ bsf git log                   # Prints the log of the BSF Git Repository
```
<!-- Example:
1. *bsf git init*
2. *bsf git log* -->


<!-- | Example                       | Definition                                                   |
|-------------------------------|--------------------------------------------------------------|
| *$ bsf git init*              | Initializes a Git Repository inside the BSF environment      |
| *$ bsf git log*               | Prints the log of the BSF Git Repository                     | -->

-----
### Publish results
To publish each execution iteration of a single or multiple tests, execute the following command.

*You must initilize a internal git repository if first time* 
```sh
    $ bsf publish
```
---
### Search Commit Messages
To search on each commit message of the internal git repository execute the following command by passing the key and value you wish to match to.
```sh
    $ bsf search $<var>=<value>


    $ bsf search build_date=20221011                    # Returns commit where the arguments are matched
    $ bsf search build_date=20221011 hash=484f32        # Returns commit where both arguments are matched 
```
<!-- | Example                                         | Definition                                                   |
|-------------------------------------------------|--------------------------------------------------------------|
| *$ bsf search build_date=20221011*              | Returns commit where the arguments are matched               |
| *$ bsf search build_date=20221011 hash=484f32*  | Returns commit where both arguments are matched              | -->

----

### List persistent folder
To list all the files and folders within the persistent workspace execute the following command

```sh
    $ ls <task> {commit_id}


    $ bsf ls hs-gcc-baremetal               # Returns list of files within hs-gcc-baremetal's persistent_ws, locally
    $ bsf ls hs-gcc-baremetal HEAD          # Returns list of files within hs-gcc-baremetal's persistent_ws of HEAD commit id
```

<!-- Example:
1. *bsf ls hs-gcc-baremetal HEAD*
2. *bsf ls hs-gcc-baremetal* -->

<!-- | Example                                       | Definition                                                   |
|-----------------------------------------------|--------------------------------------------------------------|
| *$ bsf ls hs-gcc-baremetal*                   | Returns list of files inside hs-gcc-baremetal's persistent_ws locally              |
| *$ bsf ls hs-gcc-baremetal HEAD*              | Returns list of files inside hs-gcc-baremetal's persistent_ws of HEAD commit id                | -->

<!-- Note: The `commit_id` is not mandatory, if missing, the local folder will be displayed. -->

----

### Print a file's content
To print a file's content within the persistent workspace, execute the following command
```sh
    $ cat <task> <file> {commit_id}

    $ bsf cat hs-gcc-baremetal gcc.sum          # Returns gcc.sum file within hs-gcc-baremetal persistent_ws, locally
    $ bsf cat hs-gcc-baremetal gcc.sum HEAD     # Returns gcc.sum file within hs-gcc-baremetal persistent_ws of HEAD commit id
```
<!-- | Example                                       | Definition                                                   |
|-----------------------------------------------|--------------------------------------------------------------|
| *$ bsf cat hs-gcc-baremetal gcc.sum HEAD*     | Returns gcc.sum file within hs-gcc-baremetal's persistent_ws of HEAD commit id              |
| *$ bsf cat hs-gcc-baremetal gcc.sum*          | Returns gcc.sum file within hs-gcc-baremetal persistent_ws locally                | -->

<!-- Note: The `commit_id` is not mandatory, if missing, the local file will be displayed. -->






-------------------------
-------------------------
-------------------------
-------------------------
-------------------------











## Capability to store past executions data for future comparisons.

### Publish current results

In order to compare past executions data it is necessary to store them. In order to do so execute the following command.

This command will execute a `git commit -am <commit_msg>` internally and pass as commit_msg all the JSON format files containing everything about the the tasks' execution. This implies tool versions, build date, etc

    $ bsf publish

### Comparasion

In order to make a comparasion between past execution iterations it is mandatory to have them stored in the framework internal git repository. After publishing the results using the previous command, we must extract the commit ids of the tests we wish to compare. We can achive this by executing the internal git log

    $ bsf git log


<!-- 
To create a comparasion between two hashs (or tasgs) execute the following command by replacing the \<hash1\> and \<hash2\> by the corresponding commit ids you wish to compare -->

To compare past executions data execute the following command.

    $ bsf compare <task> <hash1>:<hash2> ${args}
    $ bsf compare all <hash1>:<hash2> ${args}


| Example                                            | Definition                                                   |
|----------------------------------------------------|--------------------------------------------------------------|
| *$ bsf compare all 2019.09:2021.03*                | Returns all tasks' comparasion details  between 2019.09 vs 2021.03 |
| *$ bsf compare hs-gcc-baremetal 2019.09:2021.03*   | Returns hs-gcc-baremetal task comparasion details between 2019.09 vs 2021.03 |

<!-- | *$ bsf cat hs-gcc-baremetal gcc.sum*          | Returns gcc.sum file within hs-gcc-baremetal persistent_ws locally                | -->

<!-- 
Note that you can execute an agregator to all comparasion if your `config.json` has that implementation. To do so execute the following command. -->

<!-- $ bsf compare all <hash1>:<hash2> ${args} -->

<!-- | Example                                            | Definition                                                   | -->
<!-- |----------------------------------------------------|--------------------------------------------------------------| -->
<!-- | *$ bsf compare all 2019.09:2021.03*   |                                                              | -->
<!--  -->
Note: The `${args}` are arguments passed directly to the script responsable for the comparasion.

# Script capabilities
<!-- Some of the arguments you can pass to the compare script:

| parameter         | default | values                           |
|-------------------|---------|----------------------------------|
| --o               | text    | json, text                       |
| -v                | off     | on                               | 
| --v               |         | npass, nfail, atest, rtest, passfail, failpass |

Example:
1. *$ bsf compare hs-gcc-baremetal-qemu 2020.09:2021.03*
2. *$ bsf compare hs-gcc-baremetal-qemu 2020.09:2021.03 -v*
3. *$ bsf compare hs-gcc-baremetal-qemu 2020.09:2021.03 --v npass* -->
