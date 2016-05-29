## Multi-domain web sites
Mikser has build in support for multi-domain web site. Let's say that you are creating a web site and a blog which share common look but are placed under different domain or sub-domains. To achieve this in Mikser you can put documents in different folders for each domain. 

### Asset replication
In documents folder you create two folders: `example.com` and `blog.example.com`, then in Mikser configuration you add:

```yaml
shared:
- example.com
- blog.example.com
```

This configuration will replicate the `files` in both folders. In `out` folder you will get two folders that will have all the asses in there. And if you have you used 'href' to reference resources all relative URLs will be correct.

### Shared resources
If you have specific assets for each domain you can create a 'shared' folder inside your project folder with the domain folders `example.com` and `blog.example.com` inside and put there the specific files for each domain.

### Virtual hosts
If you want to access your server with those two domains and have it create virtual hosts for each one of them you can do it by adding an extra line to the Mikser configuration.

```yaml
domains: true
```

If point your DNS to the IP of the server running Mikser and configure Mikser to run on port 80 you will be able to access your sites directly.