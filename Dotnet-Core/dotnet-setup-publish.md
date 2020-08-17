## **Dotnet core startup guide**

### Dotnet Core Webapi

#### Step 1: To initialize a project

```sh
$ dotnet new webapi -n project-name
```

#### Step 2: Initialize and push Initial commit to GIT Repository

```sh
$ git remote add origin https://repository_url.git
$ git commit -m "Initial"
$ gitgit push -u origin master
```

#### Step 3: Database connection
***1.Add DbContext file in the Persistence namespace***
```sh
using Microsoft.EntityFrameworkCore;

namespace project.Persistence
{
    public class DataContext : DbContext
    {
        public DataContext(DbContextOptions<DataContext> options) : base(options) {
        }
        protected override void OnModelCreating(ModelBuilder modelBuilder) {
        }
    }
}
```
***2.Configure Database***
Find the database providers link below
https://docs.microsoft.com/en-us/ef/core/providers/
 > ___MySQL Configuration___

Add Pomelo MySQL dependency in ___project.csproj___
```sh
<ItemGroup>
    <PackageReference Include="Microsoft.AspNetCore.App" />
    <PackageReference Include="Pomelo.EntityFrameworkCore.MySql" Version="2.1.2" />
</ItemGroup>
```

Add Connection String in ___appSetings.json___ or ___appSettings.development.json___
```sh
"ConnectionStrings": {
    "DefaultConnection": "server=localhost;port=3306;database=DbName;uid=root;password=*****"
  }
```

 Import Entityframework
 ```sh
 using Microsoft.EntityFrameworkCore;
 ```

Inject MySql service as dependency in ___Startup.cs->ConfigureServices(IServiceCollection services)___
```sh
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<DataContext>(options =>
        options.UseMySql(Configuration.GetConnectionString("DefaultConnection")));
    services.AddMvc();
}
```

 > ___MSSQL Configuration___
 
 Import Entityframework
 ```sh
 using Microsoft.EntityFrameworkCore;
 ```
 
 Add Connection String in ___appSetings.json___ or ___appSettings.development.json___
```sh
"ConnectionStrings": {
    "DefaultConnetion" : "Data Source=127.0.0.1,1433;Initial Catalog=DbName;User ID=sa;Password=******;"
  }
```
 
Inject MSSql service as dependency in ___Startup.cs->ConfigureServices(IServiceCollection services)___
```sh
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<DataContext>(x => x.UseSqlServer(Configuration.GetConnectionString("DefaultConnetion")));
    services.AddMvc();
}
```

#### Step 4: Add Business Entities (i.e, Models)
Create business models in Models package
Example
```sh
using System;

namespace project.Models
{
    public class User
    {
        public string Id { get; set; }
        public string Email { get; set; }
        public string Username { get; set; }
    }
}
```
Create Model Configurations in EntityConfigurations package
```sh
public class UserConfiguration : IEntityTypeConfiguration<User>
    {
        public void Configure(EntityTypeBuilder<User> builder)
        {
            // Set Table Name
            builder.ToTable("Users");

            // Set Primary Key
            builder.HasKey(u => u.Id);

            // Set Column Configuration
            builder.Property(u => u.Email).HasColumnName("Email").IsRequired();
            builder.Property(u => u.Username).HasColumnName("Username").IsRequired(false);

            // Set Relationships
            builder
                    .HasOne(u => u.Organization)
                    .WithOne(o => o.User)
                    .HasForeignKey<Organization>(o => o.Id)
                    .OnDelete(DeleteBehavior.Cascade);        
        }
    }
```

Add ___DBSet<T>___ in DbContext.cs and EntityConfiguration in ___DataContext.cs->OnModelCreating(ModelBuilder modelBuilder)___
```sh
public class DataContext : DbContext
{
    public DbSet<User> Users { get; set; }
    
    public DataContext(DbContextOptions<DataContext> options) : base(options) {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder) {
        modelBuilder.ApplyConfiguration(new UserConfiguration());
    }
}
```

#### Step 5: Migrate and Update Database
Create Migrations
```sh
$ dotnet ef migrations add Initial
```
Update database
```sh
$ dotnet ef database update
```
# Publishing dotnet core on Apache - Ubuntu

 1) Generate release build of your dotnet core application 
 ```sh
 $ dotnet publish --configuration Release
 ```
 
 2) Copy "/project_root/bin/Release/netcore_version/publish" files to your server
 Example: Copy the build files to "/var/www/yourappname/" directory of the Ubuntu
 
 3) Enable proxy and proxy_http in apcache2 web server
 ```sh
 $ sudo a2enmod proxy
 $ sudo a2enmod proxy_http
 $ sudo a2enmod headers
 $ sudo a2enmod ssl
 $ sudo a2enmod proxy_balancer
 ```
 4) Creating service to configure the dotnet core to run as Deamon
    create kestral-yourappname.service in "/etc/systemd/system" folder and paste the below line
    ```sh
        [Unit]
        Description= .NET Core Web API App running
        
        [Service]
        WorkingDirectory=/var/www/yourappname
        ExecStart=/usr/bin/dotnet /var/www/yourappname/yourappname.dll
        Restart=always
        # Restart service after 10 seconds if the dotnet service crashes:
        RestartSec=10
        KillSignal=SIGINT
        SyslogIdentifier=dotnet-appname
        User=ubuntu
        Environment=ASPNETCORE_ENVIRONMENT=Production 
        
        [Install]
        WantedBy=multi-user.target
    ```
    
    change the "User" parameter and give the proper permission
    To give full permission to the user
    ```sh
    $ sudo chmod -R 755 /var/www/yourappname
    ```
    5) Enable the kestrel service using the below command
    ```sh
    $ sudo systemctl enable kestrel-yourappname.service
    ```
    To check the status of the service
    ```sh
    $ sudo systemctl status kestrel-yourappname.service
    ```
    To restart the service
    ```sh
    $ sudo systemctl restart kestrel-yourappname.service
    ```
    6) Configure Apache2 Proxy
    ```sh
    <VirtualHost *:*>
        RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
    </VirtualHost>
    
    <VirtualHost *:80>
        ProxyPreserveHost On
        ProxyPass / http://127.0.0.1:5000/
        ProxyPassReverse / http://127.0.0.1:5000/
        ServerName www.your_domain.com
        ServerAlias *.your_domain.com
        ErrorLog ${APACHE_LOG_DIR}yourappname-error.log
        CustomLog ${APACHE_LOG_DIR}yourappname-access.log common
    </VirtualHost>
    ```
    7) Restart apache2 server
    ```sh
    $ sudo service apache2 restart
    ```
    8) Done, You will be able to use port 80 to access your WebApi(REST API endpoint)

