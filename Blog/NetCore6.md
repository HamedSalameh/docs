### New hosting model ###

The minimal hosting model  
* Significantly reduces the number of files and lines of code required to create an app. Only one file is needed with four lines of code.
* Unifies Startup.cs and Program.cs into a single Program.cs file.
* Uses top-level statements to minimize the code required for an app.
* Uses global using directives to eliminate or minimize the number of using statement lines required.


Changes:  
ConfigureServices is replaced with WebApplication.Services.