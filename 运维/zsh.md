[[HTTP Proxy]]

## oh-my-zsh

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-completions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/agkozak/zsh-z $ZSH_CUSTOM/plugins/zsh-z
git clone https://github.com/joshskidmore/zsh-fzf-history-search ${ZSH_CUSTOM:=~/.oh-my-zsh/custom}/plugins/zsh-fzf-history-search

sed -i 's/ZSH_THEME="robbyrussell"/ZSH_THEME="dpoggi"/g' ~/.zshrc
sed -i 's/# HYPHEN_INSENSITIVE="true"/HYPHEN_INSENSITIVE="true"/g' ~/.zshrc
sed -i 's/plugins=(git)/plugins=(git docker zsh-autosuggestions zsh-completions zsh-syntax-highlighting zsh-z zsh-fzf-history-search)/g' ~/.zshrc

# optional
echo -e "export TERM=xterm\\n" >> ~/.zshrc
echo -e "export ZSH_AUTOSUGGEST_HIGHLIGHT_STYLE='fg=5'\\n" >> ~/.zshrc
echo -e 'export LS_COLORS="$LS_COLORS:ow=1;34:tw=1;34:"' >> ~/.zshrc
echo -e 'precmd () { echo -n "\x1b]1337;CurrentDir=$(pwd)\x07" }' >> ~/.zshrc

source ~/.zshrc

cat > /usr/bin/shopt << EOF
#!/usr/bin/env bash

args='';
for item in $@
  do
    args="$args $item";
  done
shopt $args;
EOF
chmod +x /usr/bin/shopt
```

```
REPO=sjtug/ohmyzsh REMOTE=https://git.sjtu.edu.cn/${REPO}.git sh -c "$(curl -fsSL https://git.sjtu.edu.cn/sjtug/ohmyzsh/-/raw/master/tools/install.sh\?inline\=false)"
```
