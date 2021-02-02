## Custom Plugins

A repository for customer built plugins, based off the included template

Non-Developers are welcome to start a new plugin by creating a folder for it, and laying out a readme file with the goals.

Our development team will be glad to step in and start making the plugin a reality.

Plugins can be written in any language natively supported by OS X including Python, bash, sh, zsh and pearl to name a few

Plugins will run as root - With great power comes great responsibilities.

#### Overview of a Plugin's Operation

Each plugin requires at least 2 files to function, optionally more:
- *pluginname*.plugin - The actual plugin code to executed. Can be any binary executable or interpreted language script (bash, Python, zsh, etc). This file will live in `/Library/MonitoringClient/Plugins`.
- *pluginname*.plist - A plist file with some required data for the Watchman agent to be able to run the plugin. This file also lives in `/Library/MonitoringClient/Plugins`. There are some optional keys (discussed later in this document) but the keys required in this plist are:
  - `PluginName` - The user friendly name of the PluginName.
  - `PluginSlug` - The sluggified (no URL illegal characters) version of the plugin name.
  - `PluginUUID` - UUID used to identify the plugin to the server. Watchman Monitoring will provide and register one on request.
  - `ProgramArguments` - File name of the plugin to run. Assumes the path of `/Library/MonitoringClient/Plugins/`.
- *pluginname*_settings.plist - (Optional) Plist that can contain settings/data specific to the plugin's operation, any data the plugin needs to save. This file lives in `/Library/MonitoringClient/PluginSupport` and the plugin code should generate it if/when it doesn't exist with any default settings/data.

An effective plugin needs to perform 3 Major tasks:
* Do some work to determine if any kind of condition worth reporting to the dashboard exists.
* Print some output to `stdout`.
* Exit with an appropriate status (0, 2, 20, 25, 200, details below).

On each run of the monitoring client, the plugins folder is enumerated, and each plugin with a valid plist is run.

The Monitoring Client reads the output (`stdout`) of each plugin, and includes this text, as well as the exit status, to the Watchman Monitoring server. The plugin's architect uses the exit status to let the server know the severity of the report.


#### Exit Status and the Watchman Monitoring server

The server will act according to how a plugin reports and exits.  The following exit statuses are used:

* *0* - We're All Good = Information will be displayed at the server, but no alert or email will be sent.
* *2* - Big Problem! = This information will be displayed on the server, and trigger a single email until the error is resolved.
* *20* - Information worth reporting was found, but no email should be sent.
* *200* - A one-time Alert = This is an event you need to know about whenever it is detected. An email will be sent, even amongst other repeating errors.
* *25* - Nothing to see here = No information will be sent to the server.

_If a plugin's exit status is 1, that is considered a failure state for the plugin itself. A warning will be reported to the Watchman Monitoring staff for inspection of the plugin's failure._

Plugins should be maintained in the authors own github repo, which Watchman Monitoring will need to be granted access to.


## Preference Pane Options

Each plugin has the ability to provide options to be displayed in the Preference Pane.


#### Creating PreferencePane options

The vast majority of our plugins require no user interaction in order to be effective. There are cases where the ability to set an option is needed. We've solved this by adding the ability to define custom fields in our PreferencePane

The PreferencePane supports an Array named PluginOptions in the Plugins plist, which can contain a number of Dictionary entries. Each entry will be presented within the PreferencePane, so long as the dictionary contains the follow 5 keys as defined below:

**SettingStorageKey:**
	The exact name of key which the key that PreferencePane will reference in the Plugin settings plist. Will be created if it doesn't exist.

**SettingStoragePath:**
  Full path to the settings plist. If the file doesn't exist, it will be created only if the path begins `/Library/MonitoringClient/PluginSupport`.

**SettingTitle:**
	Title of the object as displayed in the PreferencePane. Must be present or the GUI object won’t be displayed in the PreferencePane.

**SettingTitleHint:**
	Optional, displayed in smaller text in the PreferencePane object to describe functionality of this particular option.

**SettingType:**
	A dictionary with two children keys: `Type` and `Value`

#### PluginOptions

|Type|Value|Description|Data Type|
|------|-----|-----|----|
|Checkbox|Positive or Negative|Positive = checked = true & set to false if not present Negative = checked = False, & set to true if not present (Positive is assumed)|bool|
|FolderPicker|[ignored]|make this a “well”, so the end user has the option to drag & drop or click Select|string|
|FilePicker|[ignored]|make this a “well”, so the end user has the option to drag & drop or click Select|string|
|PopUpMenu|{array}|First item is default unless it is a blank|string|
|Checkboxes|[ignored]|Each dictionary entry stored as a bool value, |Dictionary of bool values|
|NumberPicker|Range|(0-12) whole numbers 0 through 12, inclusive|Number|
|TextField|[max length]|Will store whatever the user enters, as entered|String|
|DisplayOnly|ignored|There will be no user options here. This will be used only to display the SettingTitle and/or Setting Hint|Empty|


## PreferencePane visibility

There are some cases when a Plugin, or it's PreferencePane options, are needed, but not until certain requirements are met.

## Disabling a Plugin

A plugin will be fully disabled when a boolean key titled `Plugin_Enabled` and set `false` in the plugin's _settings.plist file.

If the `Plugin_Enabled` key is not present, it is assumed `true`.


## Disabling the Plugin's display option

We support a boolean key titled `PrefPaneVisibility` in any given plugin's _settings.plist file.

If this key is either missing or True, the plugin will be shown in the PreferencePane.

Programmatically changing the key to False is a way to prevent the options from being displayed, though the plugin itself can still run.
