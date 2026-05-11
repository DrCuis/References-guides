# Cuis Launching Procedure
*By Hilaire Fernandes*

## Sequence of Actions
Right after starting a Cuis-Smalltalk system, once the image is loaded
into memory, it executes a sequence of actions, part of its launching
procedure.

To know what these actions are, and in which order they are executed, is
of first importance, particularly when developing an application with
its own launching procedure.

These actions are executed in the
`Smalltalk>>snapshot:andQuit:embedded:clearAllClassState:`
method. This method is pretty large for a Smalltalk method, so we
extract the important actions:

1. execution of the `startUp` class method of each class,
   implementing it. Example `InputSensor class startUp`,
1. interpret some of the command line oprions: `-h -v -q -ud -udIsBase`,
1. open and install source and change files,
1. read and apply user preferences,
1. interpret the remaining of the command line options: `-u -r -d -l -s`,
1. execute the App Launcher, if any.


Observe how some command line options are interpreted earlier. For example
the `-ud` to set the location of the user directory. This location is
used for logging, package defaut save location, user preferences, file out,
user change files.

The start-up actions occur early in the launching procedure, therefore
it should not depend on any command line options.

## Application Launching Procedure

An application built with Cuis prepares its image from the standard
Cuis Smalltalk image, mainly by installing the required packages,
customizing it, then optionally installing a launching procedure --
For example, [Dr. Geo](https://www.gnu.org/software/dr-geo/) proceeds in such a way.

An application launching procedure is installed with the
`AppLauncher` class. As written in the previous section, it is the
last action of the Cuis launching procedure.

In Dr. Geo, it is set during the image preparation:

```smalltalk
   Smalltalk at: #drgeoApp put: DrGeoSystem.
   AppLauncher appGlobalName: #drgeoApp appSelector: #launch.

```

Then, in the end-user Dr. Geo application, the code above translates as the execution
of the following code, performed as the last action of the Cuis launching procedure:

```smalltalk
DrGeoSystem launch
```
