## Kill Port

> sudo kill -9 `sudo lsof -t -i:9001`

Issue the following command to open port 1191 for TCP traffic.
> sudo ufw allow 1191/tcp

Issue the following command to open a range of ports.
> sudo ufw allow 60000:61000/tcp

Issue the following command to stop and start Uncomplicated Firewall (UFW).
> sudo ufw disable 

> sudo ufw enable

## Find process

> sudo lsof -i -P -n

> sudo lsof -i -P -n | grep LISTEN

> sudo ps -Flww -p 827
