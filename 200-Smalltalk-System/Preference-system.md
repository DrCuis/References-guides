_By Hilaire Fernandes_

## Introduction
Cuis-Smalltalk has its preferences system designed in two classes `PreferenceSet` and `Preference`. Then there is the `Preferences` global variable for the preferences of the Cuis-Smalltalk system itself.
```smalltalk
Preferences 
=> a PreferenceSet .
Smalltalk at: #Preferences 
=> a PreferenceSet .
```

Each preference is a `Preference` instance, and member of a `PreferenceSet` instance collected in a dictionary. Here are a few preferences from the Cuis-Smalltalk system preferences:
```smalltalk
Preferences allPreferences
=>  a Dictionary(
   #checkForSlips->Preference (#checkForSlips = Boolean::true)
   #soundQuickStart->Preference (#soundQuickStart = Boolean::false)
   #defaultAuthorName->Preference (#defaultAuthorName = BlockClosure::[closure] in PreferenceSet class>>installMiscPreferences)
   #messageCategoryAnnotations->Preference (#messageCategoryAnnotations = Array::#(#messagesCount #messageSendsCount))
   #pointer->Preference (#pointer = #(#touch #mouse)::#mouse)
   #defaultFontSize->Preference (#defaultFontSize = SmallInteger::11)
   #defaultLineSpacingFactor->Preference (#defaultLineSpacingFactor = SmallFloat64::1.15)
```

There are various types of preference such as Boolean, BlockClosure, numbers, Array (where the preference can hold several values, for example see #messageCategoryAnnotations), and a value taken from a list (#pointer preference).

## Playing with the Preferences
To interrogate a preference, you need to know its symbols, Inspect [Ctrl]-i `Preferences allPreferences` to have the full list of the Cuis-Smalltalk system preferences. This is what the World menu entry 'Preferences>All preferences...' does.

### Preference Reading
To read the value of a preference:
```smalltalk
Preferences at: #soundQuickStart
=> false
Preferences at: #defaultAuthorName
=> Hilaire Fernandes
Preferences at: #messageCategoryAnnotations 
=> #(#messagesCount #messageSendsCount)
Preferences at: #pointer 
=> #mouse
```

Alternatively, you can send the symbol directly to the Preferences instances:
```smalltalk
Preferences soundQuickStart.
Preferences defaultAuthorName.
Preferences messageCategoryAnnotations.
```

You can also request a particular `Preference`:
```smalltalk
Preferences instanceAt: #pointer
=> Preference (#pointer = #(#touch #mouse)::#mouse) .
```
It provides information about its type. The `#pointer` preference above, can take the value `#touch` or `#mouse`. Refer to the `Preference` class to discover what you can do with such instance:
```smalltalk
(Preferences instanceAt: #pointer) value
=> #mouse
(Preferences instanceAt: #pointer) type
=> #(#touch #mouse)
(Preferences instanceAt: #pointer) category
=> #system
```


Preferences are grouped in categories, but honestly, it is not very important:
```smalltalk
Preferences categories 
=> #(#font #gui #programming #system)
```

### Editing a Preference
To change the value of a preference, you can do it from its instance:
```smalltalk
(Preferences instanceAt: #pointer) value: #touch
(Preferences instanceAt: #pointer) value
=> #touch
```
Or more simply from the `PreferenceSet` accessor:
```smalltalk
Preferences at: #pointer put: #touch
```

**Caution**, the Preference system only loosely enforces type. The following will not raise an error:
```smalltalk
Preferences at: #pointer put: #trackpad
```
`#trackpad` was not an option among the preference type `#(#touch #mouse)`.

Additional attributes are more easily edited from the the preference instance, though:
```smalltalk
(Preferences instanceAt: #pointer) description: 'I define the type of tracking device'
```


### New Preference
A preference may be added directly to its `PreferenceSet`:

```smalltalk
Preferences name: #animateWindow category: #gui value: true
```
This creates a new `#animateWindow` preference in the given category `#gui` with a true value:
```smalltalk
Preferences at: #animateWindow 
=> true
Preferences instanceAt: #animateWindow 
=> Preference (#animateWindow = Boolean::true)
```
**Note how the Preference system deduces the type of the preference as a Boolean.**

Another example, where a new category `#page` is also added:
```smalltalk
Preferences name: #leftMargin category: #page value: 1.5
Preferences at: #leftMargin
=> 1.5
Preferences instanceAt: #leftMargin
=> Preference (#leftMargin = SmallFloat64::1.5)
Preferences categories
=> #(#font #gui #page #programming #system) .
```
When editing a value with a different type, an error is emitted:
```smalltalk
Preferences at: #leftMargin put: true
=> Error
Preferences at: #animateWindow put: 1.5
=> Error
``` 

You can describe a preference while creating it:
```smalltalk
Preferences 
   name: #rightMargin 
   description: 'The right margin of a page in centimeters' 
   category: #page 
   type: nil 
   value: 1.5.
Preferences instanceAt: #rightMargin
=> Preference (#rightMargin = SmallFloat64::1.5) .
```
**Note** how the type is `nil` to allow the preference system to deduce it.

**Beware**, adding a preference whose name already exists, will just overwrite it:
```smalltalk
Preferences name: #pointer category: #system value: #mouse
```

## Application Preferences
We showed above how new preferences and categories can be added to the own Cuis-Smalltalk system preferences. But you can also have your own `PreferenceSet` instance. This is what is done in the Dr. Geo application.

```smalltalk
myPreferences := PreferenceSet new.
myPreferences name: #topMargin category: #page value: 1.5.
myPreferences categories
=> #(#page)
myPreferences allPreferences
=> a Dictionary(#topMargin->Preference (#topMargin = SmallFloat64::1.5) )
```

## Saving to Disk Preferences
Each one of the preferences, part of the Cuis-Smalltalk system preferences, can be set to be saved on disk and reload when the image is started. This option does not require the image to be saved to retain the preferences; the preferences are saved in a distinct file in the user base directory.

### Ensure that the user preferences are saved 
First, you need to ensure that the Cuis-Smalltalk preferences are saved in a distinct ``UserPrefs.txt`` file, at the ``DirectoryEntry userBaseDirectory`` location:
```smalltalk
Preferences name: #useUserPrefsFile category: #system value: true.
```

This option can also be toggled form the Cuis-Smalltalk system World menu: **WorldMenu>Preferences>Save Prefs in UserPrefs.txt** / **WorldMenu>Preferences>Save Prefs in the image**.

### Instruct the preference system to save it
Then, after editing a preference, you can ask the Preferences to save it:
```smalltalk
Preferences at: #pointer put: #touch.
Preferences saveToDisk: #pointer.
```
Beware that just setting a preference value won't write to disk, for that you must send the `#saveToDisk:` message.
The saving of preferences is very limited. It only works with simple objects such as boolean, number, symbol and string. So make sure to use one of these object types.

This save to disk mechanism only works with the Cuis-Smalltalk system preferences; if you decide to have your own Preferences as described above, it won't work, but there are other options. See the *fileIn/Out* method category in the `PreferenceSet` class.

The preferences saved in the file **UserPrefs.txt** are automatically read and installed during the Cuis-Smalltalk system start-up, but the relevant option needs to be turned on (see above).