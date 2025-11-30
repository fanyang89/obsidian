---
title: SHELL 配置
---

# SHELL 配置

#shell

```bash
# PATH
PATH=/usr/local/bin:$PATH
PATH=$HOME/.npm-packages/bin:$PATH
PATH=$HOME/opt/bin:$PATH
PATH=/opt/homebrew/opt/gnu-sed/libexec/gnubin:$HOME/go/bin:$JAVA_HOME/bin:$PATH
PATH=/opt/homebrew/bin/nano:$PATH
PATH=$HOME/.local/bin:$PATH
export PATH=/opt/homebrew/opt/llvm/bin:$PATH

# oh-my-zsh
export ZSH="$HOME/.oh-my-zsh"
ZSH_THEME="crcandy"
HYPHEN_INSENSITIVE="true"
plugins=(brew git docker zsh-z history zsh-completions zsh-syntax-highlighting)
source $ZSH/oh-my-zsh.sh
unalias gg  # hack for gg proxy

# shell
export LANG=en_US.UTF-8
export EDITOR='vim'

# proxy
export http_proxy='http://127.0.0.1:8899'
export https_proxy=$http_proxy

# java
export JAVA_HOME=/System/Volumes/Data/Library/Java/JavaVirtualMachines/zulu-21.jdk/Contents/Home/
# export JAVA_HOME=/System/Volumes/Data/Library/Java/JavaVirtualMachines/zulu-8.jdk/Contents/Home/

# homebrew
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.ustc.edu.cn/brew.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.ustc.edu.cn/homebrew-bottles"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.ustc.edu.cn/homebrew-core.git"

# mutagen
source <(mutagen completion zsh)
compdef _mutagen mutagen

# iterm2
test -e "${HOME}/.iterm2_shell_integration.zsh" && source "${HOME}/.iterm2_shell_integration.zsh"

# thefuck
#eval $(thefuck --alias)

# binenv
#source <(/Users/fanyang/.binenv/binenv completion zsh)

# pyenv
# export PYENV_ROOT="$HOME/.pyenv"
# command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
# eval "$(pyenv init -)"

# cargo
. $HOME/.cargo/env

# atuin
eval "$(atuin init zsh)"

# bat
export BAT_THEME="ansi"

# tiup
export PATH=/Users/fanyang/.tiup/bin:$PATH

# bun
[ -s "/Users/fanyang/.bun/_bun" ] && source "/Users/fanyang/.bun/_bun"
export BUN_INSTALL="$HOME/.bun"
export PATH="$BUN_INSTALL/bin:$PATH"

# jira-cli
export JIRA_API_TOKEN="DFCGVHNETUkvgHVehVbcEtkFJiuBDKvTJnrHbg"
function give_me_jira() {
    jira issue assign $(jira issue list -q 'project = ZBS AND status = Open AND resolution = Unresolved AND text ~auto_bot' --reverse --plain --paginate 0:1 --assignee 'Yutian Zhou' --no-headers | awk '{print $2}') "Yang Fan"
}

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/opt/homebrew/Caskroom/miniconda/base/bin/conda' 'shell.zsh' 'hook' 2>/dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/opt/homebrew/Caskroom/miniconda/base/etc/profile.d/conda.sh" ]; then
        . "/opt/homebrew/Caskroom/miniconda/base/etc/profile.d/conda.sh"
    else
        export PATH="/opt/homebrew/Caskroom/miniconda/base/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<

# Wasmer
export WASMER_DIR="/Users/fanyang/.wasmer"
[ -s "$WASMER_DIR/wasmer.sh" ] && source "$WASMER_DIR/wasmer.sh"

# SDKs
export GOPRIVATE=newgh.smartx.com
export VCPKG_ROOT="$HOME/opt/vcpkg"
export DOTNET_CLI_TELEMETRY_OPTOUT=1
export ANDROID_HOME=$HOME/Library/Android/sdk

# juicefs
export META_PASSWORD='Tw8#7Req2hZ!t4'
export REDIS_PASSWORD='Tw8#7Req2hZ!t4'
export JFS_RSA_PASSPHRASE=BiJdmqw95USiPdRF

alias rsyn='rsync -Pprclz'
alias ckcli='clickhouse client -c ~/.config/clickhouse/clickhouse-client.xml'

function trim_uuid() {
    eval "$(ls | python3 -c 'import sys,os;a=[x.strip() for x in sys.stdin if "meta" not in x];s=os.path.commonprefix([w[::-1] for w in a])[::-1];print("\n".join(["mv {} {}".format(x, x.removesuffix(s)) for x in a]))')"
}

function st() {
    open -a 'Sublime Text' $@
}
```
