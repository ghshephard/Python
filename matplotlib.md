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

Excellent SO post on tweaking legends: https://stackoverflow.com/a/4701285


Three ways to add axes to a figure:

1. ax=fig.add_axes([x1,y1,width,height])
2. ax=fig.add_subplot(rcn)
3. ax,fig=plt.subplots() or ax,fig=plt.subplots(nrows, ncols)

#1 is useful in that it gives you total control over where the axes land.
#2 is nice if you are going to be adding axes and tweaking them one at a time - don't need to index an array.
#3 is probably the best - puts everything in an array if you want to mass change stuff.  Gives you the axes and figure handler in a single command.
