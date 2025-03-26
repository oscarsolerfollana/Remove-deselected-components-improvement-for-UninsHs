# Remove deselected components improvement for UninsHs

> This is an improvement for the Inno Setup's tool: UninsHs, developed by Han-soft.
<br/>

## Introduction

[Inno Setup](https://jrsoftware.org/isinfo.php) is a free, feature-rich installer for Windows programs. It's widely used for its flexibility, compactness, and scripting capabilities.

`Uninshs` is a script developed by [Han-soft](https://www.han-soft.com) that extends Inno Setup's uninstallation features. It allows the installer to handle the removal of components during an installation update or modification, based on previously selected options.

## Improvement

This version of the `Uninshs` script includes an important enhancement:  
When modifying an existing installation, any components that were **deselected** by the user will now be **automatically uninstalled**. This behavior ensures the installed application always reflects the user's current selection of components, simplifying updates and maintenance.

## Code

For the improvement to work, modify the uninshs.iss as follows:

In the `[CODE]` section, add this variable:

```pascal

var
  InstalledComponents: string;

```

<br/>

Replace the `UninsHs_InitializeWizard()` procedure with this one:

```pascal

procedure UninsHs_InitializeWizard();
  
begin
  if IsMaintenance() then
  begin
  
  if RegQueryStringValue(HKEY_LOCAL_MACHINE, RegPathUninstall() + '{#AppId}_is1', 'Inno Setup: Selected Components', InstalledComponents) then
    begin
    end
    else
    begin
      MsgBox('Components data not found in Registry', mbError, MB_OK);
      InstalledComponents := '';
    end;
  
    CreateMaintenance();
    #if !YesNo(SetupSetting("DisableWelcomePage"))
    ClickNext();
    #endif
  end;
end;

```
<br/>
Add the following procedure as well:

```pascal

procedure UninsHs_CurStepChanged(CurStep: TSetupStep);
var
  i: Integer;
  ComponentPath: string;
  InstalledList: TStringList;
begin  
  if (CurStep = ssInstall) and IsMaintenance() and rdbModify.Checked then
  begin
    InstalledList := TStringList.Create;
    try
      InstalledList.Delimiter := ',';  
      InstalledList.StrictDelimiter := True;
      InstalledList.DelimitedText := InstalledComponents;
      for i := 0 to InstalledList.Count - 1 do
      begin
        if not WizardIsComponentSelected(InstalledList[i]) then
        begin
          ComponentPath := ExpandConstant('{app}\') + InstalledList[i];
          if DirExists(ComponentPath) then
          begin
            DelTree(ComponentPath, True, True, True);
          end;
        end;
      end;
    finally
      InstalledList.Free;
    end;
  end;
end;

```

<br/>

Finally, call the method `UninsHs_CurStepChanged` from your script file:

```pascal
procedure CurStepChanged(CurStep: TSetupStep);
begin
  UninsHs_CurStepChanged(CurStep);
end;

```
