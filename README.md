django-deploy
=============

Ansible role to deploy a Django app to Plesk.

Add the role `django-deploy`.

Required variables 
------------------

(passed as extra variables):

- `project`: **Django project name**, will also be used as virtualenv name.

Optional variables
------------------

(passed as extra variables):

- `loaddata`: Set to `true` in order to load data from `conf.data`.
- `flush`: Set to `true` in order to flush data before `loaddata`. Depends on `loaddata` variable to be `true`.
- `newrelic`: New Relic license key. Omit to skip New Relic. Recommended to add a default in playbook.

Required Configuration variables 
--------------------------------

(better to keep in configuration file as shown in examples):

- `conf`: A dictionary to help organize variables, containing:
- `conf.app_name`: The application name (human description), used in New Relic too.
- `conf.domain_name`: Domain name to be used in Apache conf. Omit to skip configure Apache in Plesk.
- `conf.dev_path`: The local path of the project, eg ~/workspace/python/myproject.
- `conf.host_path`: The remote path, where the wsgi index script lies, eg /var/www/vhosts/subscription/domain.
- `conf.host_user`: The remote user to whom to transfer ownership after operations.

Optional Configuration variables
--------------------------------

- `conf.name`: The application name (machine name), the same as the Python app. Default: `{{ project }}`
- `conf.python_version`: The python version to install (major.minor), default: `3.5`.
- `conf.virtualenv_dir`: Virtualenvs home directory, default to vars' respective option.
- `conf.wsgi_script`: The wsgi script, default: `index.wsgi`.
- `conf.compile_msgs`: Set to `true` in order to compile i18n messages.
- `conf.memcached`: Set to `true` in order to clear cache.
- `conf.data`: A list of the dump files to load data from, eg `[project, auth]`, omit to skip loaddata. Depends on `loaddata` variable to be `true`.

Other default variables
-----------------------

- `ansible_user_id`: The user id with access to mysql and mysql database.
- `virtualenv_dir`: Virtualenvs home directory. Set to `false` to disable virtualenv. Default: `/var/virtualenvs`.
- `django_settings`: The Django settings module (.py) to run manage.py from, default: `settings_live`.
- `plesk_server_group`: Plesk server group, default: `psaserv`.
- `plesk_account_group`: Plesk account group, default: `psacln`.

Playbook examples
-----------------

See the folder `examples/`:

- `django_deploy.yml`: playbook example
- `django_sites.yml`: configuration example

Task description
----------------

1. Check that project path in dev as specified in configuration is valid and has wsgi script

   Checks that there is a valid local path specified in `django_sites.yml` and an `index.wsgi` exists.
   Avoids deploying wrong files.

2. Check if gulp file exists

   So that to execute next step.

3. Execute gulp

   Execute `gulp --production` locally.

4. Set Python executable

   Auxiliary task which sets a variable with the Python executable.

5. Make virtualenv

   Create a virtualenv if it does not exist.

6. Check New Relic conf file in source

   Otherwise create from template.

7. Configure New Relic

   If `newrelic` is defined configures New Relic.

8. Open ownership

   Open the target directory ownership to allow file copy.

9. Sync files

   Syncs files to target directory.

   :warning: Make sure that you have synced back any user uploaded files in `media` with manual `rsync`. 

10. Upgrade pip

    Upgrades pip, setup, wheel. 

11. Install pip requirements

    Install `requirements.txt` in virtualenv.

12. Set Django admin executable

    Auxiliary task which sets a variable with the Django admin executable.

13. Set Django admin settings

    Auxiliary task which sets a variable with the custom settings file (default `django_settings` variable).

14. Migrate

    Runs migrations.

15. Flush data

    Flushes data if `loaddata` and `flush` are both true.

16. Load data

    Loads data if `loaddata` is true.

17. Collect static

    Runs collect static.

18. Compile messages

   Compiles i18n messages if `compile_msgs` is true.

19. Clear cache

   Clears cache if `memcached` is true.

20. Post deployment script

    Runs an optional `post_script`.

21. Close ownership to Plesk account group

    Reset target directory ownership to the appropriate Plesk permissions.

22. Close ownership to Plesk server group

    Reset target directory ownership to the appropriate Plesk permissions.

23. Configure Apache

   Configure Apache to execute the wsgi script using the provided template file.
   Only executes if a `domain_name` is provided.
   If the configuration changes, causes Plesk to reconfigure the domain in the end (notifies handler).
   If the configuration changes, causes Apache to restart in the end (notifies handler).

