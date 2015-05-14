dokku-nginx-alt
==============

dokku-nginx-alt is a alternative plugin for [dokku][1]'s built in
[nginx-vhosts][2]. This plugin supports:

- Multiple custom domains for each app (e.g. myapp.com, www.myapp.com, and
  production.myapp.com can all be served by the same app). These domains
  are defined in the `VHOST` file, with each domain name on a new line.
- Ability to override `nginx.conf`. Simply place `nginx.tpl` or `nginx.ssl.tpl`
  files into the app's home directory (i.e. `/home/dokku/[app name]/`). Upon
  push, these templates will be used.

In more detail:

- Moved configuration file from `post-deploy` into separate template files:
  `nginx.conf` and `nginx.ssl.conf`.
- Ability to override default .conf files with user's own nginx configuration
  files placed in `/home/dokku/$APP/`.
- Ability to deploy an app under multiple custom domains. The `VHOST` file can
  now contain a list of domains that nginx will serve to.
- `VHOST` file is decoupled from `post-deploy` in that `post-deploy` no longer
  overwrites `VHOST` after each deploy. Custom domains are set independently
  in the `VHOST` file.
- Ability to mix SSL and non-SSL domains. For example, `VHOST` may contain
  `mydomain.com` and `*.mydomain.com` where SSL certificates only apply to
  `*.mydomain.com`. This plugin generates both SSL configurations for the
  wildcard domains and non-SSL configuration for the root domain.
- Added helper messages during deploy.

[1]: https://github.com/progrium/dokku
[2]: https://github.com/progrium/dokku/tree/master/plugins/nginx-vhosts


Installation and Usage
----------------------

This plugin has been tested on dokku version 0.2.3.

1. Install the plugin by cloning into the dokku plugins directory:
    ```sh
    git clone https://github.com/mikexstudios/dokku-nginx-alt.git /var/lib/dokku/plugins/nginx-alt
    ```
   Do not delete the existing `nginx-vhosts/` plugin that ships with dokku.

2. Then run dokku's plugin install:
    ```sh
    dokku plugins-install
    ```
   This step renames `nginx-vhosts/` to `.nginx-vhosts/` and calls
   `.nginx-vhosts/install`.

3. Create a `VHOST` file in your dokku app directory
   (e.g. `/home/dokku/[app name]/VHOST`) and add each domain name on a separate
   line.

4. To override the default `nginx.conf` and/or `nginx.ssl.conf` templates, place
   copies renamed as `nginx.tpl` and/or `nginx.ssl.tpl` in your
   `/home/dokku/[app name]/` directory. When you re-push your application, you should see
   a message like:

   ```sh
   -----> Overriding default SSL nginx.conf with detected nginx.tpl...
   ```

5. Re-push your app.


Known Issues
------------

- `backup-export` and `backup-import` hook scripts have intentionally **not** been
  included to reduce the complexity of this plugin.


License
-------

The MIT License (MIT)
