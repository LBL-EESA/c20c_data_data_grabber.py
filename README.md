# Getting Started - C20C+ Data Grabber

Code for pulling C20C+ data from tape at NERSC.

Example usage:
```bash
c20c_data_grabber All-Hist run001 LBNL CAM5-1-1degree --variable_list hus,ua,va
```

The above command would extract data from the relevant tar files on HPSS and
place the files in a local directory structure that mirrors that of the C20C
archive.

This can also be imported and used in other python code:

```python
import c20c_data_grabber

experiments = ["All-Hist", "Nat-Hist"]

for experiment in experiments:
    for run in range(1,51):
        c20c_data_grabber.extract_c20c_data(
                experiment = experiment,
                run = f"run{run:03}",
                variable_list = ["hus","ua","va"],
                institution = "LBNL",
                model = "CAM5-1-1degree"
                )
```

The above code would download ensemble members 1-51 for the All-Hist and
Nat-Hist experiments.
