---

layout: tutorial_hands_on
title: "ToolFactory: Generating Tools From Simple Scripts"
key_points:
  - The ToolFactory is a fully automated Galaxy tool generator for scientists and developers who routinely write command line scripts.
  - It can turn a working command line script into a proper Galaxy tool with a test in a few minutes.
  - It automatically generates simple, complete Galaxy tools from information provided by filling in a normal Galaxy form in the familiar UI.
  - It is installed as a docker-galaxy-stable flavour - the ToolFactory Appliance.
  - The Appliance is a Galaxy server running in Docker, suited to any modern Linux workstation or high-end laptop.
  - Useful for learning about system administration or framework code, and for developing tools - all on your own private pop-up server.

objectives:
 - Learn why you might want to use the ToolFactory Appliance
 - Watch a video demonstration and explore the generated code - Hello Galaxy Training Network!
 - Run it locally in Docker.
 - Install and explore the simple examples provided.
 - Modify and re-generate them to see how the changes affect the tool
 - Generate new simple Galaxy tools using your own scripts

questions:
 - What options exist for new-to-Galaxy developers to convert functioning command line scripts into Galaxy tools?
 - Can any command line script I've written be wrapped as a Galaxy tool?
 - Can I make a tool from a script developed in a Galaxy Interactive Environment notebook?
 - How can I get the ToolFactory working locally, since you tell me it should never be exposed on a public server?
 - What is the difference between a hand-crafted tool and a ToolFactory generated one?

time_estimation: 1H
subtopic: tooldev

requirements:
  - type: "internal"
    topic_name: introduction
    tutorials:
      - galaxy-intro-short
      - galaxy-intro-101-everyone
  - type: "internal"
    topic_name: dev
    tutorials:
      - tool-integration
      - interactive-environments

follow_up_training:
  - type: "internal"
    topic_name: dev
    tutorials:
      - tool-generators-advanced

contributors:
  - fubar2
  - hexylena
---

This tutorial is for developers and researchers routinely developing their own analysis scripts using bash, Python, Perl, Rscript or other scripting languages.
It shows a convenient way to bridge the gap between a working command line script and installing a real tool that "wraps" that script as a tool in Galaxy.
The ToolFactory is a Galaxy tool, run in the usual Galaxy tool interface. It is distributed as the ToolFactory Appliance, ready to run on a local linux workstation or high end laptop using Docker.

This first tutorial is an introduction. It shows how ToolFactory generates a `Hello World!` Galaxy tool and how the Appliance can be installed locally.
The reader will soon learn if it might be adapted to their work. If so, an [Advanced ToolFactory tutorial]({% link topics/dev/tutorials/tool-generators-advanced/tutorial.md %}) is
available if the material here is relevant to your needs and you would like to learn more details about the different kinds of tools and features the ToolFactory offers.

Experienced galaxy tool developers already have specialised tools and training to suit their needs so may not gain much from this material.
Users new to Galaxy from other scientific disciplines not yet familiar with the manual tool development process,
may find the ToolFactory appliance useful for familiarising themselves with tool development in Galaxy.

> ### Agenda
>
> 1. TOC
> {:toc}
>
{: .agenda}

# Tools, tool wrappers and the ToolFactory in Galaxy.

Tools are the fundamental building blocks for analyses in Galaxy. Thousands are available in the project tool library from many different kinds of science. Galaxy
tools can be created from almost any Linux command line software packages or interpreter scripts. What happens
at tool execution is decoupled from the framework itself, so the framework is agnostic to scientific discipline or coding language.
This is one reason for rapid uptake in new data intensive scientific areas. All it takes is a set of discipline specific tools to bring a whole new community
of scientists into Galaxy.

Tool execution is tightly constrained and secured. User supplied parameters and data inputs exposed on the tool form are the only things that can be changed
before execution. Everything else is fixed. This is ideal for non-programmer Galaxy users who rely on prepared tools for their work. Tools are
utterly predictable to the point of being as reproducible as any complex computing component can be and this is one of the strengths of the Galaxy framework.

## Galaxy Intereactive Environments (GIE)

In contrast to tools, GIE allow unconstrained scripting in a Galaxy environment. They offer complete freedom from the constraints of existing tools for
appropriately skilled researchers and developers, because they allow code to run inside Galaxy that is not available in any existing tool.
Notebooks can be shared and reused and can even run in workflows.

If a tool that performs exactly the same functions as a GIE script is needed, the code can be extracted and turned into a parameterised command line script.
Any functional script can be turned into a typical Galaxy tool.

## Pathways from scripts to tools

Any command line script that runs correctly with sample test data input files and parameter settings can be turned into a Galaxy tool.
These may be derived from notebook scripts that have been consolidated and rewritten to take positional or argparse style parameters and tested
in a shell using small data input files. Alternatively, skilled users can develop scripts and test them using small input data files on the command line
without using Galaxy.

The Galaxy developers support extensive software infrastructure for manually creating new tools including Planemo and the new Galaxy language server.
These are complex and powerful with substantial
learning curves but can be used to turn almost any command line software package into a tool.

For those new to Galaxy, in many simple cases, it may be possible to generate a new tool "wrapping" that script in a few minutes, using a
specialised Galaxy tool for developers that generates tools from scripts.

This tutorial introduces that unusual tool.


## The ToolFactory Appliance

The ToolFactory is an unusual but useful Galaxy tool. It implements an automated, form driven code generator that installs newly generated tools so you can try them straight away
in Galaxy. The ToolFactory is distributed as a Docker appliance and can be "popped up" and ready to run in a few minutes.

> ### {% icon tip %} The ToolFactory appliance provides a fully featured Galaxy server.
> - It is a private local desktop Galaxy server, ideal for tinkering and experimentation, learning how the Galaxy server works
and for developing new tools for new kinds of scientists using Galaxy.
> - Any Galaxy tool from the toolshed can be installed and used.
> - Any reasonably simple script can be generated as a tool.
> - Newly generated tools appear in the tool menu after a refresh, and can be viewed and used as the user will see them.
> - Tool generation jobs can be rerun using the {% icon galaxy-refresh %} button on the history item after editing the form to make changes to the tool the user will see in Galaxy.
> - The Appliance is a Toolfactory flavour of the [docker-galaxy-stable resource](https://github.com/bgruening/docker-galaxy-stable/tree/master/compose).
>    - Documentation on connecting the appliance to a cluster for getting real work done with newly generated tools can be found at that repository.
>    - It can be backed up and persisted for as long as required, or it can be treated as a throw-away instance and deleted when no longer needed.
>    - There is almost zero technical friction if Docker and docker-compose are already installed. Only time is required.
>    - Usefulness will depend on sufficient hardware. Plenty of cores, RAM and disk storage are needed.
>    - On a modern workstation or well-endowed laptop, it will perform well out of the box.
>    - It will struggle on older consumer hardware with limited RAM and CPU capacity.
{: .tip }

The Appliance was developed for skilled programmers who need new Galaxy tools for their own use or for users they support. Any user comfortable with scientific or general
scripting languages on a Linux command
line may find it useful if they ever need a Galaxy tool that wraps a working script. Shell utilities and scripting language interpreters supported by Conda can be used.

Generated tools pass Planemo lint, and are functionally indistinguishable from equivalent manually written tools. A second tool can be used to finalise
ToolFactory untested archives. It uses Planemo. The tested toolshed archives contain a test based on the test data provided
at tool generation.

Working generated examples using bash, Python, Rscript, Lisp, Prolog, Perl and sed are provided and described below. Many demonstrate ToolFactory features.
All can be updated and changed easily, supporting learning by experimenting. More useful tools can be developed and tested using more complex scripts and as many inputs, outputs
and user supplied parameters as needed by that script.

If you are a scientist/programmer or software developer new to Galaxy and new to the dark arts of Galaxy tool building, this tutorial may be of help.
It introduces an automated way to convert any useful script into a toolshed ready tool, quickly *inside* Galaxy.


> ### {% icon tip %} Alternative ways to generate tools:
> - The [Galaxy Language Server](https://github.com/galaxyproject/galaxy-language-server) is a rapidly developing specialised manual tool building environment with VCS bindings.
> - Planemo can [generate tool XML](https://planemo.readthedocs.io/en/latest/writing_standalone.html) with an optional test.
>    - Planemo is recommended for developers who will focus on Galaxy tools. Excellent documentation.
>    - Widely used by experienced developers. Requires relatively little time to figure out - Galaxy tool syntax takes longer.
>    - No GUI. Command line only. Can create archives with additional steps.
>    - Need to pass all i/o and parameter details at once on the command line.
>    - Takes longer to learn to use and less accessible to many users than a form driven GUI might be.
>    - Manual XML editing required for selects and collections.
>    - See the recommended next steps at the end of this tutorial for Planemo related training.
>    - The ToolFactory uses planemo to generate test data and to run the test in the companion `planemo_test` tool.
> - Many Unix utilities (sed, awk...) are already available as IUC tools.
>    - They are `generic` in the sense that a script is supplied at **run time** by the tool user.
>    - The Lisp demonstration uses that model, but it may be desirable that one very specific script is "wrapped" as a reproducible tool.
>        - The user supplies only those I/O and parameter values specified on the ToolFactory form.
>        - Nothing else can be changed - just like with most Galaxy tools.
> - Choose whichever one fits best for the task at hand.
{: .tip }

# `Hello World!` with the ToolFactory Appliance

A `Hello World!` Galaxy tool is a good start. It takes no input files, but produces a text output file in the history. A single parameter allows the user to supply the text after
"Hello..." and the tool will write the combined string to a new history item. Trivial, but an excellent model worth studying in detail because it is easily extended to do
more useful tasks. The ToolFactory sample wraps a bash script fragment - `echo "Hello $1!"` to decorate and return the first parameter passed on the command line. This is a
generic model for many Galaxy tools with the addition of a few more files and parameters, as discussed below. Bash is used here but any scripting language available in Conda could
probably be used.

Watch a 6 minute [`Hello world` demonstration video](https://drive.google.com/file/d/1xpkcVGQ0jRdG78Kt-qLwqeFpE3RnSRsK/view?usp=sharing)
(Apologies for the poor quality - will try to make a shorter one.)

The form collects all the information needed for a new Galaxy tool. It is long and complex as a result, particularly with many repeated form elements for more complex tools.
Much of what is collected is used to construct a command line for the script when the generated tool runs.
Other information such as the name and dependencies are needed to construct the relevant
sections of the generated XML file in the toolshed archive. The ToolFactory form configured to generate the `Hello` example can be viewed below.

> ### {% icon details %} Annotated ToolFactory form that generates `Hello World`
> ![First part of the form](../../images/ToolFactory_hello1form.png "The first part of the form collects the new tool name and dependencies to be installed. In this case, no Conda dependency is used. bash can be specified as a conda dependency, but it is not very version dependent and usually available. Reproducibility is not an issue for this trivial example. When it is, specify the dependencies and their versions here and the generated tool will always use them. If run in a shell, the bash script <code>echo "Hello $1"</code> in the text box will emit a string that includes the first command line parameter - such as "Hello Galaxy Training Network" This will be collected from STDOUT (configured below) into a new history output file (named and configured below). Positional parameters are chosen so the first parameter on the command line will be emitted when the script runs.")
>
> ![Second part of the form](../../images/ToolFactory_hello2form.png "The second section shows the new generated history output. It uses the special name <code>STDOUT</code> so the tool will take whatever the bash script writes and create a new text file called <code>Hello_output</code>. When the test is generated, the pass criterion is that the default value <code>Galaxy Training Network</code> should appear as the message in <code>hello_output</code>. no difference. Other criteria including <code>sim_size</code> are available for the test applied to each output file. There is no limit (other than your patience) to the number of new generated history outputs. Note that this example has no history input files. Again, any number of these can be specified on the form using the repeat.")
>
> ![Third part of the form](../../images/ToolFactory_hello3form.png "The third section shows the form settings for the user supplied parameter to be passed in to the bash script on the command line. It will be the first positional parameter because the ordinal position is 1. Argparse parameters are shown in other samples. The help and label text for each input file and user defined parameter will appear on the generated tool form for the user so make them informative. This is where you can change the default from "World" to "Galaxy Training Network" on the sample provided and regenerate it to make a new tool later in the tutorial.")
>
{: .details }


The generated tool XML appears in the history after the ToolFactory is executed and the tool itself is installed in the `ToolFactory Generated Tools` submenu.
Text on the form is specified in the XML and it all comes from the ToolFactory form.

> ### {% icon details %} Generated XML and tool form
>
> [Galaxy XML documentation is here](https://docs.galaxyproject.org/en/latest/dev/schema.html)
>
> Note how text from the form appears in the generated tool XML
>
> ```xml
> <tool name="hello_toolshed" id="hello_toolshed" version="0.01">
>   <!--Source in git at: https://github.com/fubar2/toolfactory-->
>   <!--Created by planemo@galaxyproject.org at 22/01/2021 13:48:27 using the Galaxy Tool Factory.-->
>   <description>Says hello</description>
>   <stdio>
>     <exit_code range="1:" level="fatal"/>
>   </stdio>
>  <version_command><![CDATA[echo "0.01"]]></version_command>
>  <command><![CDATA[bash
>  $runme
>  "$sayhelloto" > $Hello_output]]>
>  </command>
>   <configfiles>
>     <configfile name="runme"><![CDATA[
>  echo "Hello $1"
>  ]]></configfile>
>   </configfiles>
>   <inputs>
>     <param label="Say hello to" help="" value="Galaxy Training Network!!" type="text" name="sayhelloto" argument="sayhelloto"/>
>   </inputs>
>   <outputs>
>     <data name="Hello_output" format="txt" label="Hello_output" hidden="false"/>
>   </outputs>
>   <tests>
>     <test>
>       <output name="Hello_output" value="Hello_output_sample" compare="diff" lines_diff="0"/>
>       <param name="sayhelloto" value="Galaxy Training Network!!"/>
>     </test>
>   </tests>
>   <help><![CDATA[
>
> **What it Does**
>
> ToolFactory demonstration - hello world in Galaxy
>
>
>
> ------
>
>
> Script::
>
>     echo "Hello $1"
>
> ]]></help>
>   <citations>
>     <citation type="doi">10.1093/bioinformatics/bts573</citation>
>   </citations>
> </tool>
> ```
>
> Which, when seen loaded into Galaxy looks like an ordinary tool:
> ![Generated form seen by the new tool user](../../images/toolfactory_hello_demo_form.png "The form displayed when the generated Hello tool is executed is below. The user sees a text box to enter any string. When executed, it will be echoed to a new history file called <code>Hello_output</code>")
> When a user runs the tool and enters some text in the text box, the decorated output will appear in a new history text dataset.
> This may not seem very exciting but it provides a useful pattern that can easily be adapted and extended.
> The script could do something far more interesting and could take unlimited input datasets, user configurable parameters and can produce as many outputs as needed in the history.
>
{: .details}


> ### {% icon tip %} If this is confusing
>
> If you are not yet familiar with the basics of Galaxy tools covered in the
> [tool integration training material]({% link topics/dev/tutorials/tool-integration/slides.html %}), the example form and XML
> will be confusing. You may gain more by reviewing the introductory part of that material, and then coming back here?
> It's a lot to learn and it is complicated. While a form driven code generator can hide much of the complexity of generating the code,
> the user must supply valid inputs for the code to be useful.
>
{: .tip}

> ### {% icon comment %} Limits and scope
>
> - It works best wrapping simple R/Bash/Python and other interpreted scripts, with few user supplied parameters and a few input and output files.
> - Scripts are easier than some Conda packages
>   - They can easily be modified to respond to default empty parameters as if they had not been passed.
>   - As a result, advanced tool building elements such as conditionals and related tricks requiring manual coding, can often be avoided.
> - On the other hand, many Conda dependencies will require XML conditionals or other tool XML constructs that are not easy to generate automatically.
> - While some simple requirements may be manageable, complex ones will not be suitable for the ToolFactory.
> - Compared to the more usual shell and a text editor, The ToolFactory in Galaxy is a slow and clumsy way to debugging scripts.
> - **Starting a new ToolFactory tool with a know good command line and data** is strongly recommended. You will know exactly what to expect from the tool test for a first sanity check.
> - Corrolary: Unless there is a working script that needs to be wrapped into a toolshed-ready Galaxy tool, the ToolFactory is of little use.
> - Generated tools are untested and not recommended for sharing.
>   - Testing is easy - use the Planemo test tool. It may take a while to run depending on Conda dependency installation. It will return the tested tool to your history and a report collection.
>   - The planemo_test tool creates a new tested toolshed archive ready for sharing, and a collection with reports.
>       - The Planemo test report is in the collection with a lint report and a log of the entire run.
>       - *Please check the html report to make sure it passed* before sharing your new tool.
{: .comment}

## Installation

> ### {% icon hands_on %} Hands-on: Launching the Appliance
>
> 1. [Install Docker](https://docs.docker.com/engine/install/) following the appropriate instructions for your platform.
>
> 2. Then, `pip3 install docker-compose`
>
> 2. Go to [the ToolFactory appliance github repository](https://github.com/fubar2/toolfactory-galaxy-server)
>
> 3. Clone it or download the zip and unzip it somewhere handy - such as `~/toolfactory-galaxy-server-main`
>
> 4. Change to the compose directory and launch it:
>
>    > ### {% icon code-in %} Input Bash: Clone the Code
>    > ```bash
>    > wget https://github.com/fubar2/toolfactory-galaxy-server/archive/refs/heads/main.zip
>    > unzip main.zip
>    > cd toolfactory-galaxy-server-main/compose
>    > docker-compose pull
>    > docker-compose up
>    > ```
>    {: .code-in}
>
>    > ### {% icon tip %} Appliance tips
>    >
>    >  - For the first time start, watching the startup process logs is highly recommended.
>    >      - You will learn a lot about how a Galaxy server works and see when the Appliance is ready to use.
>    >  - The docker containers may not fit or run well on an underpowered machine. Multiple CPU cores, 8GB of RAM and fast disk are needed for an enjoyable appliance.
>    >  - Change your admin password and if anyone else has possible network access, the API key `fakekey` used for configuration.
>    >  - It is important that your appliance is not accessible to any potential miscreants on the local or public internet.
>    >  - It is recommended for use only as a private disposable desktop development environment.
>    >    - The Appliance keeps no backup of any work.
>    >    - The user can backup the export directory if desired.
>    >    - An institutional server is a safer bet for preserving real research.
>    {: .tip}
>
> 5. Your appliance should be running with a local Galaxy on [port 8080 of your workstation](http://localhost:8080) after a fair bit of activity.
>
>    - Login with the username `admin@galaxy.org` and the password `password`
>    - This is obviously insecure but convenient and easily changed at first login.
>    - The container `/export` directory is mounted locally at `compose/export` so you can find your generated and tested tools for sharing.
>
>    > ### {% icon tip %} Tip: Demonstration tools are the functional documentation
>    >
>    > - At first login you will find the demonstration history ready to explore if you waited for all the Conda activity to die down
>    > - It takes a minute or two to import because the dependencies for the ToolFactory must first be installed.
>    > - Check the histories if a different one appears - two are loaded and there seems some randomness about which appears at login.
>    > - If it's not there, you can import it manually from Zenodo as described in the Welcome page text.
>    > - To explore an example, open the toolshed XML history item by clicking on the name, and select the {% icon galaxy-refresh %} `rerun` button from the expanded view
>    >    - The form that generated that tool will appear for you to examine
>    >    - Edit the form - add parameters and change the script to suit - and rerun to create an *updated* tool. The history has previous versions.
>    >    - Change the tool ID to change the tool name and generate a different tool. *Generating a same-name tool will overwrite the old version*.
>    {: .tip}
>
>    > ### {% icon tip %} Tip: Patience!
>    > When you run the ToolFactory for the first time inside the container and whenever you run a new tool with new dependencies, it will require some time to build the conda environment.
>    > Check for Conda or other processes if things seem stuck.
>    {: .tip}
>
>
{: .hands_on}

## Exploring the ToolFactory in the running Appliance.

- The best way to explore the kinds of tasks that can be achieved with simple scripts is to take a look at each sample tool.
- Note how the various options have been configured and what kinds of scripts this could be used for in your work.
- The example script can be swapped out for another one known to work and additional new parameters added to suit, to extend the toy examples and create tools of use to your users.
- Change the tool name on the newly edited form, press `execute` and rerun the job to generate a new toolshed archive and test report collection.
- If you don't change the tool name before re-generating a tool, the original installed tool will be updated with the new configuration.


## Hello World!

> ### {% icon hands_on %} Hands-on: Building the Hello World example
>
> 1. Run {% tool ToolFactory %} with the following parameters:
>    - "Dependencies, optional script and script interpreter"
>      - *"Interpreter for the script"*: `bash`
>      - *"Script for executable above to interpret"*: `echo "Hello $1"`
>    - "Data file input, output and settings forming the executable or script command line"
>      - *"Command line parameter passing method to use"*: `positional`
>      - "Input and output files"
>        - {% icon param-repeat %} *"Insert Outputs"*
>          - *"Name for this output to appear in new history"*: `Hello_output`
>          - *"Select the datatype for this output"*: `txt`
>          - *"Position"*: `STDOUT`
>      - "Arguments"
>        - {% icon param-repeat %} *"Insert Command Line Parameters"*
>          - *"Choose the name for this parameter - MUST not be blank!"*: `say_hello_to`
>          - *"Enter this parameter's label for the form"*: `Say hello to`
>          - *"Positional ordinal \| argparse argument name"*: `1`
>
>    > ### {% icon comment %} First time use involves a long pause in some installations
>    > The first job takes longer in some installation scenarios because the ToolFactory dependencies are installed before the tool can run.
>    {: .comment}
>
> 2. Execute
>
> 2. Explore the outputs - do they match what you expected?
>
> 3. Refresh the page - click the home icon (or the "Analysis" tab) - to see the new tool in the `ToolFactory Generated Tools` section of the tools menu.
>
> 4. Run the tool that has been added - Select the new tool and examine the form. Check that all the changes are as they should be.
>
> 5. Test the tool using the `planemo_test` tool from the `ToolFactory` tool section. This may take 5 or 10 minutes if this is the first test on a new appliance. Subsequent startup will be quicker.
>     - Check the lint report
>     - View the planemo test report - is it ok? See if you can open up the details hidden when you first view the page.
>     - Take a look at what Planemo did in the report log. It is very, very long and usually involves popping up and tearing down a Galaxy server twice.
>     - This can take time, particularly if Conda dependencies are required as they can take a long time to initialise.
>
{: .hands_on}

## The Development Cycle

1. Test your script on the command line and confirm it works.
1. In the appliance, start a new history and upload all the input samples used on the command line.
1. Open the ToolFactory tool form.
1. Define the tool metadata, dependencies, interpreter and paste the script.
1. Add the required history inputs using the small samples as examples.
1. Specify all the output files to be created in the user's history.
1. Add any user adjustable command line parameters such as text fields. Look at the samples to see how the ToolFactory form can be used.
1. Execute the tool when the form is completed.
1. When the job is complete, refresh the page (Home icon or Analysis tab). The new tool will be found in the `ToolFactory Generated Tools` section, ready to run.
1. Run the new tool and check that it does what you expect, or re-generate after adjusting the form settings as needed.
1. If it needs any changes, open the XML history item created when the tool was generated and use the {% icon galaxy-refresh %} rerun button to
recreate the ToolFactory form as it was when you last ran it. Adjust as needed and use the tool form`Execute` button to run the ToolFactory again with updated settings.
1. Rinse, repeat.
1. When everything is to your satisfaction, start the `planemo_test` tool in the ToolFactory menu and select the tool XML to finalise.
    1. A Planemo test will be run
    1. Time will depend on Conda dependencies. If none a minute or so.
    1. A new tested archive, Planemo test report, Planemo lint output and a log of the test run will be in a new collection in the history when the job finishes.
    1. The tested toolshed archive can be downloaded from the history or found in `...compose/export/galaxy/tested_TF_archives/[tool name]`
1. Warning: building a tool with the name `mytool` will overwrite any previously generated ToolFactory tool with the same name. Persisted jobs in user histories always allow older versions to be recreated if necessary.

Galaxy can be used as an Integrated Development Environment for tools - clunky but oddly satisfying. Note this is distinct from debugging the script - that is not at all satisfying in Galaxy unless you like waiting for jobs to finish. A shell is much better for that.

![Galaxy as an IDE for tools with the ToolFactory](../../images/ToolFactory_big_picture.png "Galaxy can be used as a tool development environment for users who can write their own scripts as shown in this process overview slide.")

## Hello World: Continued

> ### {% icon hands_on %} Hands-on: Modifying the Hello World example
>
> 1. Rerun the output of your previous job, and make the following changes
>
>    - "Dependencies, optional script and script interpreter"
>      - *"Script for executable above to interpret"*: `echo "Hello $1"; echo "Goodbye $2";`
>    - "Data file input, output and settings forming the executable or script command line"
>      - "Arguments"
>        - Add a second {% icon param-repeat %} *"Insert Command Line Parameters"*
>          - *"Choose the name for this parameter - MUST not be blank!"*: `say_bye_to`
>          - *"Enter this parameter's label for the form"*: `Say bye to`
>          - *"Positional ordinal \| argparse argument name"*: `2`
>
{: .hands_on}

## Hello Collections!

> ### {% icon hands_on %} Hands-on: Building a File Splitter
>
> 1. Run {% tool ToolFactory %} with the following parameters:
>    - *"New tool ID and title for outputs"*: `file_splitter`
>    - "Dependencies, optional script and script interpreter"
>      - *"Interpreter for the script"*: `bash`
>      - *"Script for executable above to interpret"*:
>
>        ```bash
>        mkdir -p outputs/;
>        split --lines=$2 --additional-suffix=.txt $1 outputs/
>        ```
>
>    - "Data file input, output and settings forming the executable or script command line"
>      - *"Command line parameter passing method to use"*: `positional`
>      - "Input and output files"
>        - {% icon param-repeat %} *"Insert Inputs"*
>          - *"Select an input file from your history"*: Choose any XML file from your history, we'll use this as an example
>          - *"Select the datatype for this output"*: `txt`
>          - *"This will become the user prompt for the form so please make it informative"*: `File to split`
>          - *"Positional: ordinal integer. Argparse: argument name. STDIN if the executable/script expects it"*: `1`
>        - {% icon param-repeat %} *"Insert Output Collections"*
>          - *"Select the kind of collection for this output"*: `List`
>          - *"Label for this collection"*: `File Parts`
>      - "Arguments"
>        - {% icon param-repeat %} *"Insert Command Line Parameters"*
>          - *"Choose the name for this parameter - MUST not be blank!"*: `lines`
>          - *"Select the type for this parameter"*: `Integer`
>            - *"Enter this parameter's default integer value"*: `4`
>          - *"Enter this parameter's label for the form"*: `Number of lines in each split file`
>          - *"Positional ordinal \| argparse argument name"*: `2`
>
> 2. Execute
>
> 3. Refresh the Galaxy Page and locate your `collections_test` tool
>
> 4. Run the tool, selecting any text file in your history, e.g. the XML output from the ToolFactory that created this tool.
>
> 5. You should see a collection filled with files named `aa` to `ao` (or so), each with 4 lines from your file.
{: .hands_on}

Hopefully this is a motivating example and you can imagine what else you might be able to accomplish with collection outputs! You could extend this example to split a `.fastq` file which could speed up your processing, or you could use collections to store extra images or plots produced by your tool.

> ### {% icon warning %} Collection Testing
> The default generated test for output collections always passes because it doesn't test anything.
> Supplying a test over-ride is recommended for collections.
> For a real test, one or more expected <element.../> tags must be provided so the test really does test something.
{: .warning}

## Done!

> ### {% icon hands_on %} Hands-on: To safely shut the appliance down
>
> 1. Type <kbd>Ctrl-C</kbd> in the terminal where you ran `docker-compose`. Most things will still be there next time you start it.
>
> 2. Explore the `export/galaxy/tools/TFtools` directory. There you should find directories with the tools you created during this tutorial, as well as some others that are pre-loaded.
>
{: .hands_on}

### Limits, workarounds and value proposition

- The ToolFactory Appliance is a slightly clumsy but useable way to create, test and maintain Galaxy tools in a web GUI.
- The ToolFactory tool is an automated code generator installed in the appliance tool menu
- No generator can replace manual editing by a skilled developer other than in constrained simple cases.
- These are common enough in the daily work of most data intensive scientific fields to make a tool generator potentially worth keeping handy.
- For simple scripts and appropriate Conda packages, a professional Galaxy tool developer can probably do it quickly by hand, but those skills take time to acquire
and are not widely available, particularly in scientific fields coming to Galaxy.
- Tools can have command-override and test-override pasted in as in one of the BWA samples.
   - This can solve some of the limitations but if it is needed, it might be better to prepare the wrapper manually if a skilled developer is available.
- The ToolFactory can help new scientists and developers to quickly get some simple tools working for their colleagues while awaiting help with the complex ones.

# Next Steps

Expand your knowledge further with the [Advanced ToolFactory tutorial]({% link topics/dev/tutorials/tool-generators-advanced/tutorial.md %})

# Acknowledgements

This tutorial is based on the work of thousands of contributers to the Galaxy project over the last 15 years or so. Thanks all!

Special thanks are owed to:

- {% include _includes/contributor-badge.html id="mvdbeek" %} for thoughtful comments on the role of the ToolFactory that helped motivate the tutorial.
- {% include _includes/contributor-badge.html id="hexylena" %} for
    - review and revisions to the tutorial and associated code.
    - elegantly generated lint-free XML provided by [galaxyml code](https://github.com/hexylena/galaxyxml)
