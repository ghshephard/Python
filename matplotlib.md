Create much Sharper Graphs:

Place following in ipython_kernel_config.py

```
c.InteractiveShellApp.matplotlib = 'inline'
c.InlineBackend.figure_formats = {'svg'}
```
