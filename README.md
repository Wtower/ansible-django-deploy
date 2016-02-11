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

- `loaddata`: A list of the dump files to load data from, eg `[project, auth]`, omit to skip loaddata.
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
- `conf.wsgi_script`: The wsgi script, default: `index.wsgi`.
- `conf.compile_msgs`: Set to `true` in order to compile i18n messages.
- `conf.memcached`: Set to `true` in order to clear cache.

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
