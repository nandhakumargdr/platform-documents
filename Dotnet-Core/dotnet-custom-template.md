### .Net Core WebAPI Custom Template Creation Guide
---

**Step 1:** Create .Net core web API Project using .Net CLI
```sh
$ dotnet new webapi -n PROJECT_NAME 
```

Example: 
```sh
$ dontnet new webapi -n BasicWebApiTemplate

```

**Step 2:** Modify the features of your template

**Step 3:** Creating the Template Configuration File
1. Create a folder **.template.config** in the root of your .NetCore WebAPI Project
2. With in the **.template.config** directory, we need to create **template.json** file
3. Paste the below configuration and modify the file depends on your requirement

```json
  {
  "$schema": "http://json.schemastore.org/template",
  "author": "AUTHOR_NAME", 
  "classifications": ["WebApi"], // Tags used to search for the template.
  "name": "Basic WebAPI Template", 
  "identity": "CompanyName.BasicWebApi.CSharp", // A unique ID for the project template.
  "shortName": "basicmysqlwebapi", // You can create the project using this short name instead of the one above.
  "tags": {
      "language": "C#"
  }, // Specify that this template is in C#.
  "sourceName": "BasicWebApiTemplate", // namespace or csproj of your template
  "preferNameDirectory": "true"
  }
```
**Step 4:** Installing locally created template

```sh
$ dotnet new --install /path/to/parent/of/.template.config
```
Example: 
```sh
$ dotnet new --install ~/Templates/BasicWebApiTemplaye/
```
**Step 5:** Using the Template
To list the installed template
```sh
$ dotnet new --list
```

```sh
$ dotnet new templateName --name MyProject
```

To reset all template
```sh
$ dotnet new --debug:reinit
```

***Step 6:*** To distribute template as Nuget package

Create a folder for the NuGet package. For the tutorial, the folder name CompanyName.WebApiTemplate.CSharp is used, and the folder is created inside a Documents\NuGetTemplates folder in the user's profile. Create a folder named content inside of the new template folder to hold the project files.

Copy the contents of your project folder, together with its .template.config/template.json file, into the content folder you created.

Next to the content folder, add a nuspec file. The nuspec file is an XML manifest file that describes a package's contents and drives the process of creating the NuGet package.

```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2012/06/nuspec.xsd">
  <metadata>
    <id>CompanyName.WebApiTemplate.CSharp</id>
    <version>1.0.0</version>
    <description>
      Template Description here
    </description>
    <authors>AuthorName</authors>
    <packageTypes>
      <packageType name="Template" />
    </packageTypes>
  </metadata>
</package>
```

Create nuget file using nuspec
```sh
nuget pack ~\CompanyName.WebApiTemplate.CSharp\CompanyName.WebApiTemplate.CSharp.nuspec
```
We can see the path of nuget package after running the above command on command prompt
```
Successfully created package '/Users/username/ComapanyName.BasicWebApiMySQL.CSharp.1.0.0.nupkg'.
```

**Step 7:** To install template from nuget package file i.e., .nupkg file

Windows
```sh
$ dotnet new -i C:\Users\<USER>\CompanyName.WebApiTemplate.CSharp.1.0.0.nupkg
```
Mac
```sh
$ dotnet new -i \Users\<USER>\CompanyName.WebApiTemplate.CSharp.1.0.0.nupkg
```

#### For more information
https://docs.microsoft.com/en-us/dotnet/core/tutorials/create-custom-template