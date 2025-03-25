# UninsHs improvement

> This is an improvement for the Inno Setup's tool: UninsHs, developed by Han-soft.
<br/>

## Introduction

[Inno Setup](https://jrsoftware.org/isinfo.php) is a free, feature-rich installer for Windows programs. It's widely used for its flexibility, compactness, and scripting capabilities.

`Uninshs` is a script developed by [Han-soft](https://www.han-soft.com) that extends Inno Setup's uninstallation features. It allows the installer to handle the removal of components during an installation update or modification, based on previously selected options.

## Improvement

This version of the `Uninshs` script includes an important enhancement:  
When modifying an existing installation, any components that were **deselected** by the user will now be **automatically uninstalled**. This behavior ensures the installed application always reflects the user's current selection of components, simplifying updates and maintenance.

## Code

