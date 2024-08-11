# lessons-learned-log

This documentation serves as a record of programming errors and the solutions applied to resolve them.

## 11/08/2024: IONOS Support Team Cannot Access Private Repo
- Error: IONOS support team is unable to investigate further if repository is set as private
- Solution:
    - make repository public
    - add the [ionos-deploy-now-support GitHub account](https://github.com/ionos-deploy-now-support) as a collaborator for the repository 

## 11/08/2024: IONOS Deploy Now, Internal Server Error 500, Error when redirecting to subpages
- Error: Internal Server Error when redirecting to subpages
- Reason: server is unable to resolve the request
    - usually, when the error shows only on subpages and/or certain elements on a page, the error resides in the .htaccess file
- Further Description: .htaccess file
    - part of Apache, [.htaccess files provide a way to make configuration changes on a per-directory basis](https://httpd.apache.org/docs/2.2/howto/htaccess.html)
    - [is a directory-level configuration file supported by several web servers, used for configuration of website-access issues, such as URL redirection, URL shortening, access control (for different web pages and files)](https://en.wikipedia.org/wiki/.htaccess)
    - [a site could have more than one .htaccess file, and the files are placed inside the web tree](https://en.wikipedia.org/wiki/.htaccess)
    - [act as subset of the server's global configuration file for the directory that they are in](https://en.wikipedia.org/wiki/.htaccess)
    - [mostly used in cases where you don't have access to the main configuration files](https://stackoverflow.com/questions/13170819/what-is-htaccess-file)
- Solution: add a second .htaccess file at the root of the deployment with the following content as shown in the Laravel test repository in the [Deploy Now GitHub account](https://github.com/ionos-deploy-now-support/laravel/tree/main):
```
# enable gzip compressing for the following mime-types
<IfModule mod_deflate.c>
    AddOutputFilterByType DEFLATE text/plain
    AddOutputFilterByType DEFLATE text/html
    AddOutputFilterByType DEFLATE text/xml
    AddOutputFilterByType DEFLATE text/shtml
    AddOutputFilterByType DEFLATE text/css
    AddOutputFilterByType DEFLATE application/xml
    AddOutputFilterByType DEFLATE application/xhtml+xml
    AddOutputFilterByType DEFLATE application/rss+xml
    AddOutputFilterByType DEFLATE application/javascript
    AddOutputFilterByType DEFLATE application/x-javascript
</IfModule>

### IMPORTANT ###
### Because we can't change the webroot folder to a subfolder of the document root, we need this rewrite rules ###

RewriteEngine on

# serve existing files in the /public folder as if they were in /
RewriteCond %{REQUEST_URI} !public/
RewriteRule (.*) /public/$1 [L]

# route everything else to /public/index.php
RewriteRule ^ /public/index.php [L]
```