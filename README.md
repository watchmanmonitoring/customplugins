Custom Plugins
==============

A repository for customer built plugins, based off the included template

# Base Plugin

Here we have provided a basic example of plugin architecture.

# Plugin Operation

An effective plugin needs to perform 3 Major tasks:
* Do some work!
* Print some output.
* Exit with an appropriate status

The Monitoring Client reads the output of the plugin, and reports it back to a server along with the exit status.  The exit status lets the server know the severity of any issues this plugin found.

The name of your plugin must begin with an underscore _ 

# Exit Statuses

The server will act according to how a plugin reports and exits.  The following exit statuses are used:

* *0* - We're All Good = Information will be displayed at the server, but not emailed.
* *2* - Big Problem! = This information will be displayed on the server, and trigger an email.
* *20* - Information worth reporting was found, but no email should be sent.
* *25* - Nothing to see here = Information will not be sent to the server (and not emailed)

_A future version of the server will gather & display messages sent with status 20._

_If a plugin's exit status is 1, the warning will be reported to the Watchman Monitoring staff, for inspection of the plugin's failure._




# Preference Pane Options

Each plugin has the ability to provide options to be displayed in the Preference Pane.











A private repository is available as well.
https://github.com/watchmanmonitoring/subscriberplugins
