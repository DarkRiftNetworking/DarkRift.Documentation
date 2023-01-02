# DarkRift.Documentation
This is the source repository for [DarkRift](https://www.darkriftnetworking.com) documentation.

# Building the Documentation
This documentation is built using [DocFx](https://dotnet.github.io/docfx/).

Once you have installed DocFx you can build this project by simply running `docfx` from the project directory. The built documentation will be output to the `_site` folder.

Unless you have the source code for DarkRift, DocFx will be unable to generate the reference documentation. This should not stop the build and should only throw warnings you can ignore.

If you do have the source you can edit the `metadata/source` elements in the docfx.json file to point at the source code and thus build the reference documentation.

# Diagrams
Diagrams are made using [diagrams.net](https://app.diagrams.net) and the XML save file should be version controlled in the directory the diagrams are used in with the the PNG exports saved in `/images`.

## Source Code License
Documentation is licensed under MIT [LICENSE.md](LICENSE.md). Source code is licensed under MPL 2.0, with some exceptions where MIT applies. See DarkRift's [LICENSE.md](https://github.com/DarkRiftNetworking/DarkRift/blob/master/LICENSE.md).

# Contributions and Issues
Contributions and issues are most welcome.

Any issues should be solely related to documentation however, issues related to the rest of DarkRift should be posted [here](https://github.com/DarkRiftNetworking/DarkRift-Networking) instead.

All contributions should be in well written english (we're not currently providing documentation in any other language), we might be quite strict about this in PRs so don't take it personally!

And of course, any spelling/grammar fixes are actively encouraged!

## Code of Conduct
Be civil. DarkRift's [code of conduct](https://github.com/DarkRiftNetworking/DarkRift/blob/master/CONTRIBUTING.md) applies here too.