# ACN-Data Static

This repository provides a static, file-based snapshot of
[ACN-Data](https://ev.caltech.edu/dataset), an open electric vehicle charging
dataset collected from sites at Caltech, JPL, and nearby office facilities.

The data in this snapshot covers charging activity from 2018 through 2020 and
is organized for direct download, archival use, and reproducible offline
analysis.

## Contents

- 85,877 compressed charging-session time-series files in `.csv.gz` format.
- Site folders for Caltech, JPL, and office charging locations.
- A `session data/` directory reserved for session-level exports.

Current time-series coverage:

| Site group | Location | Files |
| --- | --- | ---: |
| `office_01` | `Parking_Lot_01` | 1,474 |
| `caltech` | `California_Garage_01` | 31,860 |
| `caltech` | `S_Wilson_Garage_01` | 2,703 |
| `caltech` | `California_Garage_02` | 15,589 |
| `caltech` | `LIGO_01` | 229 |
| `caltech` | `N_Wilson_Garage_01` | 6,299 |
| `jpl` | `Arroyo_Garage_01` | 27,723 |

## Repository Layout

```text
ACN-Data-Static/
|-- README.md
|-- session data/
|   `-- caltech_sessions.json
`-- time series data/
    |-- caltech/
    |   |-- California_Garage_01/
    |   |-- California_Garage_02/
    |   |-- LIGO_01/
    |   |-- N_Wilson_Garage_01/
    |   `-- S_Wilson_Garage_01/
    |-- jpl/
    |   `-- Arroyo_Garage_01/
    `-- office_01/
        `-- Parking_Lot_01/
```

Each file under `time series data/` contains the time-series measurements for a
single charging session. File names include ACN identifiers and a session start
timestamp.

The `session data/caltech_sessions.json` path is included for session-level
exports. In the current checkout, the file is empty; use the time-series
directories for the complete static dataset.

## Data Format

The time-series files are gzip-compressed CSV files. The first column is an ISO
8601 timestamp, and the remaining columns report charging measurements:

| Column | Description |
| --- | --- |
| `Charging Current (A)` | Measured charging current in amps. |
| `Actual Pilot (A)` | Pilot signal current limit in amps. |
| `Voltage (V)` | Measured voltage in volts. |
| `Charging State` | Charging state label reported by the system. |
| `Energy Delivered (kWh)` | Cumulative energy delivered during the session. |
| `Power (kW)` | Instantaneous charging power in kilowatts. |

Example:

```bash
gzip -cd "time series data/office_01/Parking_Lot_01/19-102-260-1635-2019-10-31T14-21-10-330888.csv.gz" | head
```

## Loading the Data

The files can be read directly with standard data tools. For example, with
Python and pandas:

```python
from pathlib import Path

import pandas as pd

repo = Path("ACN-Data-Static")
path = repo / "time series data" / "office_01" / "Parking_Lot_01"
file = next(path.glob("*.csv.gz"))

df = pd.read_csv(file, compression="gzip", index_col=0, parse_dates=True)
df.index.name = "timestamp"

print(df.head())
```

Because this repository is data-only, it does not require a package
installation step. Downstream analysis code should pin its own dependencies for
reproducibility.

## Related Resources

- Official ACN-Data site: <https://ev.caltech.edu/dataset>
- GMM models for ACN-Data: <https://github.com/tongxin-li/GMM-for-ACN-Data>
- EV charging gym environment: <https://github.com/tongxin-li/gym-EV>

## Changelog

- 2024-07-05: Added the `session data/caltech_sessions.json` export path for
  Caltech session data.

## Citation

If you use ACN-Data in published work, please cite the original dataset paper so
others can find and use the dataset:

```bibtex
@inproceedings{lee_acndata_2019,
  author = {Lee, Zachary J. and Li, Tongxin and Low, Steven H.},
  title = {{ACN-Data}: Analysis and Applications of an Open EV Charging Dataset},
  booktitle = {Proceedings of the Tenth ACM International Conference on Future Energy Systems},
  series = {e-Energy '19},
  year = {2019},
  month = jun,
  location = {Phoenix, Arizona}
}
```

## License and Terms

This repository does not currently include a standalone license file. Please
refer to the terms on the official ACN-Data site before redistribution, and cite
the dataset when using it in research or published analysis.
