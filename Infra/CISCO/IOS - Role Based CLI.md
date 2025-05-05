Before an administrator can create a view, AAA must be enabled using the **aaa new-model** command. To configure and edit views, an administrator must log in as the root view using the **enable view** privileged EXEC command.

There are five steps to create and manage a specific view.

---

**Step 1.** Enable AAA with the **aaa new-model** global configuration mode command. Exit and enter the root view with the **enable view** command.
```ios
Router# enable [view [view-name]]
```
**`view`** : This parameter enters root view if no view-name is specified. the view parameter is required to configure a CLI view.
**`view-name`** : This parameter enters or exists a specified CLI view

**Step 2.** Create a view using the **parser view** _view-name_ global configuration mode command. This enables the view configuration mode. Excluding the root view, there is a maximum limit of 15 views in total.
```ios
Router(config)# parser view view-name
```

**Step 3.** Assign a secret password to the view using the **secret** _password_ view configuration mode command.
This sets a password to protect access to the view. The password must be created immediately after creating a view, otherwise, an error message will appear.
```ios
Router(config-view)# secret password
```

**Step 4.** Assign commands to the selected view using the **commands** _parser-mode_ command in view configuration mode.
```ios
Router(config-view)# commands parser-mode {include | include-exclusive | exclude} [all] [interface interface-name | command]
```
**`commands`** : Adds commands or interfaces to a view
**`parser-mode`** : The mode in which the specified command exists; for example, `EXEC` mode.
**`ìnclude`** : Adds command or an interface to the view and follows the same command or interface.
**`include-exclusive`** : Adds to the view and excludes the same interface or command from bring added to all others views
**`exclude`** :  Excludes a command or an interface from the view
**`all`** : A "Wilcard" that allows











