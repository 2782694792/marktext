# 一、vs、vc

```sh
#.svn
#Src/[Dd]ebug/
#Src/[Rr]elease/
#ToolPlus/Src/[Rr]elease/
#ToolPlus/Src/[Dd]ebug/
#Src_OCR/[Dd]ebug/
#Src_OCR/[Rr]elease/
#DevDriverLog/

# other file
*.txt
*.log
*LOG/
*log/

# Compiled Object files
*.slo
*.lo
#*.o
*.obj

# Precompiled Headers
*.gch
*.pch

# Compiled Dynamic libraries
#*.so
*.dylib
#*.dll

# Fortran module files
*.mod

# Compiled Static libraries
*.lai
*.la
#*.a
#*.lib

# Executables
#*.exe
#*.out
#*.app


## Ignore Visual Studio temporary files, build results, and
## files generated by popular Visual Studio add-ons.

# User-specific files
*.suo
*.user
*.userosscache
*.sln.docstates

# User-specific files (MonoDevelop/Xamarin Studio)
*.userprefs

# Build results
#[Dd]ebug/
[Dd]ebugPublic/
#[Rr]elease/
[Rr]eleases/
#x64/
#x86/
build/
bld/
[Bb]in/
[Oo]bj/

# Roslyn cache directories
*.ide/

# MSTest test Results
[Tt]est[Rr]esult*/
[Bb]uild[Ll]og.*

#NUNIT
*.VisualState.xml
TestResult.xml

# Build Results of an ATL Project
[Dd]ebugPS/
[Rr]eleasePS/
dlldata.c

*_i.c
*_p.c
*_i.h
*.ilk
*.meta
*.obj
*.pch
*.pdb
*.pgc
*.pgd
*.rsp
*.sbr
*.tlb
*.tli
*.tlh
*.tmp
*.tmp_proj
#*.log
*.vspscc
*.vssscc
.builds
*.pidb
*.svclog
*.scc

# Chutzpah Test files
_Chutzpah*

# Visual C++ cache files
ipch/
*.aps
*.ncb
*.opensdf
*.sdf
*.cachefile

# Visual Studio profiler
*.psess
*.vsp
*.vspx

# TFS 2012 Local Workspace
$tf/

# Guidance Automation Toolkit
*.gpState

# ReSharper is a .NET coding add-in
_ReSharper*/
*.[Rr]e[Ss]harper
*.DotSettings.user

# JustCode is a .NET coding addin-in
.JustCode

# TeamCity is a build add-in
_TeamCity*

# DotCover is a Code Coverage Tool
*.dotCover

# NCrunch
_NCrunch_*
.*crunch*.local.xml

# MightyMoose
*.mm.*
AutoTest.Net/

# Web workbench (sass)
.sass-cache/

# Installshield output folder
[Ee]xpress/

# DocProject is a documentation generator add-in
DocProject/buildhelp/
DocProject/Help/*.HxT
DocProject/Help/*.HxC
DocProject/Help/*.hhc
DocProject/Help/*.hhk
DocProject/Help/*.hhp
DocProject/Help/Html2
DocProject/Help/html

# Click-Once directory
publish/

# Publish Web Output
*.[Pp]ublish.xml
*.azurePubxml
# TODO: Comment the next line if you want to checkin your web deploy settings 
# but database connection strings (with potential passwords) will be unencrypted
*.pubxml
*.publishproj

# NuGet Packages
*.nupkg
# The packages folder can be ignored because of Package Restore
**/packages/*
# except build/, which is used as an MSBuild target.
!**/packages/build/
# Uncomment if necessary however generally it will be regenerated when needed
#!**/packages/repositories.config

# Windows Azure Build Output
csx/
*.build.csdef

# Windows Store app package directory
AppPackages/

# Others
*.[Cc]ache
ClientBin/
[Ss]tyle[Cc]op.*
~$*
*~
*.dbmdl
*.dbproj.schemaview
*.pfx
*.publishsettings
node_modules/
bower_components/

# RIA/Silverlight projects
Generated_Code/

# Backup & report files from converting an old project file
# to a newer Visual Studio version. Backup files are not needed,
# because we have git ;-)
_UpgradeReport_Files/
Backup*/
UpgradeLog*.XML
UpgradeLog*.htm

# SQL Server files
*.mdf
*.ldf

# Business Intelligence projects
*.rdl.data
*.bim.layout
*.bim_*.settings

# Microsoft Fakes
FakesAssemblies/

# Node.js Tools for Visual Studio
.ntvs_analysis.dat

# Visual Studio 6 build log
*.plg

# Visual Studio 6 workspace options file
*.opt

```

# 二、make、cmake

```sh
# Prerequisites
*.d

# Compiled Object files
*.slo
*.lo
*.o
*.obj

# Precompiled Headers
*.gch
*.pch

# Compiled Dynamic libraries
#*.so
*.dylib
*.dll

# Fortran module files
*.mod
*.smod

# Compiled Static libraries
*.lai
*.la
*.a
#*.lib

# Executables
*.exe
#*.out
*.app
```

# 三、git clean

永久删除文件。

## 3.1 定义

- 忽略的文件：.gitignore 中忽略的文件
- 未被跟踪的文件：没有被忽略，但是还没 git add 的文件

## 3.2 命令选项

- **f**

删除：**未被跟踪的文件**

- **d**

删除：**未被跟踪的文件夹**

- **f**

删除：**忽略**的文件

- **n**

**查看**将要删除的文件。