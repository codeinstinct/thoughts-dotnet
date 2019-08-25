# thoughts-dotnet

Here are some thoughts on how to make dotnet toolchain more user friendly on different OS's.

**Note**: These are simply proposals. Feel free to consider or discard them.

## Dotnet First Contact Executable (DFCE)

- A tool that can be downloaded on any OS by a [user](#user)  which lets them express their intent of use. Based on the [intent](#intents) specified, the tool does all the necessary installation such as:
  -  installing the right runtime, or 
  -  installing the right SDK, or
  -  installing the right IDE's (vscode and/or addons, unity, xamarin).
  
- After this installation, the DFCE also runs through a diagnostic flow to ensure that the user will actually be able to perform those intents on this setup. This is where the [Dotnet Doctor](#dotnet-doctor) tool comes in.

- Responsibilities of DFCE
  - Collect user intent
  - Keep track of environment variables and notify the user if they have gotten unintentionally modified by some other tool.
    - Check the .bashrc, .bash_profile files on ubuntu, and other files for other OS's 
  - Keep track of integrity of the installation sites (folders) and inform if there is any mismatch in file signatures.
  - To enable quick actions such as "upgrade/downgrade" of runtimes/sdks/ide's.
  - Suggestion to remove a version of dotnet if it's not being used (atleast once in 6 months).


## Dotnet Doctor (DD)

- Based on the learnt intent of the user, with the [DFCE](#dotnet-first-contact-executable-dfce), this tool attempts to create projects behind the scenes (temp folders) and execute them end-to-end.
  - Examples:
    - Dotnet console project
      - The tool creates a new dotnet project and runs it. Then checks if the output to the console was "Hello world!" or not.
    - Blazor server project
      - The tool creates a new blazorserver project and runs it in a headless browser. Then checks the outputs of the console (both in the shell and in the headless browser) to detect any failures.
    - Xamarin Android development
      - The tool first looks for android dependencies, environment variables, Java SDK configuration, emulators/devices are usable etc.
      - Mono tools and Xamarin IDE are functioning as expected.
      - Then it checks if the hello world equivalent works as well 

# Why would  we need this ?

The reason to have the [DFCE](#dotnet-first-contact-executable-dfce) and [DD](#dotnet-doctor-dd) is :
- to be assured that the right bits are present on the system. 
- to catch installation errors early on and not when developers start working on the application they want to build.
  - For example, building a wpf project is not supported on ubuntu as of today.
- Know right away whether a particular [setup configuration](#setup-configuration) can help achieve the intent of the user. If not, what steps could be taken.
- Adding deterministic flow to configuration reduces anxiety of whether a configuration will work or not, on not just our machines, but also in docker, or in cloud VPS/VMs or iot devices and wherever dotnet can reach - also addresses the "I don't want to mess with this machine" problem.
- Ability to export configuration (configuration.json, for example) and replicate the exact setup on a remote machine with simply the configuration.json and the DFCE.

# Glossary

## User
- An application consumer or application developer

## Intents
- End-User intents
  - Install a particular runtime for a particular application.
    - We don't want the user to be concerned with which package manager is being used.
- Developer intents
  - Install a particular sdk.
  - Create a WPF application
  - Create a BlazorServer application etc.

## Setup configuration
- A state representing all the dotnet runtimes, sdks, IDE's settings installed globally or locally on the disk.