#samba

```ini
# See smb.conf.example for a more detailed config file or
# read the smb.conf manpage.
# Run 'testparm' to verify the config is correct after
# you modified it.
#
# Note:
# SMB1 is disabled by default. This means clients without support for SMB2 or
# SMB3 are no longer able to connect to smbd (by default).

[global]
        server role = standalone server
        logging = systemd
        loglevel = 1
        passdb backend = tdbsam

[public]
        path = /datastore/public/
        read only = no
        inherit permissions = yes
        valid users = public, fanmi

[fanyang]
        path = /datastore/fanyang/
        read only = no
        inherit permissions = yes
        valid users = fanmi
```

```bash
smbclient -U fanmi \\10.1.2.3\public
```
