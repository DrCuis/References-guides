# Package
*By Hilaire Fernandes*

The `Feature require: 'myGreatPackage'` searches for the package file
`'myGreatPackage'` and installs it. Cuis searches in some default path
directions. 

Let's suppose your Cuis-Smalltalk environment is installed in the
`Cuis-Current/Cuis-Smalltalk-Dev` directory.

* in the `myGreatPackage` directory, ??? **I don't understand the
  rational behind**

* in the `Packages` folder and its sub-folders of your Cuis
  installation: `Cuis-Current/Cuis-Smalltalk-Dev/Packages`.

* In the user's Package folder as defined by Cuis and the default
  place where a newly created package is saved:
  `/Cuis-Current/Cuis-Smalltalk-Dev-UserFiles/NewPackages`

* In the parent folder and sub-folders of your Cuis installation:
  `Cuis-Current`


Reference:  method `placeToLookForPackagesDo:`
