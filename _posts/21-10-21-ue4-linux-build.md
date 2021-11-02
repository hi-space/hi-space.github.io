---
title: "[Unreal] Linux Build Project Scripts"
category: Game
tags: env unreal
---

## Generating project files for your project
To generate project files for your project, you need to run this command from the UnrealEngine folder:

```sh
./GenerateProjectFiles.sh -project="/home/user/Documents/Unreal\ Projects/MyProject/MyProject.uproject" -game -engine  -vscode
```

<!--more-->

## Scripts

```sh
#!/bin/bash

set -e

SCRIPT_DIR=$(readlink -f $(dirname ${BASH_SOURCE[0]}))

if [ -z $UE4_ROOT ] ; then
    echo "Usage: $0"
    echo "UE4_ROOT evironment variable should be specified"
    exit 1
fi

#### Workarround for 4.22.3
# Enable rtti for all modules by default
sed -i "s/no-rtti/rtti/g" ${UE4_ROOT}/Engine/Source/Programs/UnrealBuildTool/Platform/Linux/LinuxToolChain.cs


UBT="${UE4_ROOT}/Engine/Binaries/DotNET/UnrealBuildTool.exe"

# Setup Mono
source ${UE4_ROOT}/Engine/Build/BatchFiles/Linux/SetupMono.sh ${UE4_ROOT}/Engine/Build/BatchFiles/Linux

# First make sure that the UnrealBuildTool is up-to-date
if ! xbuild /property:Configuration=Development /verbosity:quiet /nologo ${UE4_ROOT}/Engine/Source/Programs/UnrealBuildTool/UnrealBuildTool.csproj; then
  echo "Failed to build to build tool (UnrealBuildTool)"
  exit 1
fi

echo ##### [Configuration]
echo
echo Project = $SCRIPT_DIR/ProceduralTest.uproject
echo UnrealBuildTool = $UBT

echo
echo
echo ##### [Build - Development Editor Mode]
echo

mono $UBT\
    Development\
    Linux\
    -Project="$SCRIPT_DIR/ProceduralTest.uproject"\
    -TargetType=Editor\
    -Progress\
    -NoHotReloadFromIDE
exit $?
```