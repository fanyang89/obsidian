#bash #smtxos

```bash
unset PROMPT_COMMAND
```

```bash
# cat /etc/profile.d/tuna_profile.sh

export PS1='[\u@\h \t \W]$'
export HISTTIMEFORMAT='%Y-%m-%d %H:%M:%S '

export PROMPT_COMMAND='{ date "+[ %Y%m%d %H:%M:%S `who -u am i` ] [`whoami`] `history 1 | { read x cmd; echo "$cmd"; }`"; } >> /var/log/bash.log'


# Avoid duplicates
export HISTCONTROL=ignoredups:erasedups
## When the shell exits, append to the history file instead of overwriting it
shopt -s histappend

## After each command, append to the history file and reread it
export PROMPT_COMMAND="${PROMPT_COMMAND:+$PROMPT_COMMAND$'\n'}history -a; history -c; history -r"

if [ ! -f /var/log/bash.log ];then
    touch /var/log/bash.log
    chmod a+w /var/log/bash.log
    chattr +a /var/log/bash.log
fi
```
