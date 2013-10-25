dokku-nginx-alt
==============

dokku-nginx-alt is a alternative plugin for [dokku][1] that configures [nginx][2]
as a reverse proxy to deployed dokku app. There are a number of design changes
between this plugin and the standard built in [dokku-vhosts][3] plugin:

- Moved configuration file from `post-deploy` into separate template files:
  `nginx.conf` and `nginx.ssl.conf`.
- Ability to override default .conf files with user's own nginx configuration
  files placed in `/home/git/$APP`.
- Ability to deploy an app under multiple custom domains. The `VHOST` file can 
  now contain a list of domains that nginx will serve to.
- Command to show/add/remove/clear custom domains in a heroku-like manner. e.g., 
  `dokku domains:add myapp mydomain.com`. 
- `VHOST` file is decoupled from `post-deploy` in that `post-deploy` no longer
  overwrites `VHOST` after each deploy. Custom domains are set independently
  using the `domains:add` command and dokku no longer needs to guess at the 
  domain.
- Ability to mix SSL and non-SSL domains. For example, `VHOST` may contain
  `mydomain.com` and `*.mydomain.com` where SSL certificates only apply to
  `*.mydomain.com`. This plugin generates both SSL configurations for the
  wildcard domains and non-SSL configuration for the root domain.
- Added helper messages during deploy.

[1]: https://github.com/progrium/dokku
[2]: http://nginx.org
[3]: https://github.com/progrium/dokku/tree/master/plugins/nginx-vhosts

Installation and Usage
----------------------

1. Remove the default `nginx-vhost` plugin:
   ```sh
   rm -rf /var/lib/dokku/plugins/nginx-vhost
   ```
   
2. Install the plugin by cloning into the dokku plugins directory:
    ```sh
    git clone https://github.com/mikexstudios/dokku-nginx-alt.git /var/lib/dokku/plugins/dokku-nginx-alt
    ```
3. Re-push your app.
4. To show/add/remove/clear custom domains, see: `dokku domains:help`:

   ```sh
    $ dokku domains:help
    Configure VHOST domains:
      domains <appname>               # list custom domains for app
      domains:help <appname>          # this help message
      domains:add <appname> DOMAIN    # add a custom domain to app
      domains:clear <appname>         # clear all custom domains for app
      domains:remove <appname> DOMAIN # remove a custom domain from app
   ```
   
5. To over-ride the default `nginx.conf` and `nginx.ssl.conf` templates, place 
   copies renamed as `nginx.template` and `nginx.ssl.template` in your
   `/home/git/$APP` directory. When you re-push your application, you should see
   a message like:

   ```sh
   -----> Overriding default SSL nginx.conf with detected nginx.template...
   ```
