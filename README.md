# logrotate

![Build Status](https://travis-ci.org/nickhammond/ansible-logrotate.svg?branch=master)

Installs logrotate and provides an easy way to setup additional logrotate scripts by
specifying a list of directives.

## Requirements

None

## Role Variables

**install_logrotate**: A boolean which tell whether logrotate should be installed or not

**logrotate_scripts**: A list of logrotate scripts and the directives to use for the rotation.

* name - The name of the script that goes into /etc/logrotate.d/
* path - Path to point logrotate to for the log rotation
* options - List of directives for logrotate, view the logrotate man page for specifics
* scripts - Dict of scripts for logrotate (see Example below)


```
logrotate_scripts:
  - name: rails
    path: "/srv/current/log/*.log"
    options:
      - weekly
      - size 25M
      - missingok
      - compress
      - delaycompress
      - copytruncate
```

**logrotate_run_hourly**: Set whether logrotate cron should run hourly (if true), or normally (daily). This assumes /etc/cron.daily/logrotate exists

**restart_command**: Set the restart command, invoke by passing `tags: restart_cron`. _Defaults to '/etc/init.d/crond restart'_

## Dependencies

None

## Example Playbook

Setting up logrotate for additional Nginx logs, with postrotate script (assuming this role is located in `roles/logrotate`).

```
- role: logrotate
  install_logrotate: true
  logrotate_scripts:
    - name: nginx
      path: /var/log/nginx/*.log
      options:
        - weekly
        - size 25M
        - rotate 7
        - missingok
        - compress
        - delaycompress
        - copytruncate
      scripts:
        postrotate: "[ -s /run/nginx.pid ] && kill -USR1 `cat /run/nginx.pid`"
    - name: access_logs
      path: /etc/foo.log
      options:
        - daily
        - size 20M
        - rotate 12
        - missingok
    - name: rails
      path: /users/deploy/apps/rails_app/shared/logs/*.log
      options:
        - hourly
        - size 20M
        - rotate 4
        - missingok
        - compress
        - delaycompress
        - copytruncate
      scripts:
        postrotate: "upload to s3"
  logrotate_run_hourly: true
  restart_command: sudo systemctl restart crond
```

## License

[BSD](https://raw.githubusercontent.com/nickhammond/logrotate/master/LICENSE)

## Author Information

* [nickhammond](https://github.com/nickhammond) | [www](http://www.nickhammond.com) | [twitter](http://twitter.com/nickhammond)
* [bigjust](https://github.com/bigjust)
* [steenzout](https://github.com/steenzout)
* [jeancornic](https://github.com/jeancornic)
* [duhast](https://github.com/duhast)
* [kagux](https://github.com/kagux)
* [oozou](https://oozou.com)
