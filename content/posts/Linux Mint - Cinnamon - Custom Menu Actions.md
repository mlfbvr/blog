---
title: Linux Mint - Cinnamon - Custom Menu Actions
date: 2026-02-07
draft: true
tags:
  - blog
---
If you're like me and like Linux Mint for it's stability and consistency, but also it's ability to be customised to your liking, then you might enjoy having custom actions available from the context menu on your desktop.

## Define the action
You define the action in configuration file stored in ~/.local/share/nemo/actions, named <something>.nemo_action. In this example, I've created the file ~/.local/share/nemo/action/vscode.nemo_action to have a "Open in VS Code" context menu option.

```bash
[Nemo Action]
Name=Open in VS Code
Comment=Open the selected file or folder in Visual Studio Code
Exec=code "%F"
Icon-Name=visual-studio-code
Selection=Any
Extensions=any;
# Optional condition: show only if the 'code' command is found
# Conditions=exec program == code
```

That's it! Here's a rundown of the lines above and what they do:
- Name -- The name of the action as it appears in the menu
- Comment -- The "description" that will be displayed in nemo when hovering the mouse over the action
- Exec -- The command to execute
- Icon-Name: The name of the icon file to display
- Selection -- 