### Docker containerizing .Net Core

**Step 1:** Create a .Net Core Project (WebApi)
```sh
$ dotnet new webapi -n project-name
```

Create release files
```sh
$ dotnet publish -c release -o app/
```

**Step 2:** Create ___dockerfile___ and ___.dockerignore___
Paste the belo code in ___dockerfile___

```dockerfile
FROM microsoft/dotnet:sdk AS build-env
WORKDIR /app
COPY /app /app
ENTRYPOINT [ "dotnet", "project-name.dll" ]
```

Paste the below lines in ___.dockerignore__ file to avoid the unwanted folders
```dockerfile
obj\
bin\
```

**Step 3:** Build and create docker image

```sh
$ docker build -t project-name .
```

**Step 4:** Running docker Image
```sh
$ docker run -p 5000:80 project-name
```

Done! Now, .Net Core Api is running on port 5000