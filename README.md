Custom Plugins
--------------

A repository for customer built plugins, based off the included template

Non-Developers are welcome to start a new plugin by creating a folder for it, and laying out a readme file with the goals.

Our development team will be glad to step in and start making the plugin a reality.

Plugins can be writting in any language nativly supoorted by OS X including Python, BASH, SH, TSH and pearl to name a few

Plugins will run as root - With great power comes great responsibilities.

Overview of a Plugin's Operation
--------------

An effective plugin needs to perform 3 Major tasks:
* Do some work! (Python, Bash, etc)
* Print some output. (stdout please)
* Exit with an appropriate status (0, 2, 20 details below)

On each run of the monitoring client, the plugins folder is enumerated, and each plugin with a valid plist is run.

The Monitoring Client reads the output (stdout) of each plugin, and includes this text, as well as the exit status, to the Watchman Monitoring server.

The plugin's architect uses the exit status to the server know the severity of the report.


Exit Statuses and the Watchman Monitoring server 
--------------

The server will act according to how a plugin reports and exits.  The following exit statuses are used:

* *0* - We're All Good = Information will be displayed at the server, but not emailed.
* *2* - Big Problem! = This information will be displayed on the server, and trigger an email.
* *20* - Information worth reporting was found, but no email should be sent.
* *25* - Nothing to see here = Information will not be sent to the server (and not emailed)

_A future version of the server will gather & display messages sent with status 20._

_If a plugin's exit status is 1, the warning will be reported to the Watchman Monitoring staff, for inspection of the plugin's failure._


All custom plugins must begin with an underscore _ (i.e. _check_for_variable.plugin)

A private repository is available as well.
https://github.com/watchmanmonitoring/subscriberplugins


Preference Pane Options
--------------

Each plugin has the ability to provide options to be displayed in the Preference Pane.


h1. Creating PreferencePane options

The vast majority of our plugins require no user interaction in order to be effective.

There are cases where the ability to set an option is needed. We've solved this by adding the ability to define custom fields in our PreferencePane

The PreferencePane support an Array named PluginOptions, which can contain a number of Dictionary entries.

Each entry will be presented within the PreferencePane, so long as the dictionary contains the follow 5 keys as defined below

*SettingStoragePath*
  full path to the settings file in question
	If the file doesn't exist, it will be created IF the path begins /Library/MonitoringClient
		else do not display that GUI object

*SettingStorageKey*
	The exact name of key which the PreferencePane will save the file to.
	Will be created if it didn’t exist

*SettingTitle*
	Must be present or the gui option won’t be displayed in the PreferencePane

*SettingTitleHint*
	Optional, displayed in smaller text to describe functionality of this particular option.

*SettingType*
	A dictionary with two children keys:
	Type
	Value


*PluginOptions*

|Type|Value|Description|Stored as|
|------|-----|-----|----|
|Checkbox|Positive or Negative|Positive = checked = true & set to false if not present Negative = checked = False, & set to true if not present (Positive is assumed)|bool|
|FolderPicker|[ignored]|make this a “well”, so the end user has the option to drag & drop or click Select|string|
|FilePicker|[ignored]|make this a “well”, so the end user has the option to drag & drop or click Select|string|
|PopUpMenu|{array}|First item is default unless it is a blank|string|
|Checkboxes|[ignored]|Each dictionary entry stored as a bool value, |Dictionary of bool values|
|NumberPicker|Range|(0-12) whole numbers 0 through 12, inclusive|Number|
|TextField|[max length]|Will store whatever the user enters, as entered|String|
|DisplayOnly|ignored|There will be no user options here. This will be used only to display the SettingTitle and/or Setting Hint|Empty|


PreferencePane visibility
--------------

There are some cases when a Plugin, or it's PreferencePane options, are needed, but not until certain requirements are met.

Disabling a Plugin
--------------

A plugin will be fully disabled when it's file name is added to 

/Library/MonitoringClient/ClientSettings.plist

as an entry in the array _PluginsDisabled_


Disabling the Plugin's display option
--------------

We support a boolean key titled _PrefPaneVisibility_ in any given plugin's _settings.plist file.

If this key is either missing or True, the plugin will be shown in the PreferencePane.

Programmatically changing the key to False is a way to prevent the options from being displayed, though the plugin itself can still run.
