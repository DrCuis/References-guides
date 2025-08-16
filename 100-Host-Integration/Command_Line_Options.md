# Command line options
*By Hilaire Fernandes*

The Open Smalltalk Virtual Machine has command line options interpreted at its own C level, Cuis has also its own command line options interpreted at the Cuis Smalltalk image level, therefore accessible to the Smalltalk developer.

This technical document describes these options.

## List of options

This shell statement executed in a Cuis-Smalltalk-Dev directory on an x64 Linux system (the VM invocation varies with the platform, below it is abbreviated as CuisVM):
```
Cuis-Smalltalk-Dev$ ./CuisVM.app/Contents/Linux-x86_64/squeak ./CuisImage/Cuis7.3-7036.image -h
```
revealed the available options:
```
Command line options:
  -h Display this help message and quit
  -v Display the system information, containing the version and quit
  -q Just quit
  -e Do not ignore exceptions during startup, but open a Debugger
  -u Install available updates from CoreUpdates directory
  -r <feature> Require <feature>. Find and install a Cuis package file supplying it.
  -d <code> Evaluate code. <code> must be a Smalltalk expression in double quotes. (No comments allowed)
  -s <scriptFile> The file must contain a valid Smalltalk expression. Compile and evaluate it.
  -l <codeFile> File in Smalltalk code. The file must be a valid ChangeSet file.
  -ud <directory> Set User Directory to be <directory>
  -udIsBase Set User Directory to be Cuis Base directory
  -- end of options; stop processing options

Arguments for scripts, code files, packages (and <code>):
  are available in 'Smalltalk startUpScriptArguments'

Multiple options:
  any option can be passed multiple times
```

## Descriptions

### -h
Display this help message and quit

### -v
Display the system information, containing the version and quit

### -q
Just quit

### -e 
If any errors occur during startup, instead of trying to continue silently, open a Debugger.

[See -d option](#-d) for more details

### -u

At start-up, the latest Cuis updates will be installed. When using this option, you should first update your `Cuis-Smalltalk-Dev` repository with `git pull`. An alternative is to execute `./pullAllRepos.sh` to additionally update the related Cuis repositories around.

### -r
Find and Install a Cuis package for requested feature name, a file with the `.pck.st` extension.

```
CuisVM Cuis.image -r SVG
```

When the package is not found, Cuis quit with an error message in the console:
```
CuisVM cuis.image -r SVGMorph
Could not find code package file for [SVGMorph].
Installation failed.
```
Several packages can be installed, each one with its `-r` option:
```
CuisVM cuis.image -r Goodies -r Theme-Themes
```

### -d

Write Smalltalk code to be executed between double quotes:

```
CuisVM cuis.image -d "Transcript logToStdout: true. 'Hello message to stdout' print"
2025-05-15T11:31:21.101 process:40 47139 Hello message to stdout
```

When the Smalltalk code fails at execution time, by default there is no notice from Cuis. In that circumstance add the `-e` option if a Debugger window is wished:
```
CuisVM cuis.image -e -d "1/0"
```

With this `-d` option it is possible to pass information from the host to Cuis. In [Dr. Geo](http://gnu.org/s/dr-geo), to know about the home directory of the user, this option is added to the Dr.Geo start-up script:
```
-d "Smalltalk at: #home put: '$HOME' asDirectoryEntry"
```
Then from Cuis, the information can be accessed:
```smalltalk
Smalltalk at: #home 
=> /home/hilaire
```

### -s

Compile and evaluate a Smalltalk expression. Similar to -d, but the code must be in a file, and the argument is the file name.

```
CuisVM cuis.image -s setUpEnvironment.st
```

See also the chapter [Daily Workflow](https://drcuis.github.io/TheCuisBook/Daily-Workflow.html) in _The Cuis Book_.

### -l

File in a Smalltalk Change Set code file

```
CuisVM cuis.image -l codePatches.cs.st
```

### -ud

This option changes the user base directory. It is used in several
circumstances in the Cuis-Smalltalk system to install sub-folders or files:

- **ChangeSets.** To save new ChangeSets
- **Packages.** To save new code packages
- **UserChanges.** To store user changes to the Smalltalk image
- **FileOuts.** To save filed out code (method, class, code category)
- **Logs.** The place to log on file when the Cuis-Smalltalk system is
  instructed to do so
- **UserPrefs.txt.** To save user preferences in the system

```
CuisVM cuis.image -ud "~/.config/myCuisApp"
```

### -udIsBase

This option sets the user base directory to be the same as the Cuis base directory. It can be used instead of using the **-ud** option, if you design the folder structure of your application to use a single main folder. It doesn't take any arguments, and the effect is the same as doing **-ud** with the base Cuis directory as the argument.
