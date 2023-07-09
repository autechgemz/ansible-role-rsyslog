# Ansible Role: rsyslog

## Description

Manage system message logging service.

## Requirements

None

## Dependencies

None

## OS Platforms

- RHEL-7/CentOS-7 or higher
- Ubuntu-20.04 or higher

## Example Playbook

```
- hosts: all
  roles:
    - rsyslog
```

## Role Variables

### rsyslog_package_ensure: (string)

```
rsyslog_package_ensure: 'present'
```

### rsyslog_service_ensure: (string)

```
rsyslog_service_ensure: 'started'
```

### rsyslog_service_enable: (bool)

```
rsyslog_service_enable: true
```

### rsyslog_daemon_config_options: (list)

```
rsyslog_daemon_config_options: []
```

### rsyslog_global_config_options: (list)

```
rsyslog_global_config_options: []
```

### rsyslog_dropin_config_options: (list)

```
rsyslog_dropin_config_options: []
```

## Example vars

### RedHat

```
rsyslog_global_config_options:
  - 'SYSLOGD_OPTIONS=""'

rsyslog_dropin_config_options:
  - '$SystemLogSocketName /run/systemd/journal/syslog'
```

### Ubuntu

```
rsyslog_global_config_options:
  - 'module(load="imuxsock")'
  - 'module(load="imklog" permitnonkernelfacility="on")'
  - '$ActionFileDefaultTemplate RSYSLOG_TraditionalFileFormat'
  - '$RepeatedMsgReduction on'
  - '$FileOwner syslog'
  - '$FileGroup adm'
  - '$FileCreateMode 0640'
  - '$DirCreateMode 0755'
  - '$Umask 0022'
  - '$PrivDropToUser syslog'
  - '$PrivDropToGroup syslog'
  - '$WorkDirectory /var/spool/rsyslog'
  - '$IncludeConfig /etc/rsyslog.d/*.conf'

rsyslog_dropin_config_options:
  - name: '20-ufw.conf'
    state: present
    content:
      - ':msg,contains,"[UFW " /var/log/ufw.log'
      - '& stop'
  - name: '21-cloudinit.conf'
    state: present
    content:
      - ':syslogtag, isequal, "[CLOUDINIT]" /var/log/cloud-init.log'
      - '& stop'
  - name: '50-default.conf'
    state: present
    content:
      - 'auth,authpriv.*			/var/log/auth.log'
      - '*.*;auth,authpriv.none		-/var/log/syslog'
      - 'kern.*				-/var/log/kern.log'
      - 'mail.*				-/var/log/mail.log'
      - 'mail.err			/var/log/mail.err'
      - '*.emerg				:omusrmsg:*'
```
