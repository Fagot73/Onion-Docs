---
title: Installing the Console
layout: guide.hbs
columns: two
devices: [ Omega2 ]
order: 2
---

## Installing the Console {#console-series-installing-the-console}

<!-- // Show how to install the console through the setup-wizard, or through the command line -->
The Console can be installed through the Setup Wizard, or through the command line.
Follow this [guide](#first-time-setup) on setting up your Omega, if you have not already done so.

### Installing Using the Setup Wizard

> **NOTE** This section is only valid for firmwares v0.2.0 and below. In v0.2.1 the Console and Setup Wizard were replaced with OnionOS and a new setup wizard. Learn more about [OnionOS on the Onion blog](https://onion.io/2bt-onion-os/).

If you have set up the Omega and didn't install the console, skip to the Software page and make sure the checkbox is checked.
![checkbox](https://raw.githubusercontent.com/OnionIoT/Onion-Docs/master/Omega2/Documentation/Get-Started/img/installing-console-checkmark.png)

Click the Install Console button and your Omega will begin installing the Console. This process takes about 5 minutes.

> This process can take longer than 5 minutes depending on your download speeds.

Refresh the page and you should see the Console login page.
![login-page](https://raw.githubusercontent.com/OnionIoT/Onion-Docs/master/Omega2/Documentation/Get-Started/img/installing-console-login.png)

The default login info is:

```
username: root
password: onioneer
```

### Installing Using the Command Line

<!-- command line console installation instructions -->
```{r child = './Installing-the-Console-Component-Command-Line-Install.md'}
```

Now you'll be able to [access the Omega's Console using your browser](#access-the-console)!


#### Behind the Scenes

<!-- console install tool explanation -->
```{r child = './Installing-the-Console-Component-Command-Line-Install-Explanation.md'}
```


<!-- // TODO batch 3 or 4: add a section on navigating and using the console -->

<!-- // TODO: find an appropriate place to mention that oupgrade shouldn't be run from the Terminal app on the console -->
