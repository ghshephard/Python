## Some useful Jupyter Tips:

Auto-Reload your changed python modules

    %load_ext autoreload
    %autoreload 2
    
No need to restart the kernel and re-execute all those cells each time you change your .py files you've imported.

----
Get the full use of your widescreen monitor with Jupyter Notebook (automatic with Jupyter Lab)

    from IPython.core.display import display, HTML
    display(HTML("<style>.container { width:95% !important; }</style>"))
    