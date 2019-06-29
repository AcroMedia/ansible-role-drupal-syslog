# Ansible Role: acromedia.drupal-syslog

When using the Drupal Syslog module, redirect logging to a specific file, instead of having drupal write to the system's generic /var/log/(messages|syslog), which can become swamped very quickly by Drupal's watchdog activity.


## Requirements / Limitations

- Ubuntu >= 16.04 or CentOS >= 6
- Ansible >= 2.5 on your workstation
- Rsyslog installed on your server (it's usually there by default on most Ubuntu and Red Hat / CentOS boxes)
- You must `gather_facts: true` in your playbook
- The values for `syslog_identity` and `syslog_facility` in your drupal site's syslog settings must match what you've configured for this role
- The value for `drupal_syslog_identity` cannot exceed 31 characters


## Role Variables

* **drupal_log_path** - no default
  - Where do you want rsyslog to write log entries to.

* **drupal_syslog_facility** - defaults to `local0`
  - You'll only need to change this if something else is already occupying local0.

* **drupal_syslog_conf_path**
  - Defaults to /etc/rsyslog.d/45-drupal.conf
  - This is how we tell syslog to write somehwere else other than the general log file.
  - The "45" in the file name ensures the conf file is processed before the generic "50-default.conf" file. If we don't put ours first, we'll still end up writing to the generic file, which defeats the whole purpose of this exercise.

* **drupal_syslog_logrotate_conf_path**
  - Defaults to /etc/logrotate.d/drupal-syslog
  - Your log file will fill up the server's disk if you don't keep it trimmed. The role creates a logrotate entry to ensure this doesn't happen.

* **drupal_syslog_read_users** / **drupal_syslog_read_groups**
  - defaults to empty list (`[]`)
  - By default, the log file this role creates is only readable by administrators. Chances are, the site owner is also going to want to be able to access their own drupal log. Specfiy any other linux users or groups that will need read access to the log file.

* **drupal_log_keep_for_x_days**
  - Defaults to 7
  - How many days do you want to keep your drupal logs for?

## Dependencies

None


## Example Playbook

```yaml
hosts: app-nodes
gather_facts: true
become: true
roles:
  - name: Send drupal watchdog to its own file instead of writing to syslog
    role: acromedia.drupal-syslog
    vars:
      drupal_log_path: /var/log/vhosts/account/project/drupal.log
      drupal_syslog_read_users:
        - johndoe
```


## License

GPLv3


## Author Information

[Acro Media Inc.](https://www.acromedia.com/)
