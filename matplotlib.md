Create much Sharper Graphs:

Place following in ipython_kernel_config.py

```
c.InteractiveShellApp.matplotlib = 'inline'
c.InlineBackend.figure_formats = {'svg'}
```

Set matplotlib figure size to something reasonable for jupyter
(Obv can be overridden with `fig=plt.figure(figsize=(x,y))`

```
plt.rcParams["figure.figsize"] = (10,10)
```
