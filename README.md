# Aardvark.Fake

This repository contains some useful extensions for the [Fake][1]/[Paket][2] infrastructure, 
which we use in our projects, such as [Aardvark.Rendering][3] and [Aardvark.Base][4].
Most importantly, AdditionalSources.fsx provides ``cabal add-source`` like functionality for referencing source versions of dependent packages.

in our packages we typically use a paket.dependencies config as such:

```
group Build
content: none
source https://vrvis.myget.org/F/aardvark_public/api/v3/index.json
source https://api.nuget.org/v3/index.json

github vrvis/Aardvark.Fake:standalone 
```

while the build script might look like (DefaultSetup is the main entry script):

```
#load @"paket-files/build/vrvis/Aardvark.Fake/DefaultSetup.fsx"

open Fake
open Aardvark.Fake
open Fake.Testing

do Environment.CurrentDirectory <- __SOURCE_DIRECTORY__

DefaultSetup.install ["src/Aardvark.sln"]
```

Thus, when used that way, the build script provides the following commands:

```
As a result, the build script provides the following commands:
  syntax: build [Target] where target is one of the following
    Default (which is executed when no target is given)
      same like compile but also copying native dependencies (from libs/Native/PROJECTNAME)
      to bin/Release and injecting them as resource into the resulting dll/exe
    Compile
      builds the project's solution to bin/Release
    Clean
      deletes all output files in bin/Debug and bin/Release
    CreatePackage
      creates packages for all projects having a paket.template using the current
      git tag as version (note that the tag needs to have a comment).
      the resulting packages are stored in bin/*.nupkg
    Push
      creates the packages and deploys them to all feeds specified in deploy.targets
    Restore
      ensures that all packages given in paket.lock are installed
      with their respective version.
    Install
      installs all packages specified in paket.dependencies and
      adjusts project files according to paket.references (next to the project)
      may also perform a new resolution when versions in paket.dependencies have changed.
    Update [regex]
      searches for newer compatible version (according to paket.dependencies)
      and installs them if possible. this target also adjusts the project files
    UpdateBuildScript
      updates the build script and its dependencies.

 advanced features
   AddSource <folder>
     adds the repository located in <folder> as source dependecy causing all packages
     referenced from that repository to be overriden by a locally built variant.
     Note that these overrides are not version-aware and will override all packages
     without any compatibility checks.
     Furthermore these source packages \"survive\" Install/Update/Restore and
     are rebuilt (upon restore/install/update) when files are modified in the source folder
   RemoveSource <folder>
```

[1]: http://fsharp.github.io/FAKE/
[2]: https://github.com/fsprojects/Paket
[3]: https://github.com/vrvis/aardvark.rendering
[4]: https://github.com/vrvis/aardvark

