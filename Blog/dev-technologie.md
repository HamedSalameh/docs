## Copilot ##

Demo 0:
// Implement a binary search tree
manually type : public class BinarySearchTree

Demo 1:
// download resource from the web by url

Demo 2:
Create a contoller named EquipmentController

Demo 3: 
Create a new docker compose file


## Project Tye ##

Tye can:

Simplify microservices development by making it easy to:
+ Run many services with one command
+ Use dependencies in containers
+ Discover addresses of other services using simple conventions

Deploy .NET applications to Kubernetes by:
+ Automatically containerizing .NET applications
+ Generating Kubernetes manifests with minimal knowledge or configuration
+ Using the same conventions as development to keep it 
+ consistent

working with Tye;
+ install tye
  `dotnet tool install --global Microsoft.Tye --version 0.10.0-alpha.21420.1 --configfile .\NuGet.Config`  
+ run the command `tye init` from the solution folder. this will make tye auto detect all our projects within the solution. and produce a manifest.
+ run the command `tye run`
+ Tye will give me a dashboard to check my services.


-- show what we have 
-- explain the binings are not working

+ install the tye extentions in the relevant project:
  `dotnet add package Microsoft.Tye.Extensions.Configuration --version 0.10.0-alpha.21420.1 --source https://api.nuget.org/v3/index.json`

-- update the tye manifest with binding
-- update the HttpClient as builder with the new config: 

+ finally, execute `tye run` from the solution folder