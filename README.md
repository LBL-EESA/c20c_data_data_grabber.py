# Getting Started - C20C+ Data Grabber

Code for pulling C20C+ data from tape at NERSC.

Example usage:
```python
c20c_data_grabber All-Hist run001 LBNL CAM5-1-1degree --variable_list hus,ua,va
```

The above code would extract data from the relevant tar files on HPSS and place
the files in a local directory structure that mirrors that of the C20C archive.
