# DarkRift.Documentation
This is the source repository for [DarkRift](https://www.darkriftnetworking.com) documentation.

# Building the Documentation
This documentation is built using [docfx](https://dotnet.github.io/docfx/). 

Once you have installed docfx you can build this project by simply running `docfx` from the project directory. The built documentation will be output to the `_site` folder.

Unless you have the source code for DarkRift, docfx will be unable to generate the reference documentation. This should not stop the build and should only throw warnings you can ignore.

If you do have the source you can edit the `metadata/source` elements in the docfx.json file to point at the source code and thus build the reference documentation.

# Contributions and Issues 
Contributions and issues are most welcome.

Any issues should be solely related to documentation however, issues related to the rest of DarkRift should be posted [here](https://github.com/DarkRiftNetworking/DarkRift-Networking) instead.

All contributions should be in well written english (we're not currently providing documentation in any other language), we might be quite strict about this in PRs so don't take it personally!

And of course, any spelling/grammar fixes are actively encouraged!
