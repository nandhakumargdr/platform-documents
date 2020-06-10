### Steps to create virtual host in apache2 server
1. Create a config file (eg: domain.com.conf) in /etc/apache2/sites-available/ folder
```sh
<VirtualHost *:80>

  # Admin email, Server Name (domain name) and any aliases
  ServerAdmin email@domain.com
  ServerName  domain.com
  ServerAlias www.domain.com

  # Index file and Document Root (where the public files are located)
  DirectoryIndex index.html
  DocumentRoot /home/user/public_html/domain.com/
  
  # Give permision to get full permission
  <Directory /home/user/public_html/domain.com/>
    AllowOverride All
    Require all granted
  </Directory>
  
</VirtualHost>
```

2.Disable the default config file 
```sh
$ sudo a2dissite 000-default.conf
```
3.Enable the newly created config file
```sh
$ sudo a2ensite domain.com.conf
```
4.List site configs
```sh
$ sudo a2query -s
```
5.Restart apacahe2 server
```sh
$ sudo service apache2 restart
or
$ sudo /etc/init.d/apache2 restart
```

### Steps to create virtual host in apache2 server

1. Create a config file (eg: multiple-domain.conf) in "/etc/apache2/sites-available/" folder
```sh
<VirtualHost *:80>

  # Admin email, Server Name (domain name) and any aliases
  ServerAdmin email@domain1.com
  ServerName  domain1.com
  ServerAlias www.domain1.com

  # Index file and Document Root (where the public files are located)
  DirectoryIndex index.html
  DocumentRoot /home/user/public_html/domain1.com/
  
  # Give permision to get full permission
  <Directory /home/user/public_html/domain1.com/>
    AllowOverride All
    Require all granted
  </Directory>
  
</VirtualHost>
```

<VirtualHost *:80>

  # Admin email, Server Name (domain name) and any aliases
  ServerAdmin email@domain2.com
  ServerName  domain2.com
  ServerAlias www.domain2.com

  # Index file and Document Root (where the public files are located)
  DirectoryIndex index.html
  DocumentRoot /home/user/public_html/domain2.com/
  
  # Give permision to get full permission
  <Directory /home/user/public_html/domain2.com/>
    AllowOverride All
    Require all granted
  </Directory>
  
</VirtualHost>
```

Reference: https://httpd.apache.org/docs/2.4/vhosts/examples.html
