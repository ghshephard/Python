## TPM

TPM is the tmux plugin manager - A handy tool for automomatically 
installing plugins from github, as well as automatically running
them (via the .tmux file located in each plugins directory 
under ~/.tmux/plugins/xxxxxx)

Once installed, you only need to define your @tpm_plugins like:

````
 set -g @tpm_plugins "          \
 tmux-plugins/tpm             \
 tmux-plugins/tmux-sensible   \
 tmux-plugins/tmux-resurrect  \
 tmux-plugins/tmux-continuum \
 tmux-plugins/tmux-logging \
 tmux-plugins/tmux-resurrect-virtualenvwrapper"

````
And then, somwhere near the bottom of our tmux.conf:
````
run '~/.tmux/plugins/tpm/tpm'
````

which will make sure that the plugin manager is started.
Useful or easy Install with `meta-I` or upgrade with `meta-U`

Also has a CLI documented here: https://github.com/tmux-plugins/tpm/blob/master/docs/managing_plugins_via_cmd_line.md

