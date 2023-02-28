# scidatacontainer

This is a Python 3 implementation of a lean container class for the storage of scientific data, in a way compliant to the [FAIR princples](https://en.wikipedia.org/wiki/FAIR_data) of modern research data management. In a standardized container file it provides maximum flexibility and minimal restrictions. Data containers may be stored as local files and uploaded to a data storage server. The class is operating system independent.

The basic concept of the data container is that it keeps the raw dataset, parameter data and meta data together. Parameter data is every data which was usually be recorded in lab books like test setup, measurement settings, simulation parameters or evaluation parameters. The idea is to make each dataset self-contained. Large amounts of parameter data may be stored in their own container, referenced by its identifier. This is especially useful for static data.

## Structure and Terms

Each data container is identified by a [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier), which is usually generated automatically. The *Container* file is a [ZIP package file](https://en.wikipedia.org/wiki/ZIP_(file_format)). The data in the container is contained in *Items* (file in ZIP package), which are organized in *Parts* (folder in ZIP package). The standard file extension of the container files is `.zdc`. When you execute the file `zdc.reg` on Microsoft Windows, the operating sytem treats this extension in the same way as `.zip`. This allows to inspect the file with a double-click in the Windows Explorer.

There are no restrictions regarding data formats, but items should be represented as Python dictionaries and stored as JSON files in the ZIP package, if possible. This allows to inspect, use and even create data container files with the tools provided by the operating system without any special software. However, this container class makes these tasks much more convenient. Data *Attributes* are keys of JSON mappings.

Just two items `content.json` and `meta.json` are required and must be located in the root part of the container. All data payload and parameter data should be stored in an optional set of suggested parts.

## Container Parameters

The parameters describing the container are stored in the required root item `content.json`. The following set of attributes is currently defined for this item:

- `uuid`: automatic UUID
- `replaces`: optional UUID of the predecessor of this dataset
- `containerType`: required mapping
    + `name`: required
    + `id`: optional
    + `version`: required only if id is given
- `created`: automatic creation timestamp
- `modified`: automatic last modification timestamp
- `static`: required booleanflag (static datasets)
- `complete`: required booleanflag (multi-step datasets)
- `hash`: optional hex digest of SHA256 hash, required for static datasets
- `usedSoftware`: optional list of mappings
    + `name`: required
    + `version`: required
    + `id`: optional
    + `idType`: required if id is given
- `modelVersion`: automatic data model version

## Description of Data

The meta data describing the data payload of the container is stored in the required root item `meta.json`. The following set of attributes is currently defined for this item:

- `author`: required
- `email`: required
- `comment`: optional
- `title`: required
- `keywords`: optional
- `description`: optional

In order to simplify the generation of meta data, the data container class will insert default values for the author name and email address. These default values are either been taken from the environment variables `DC_AUTHOR` and `DC_EMAIL` or fron a configuration file. This configuraton file is `%HOMEDRIVE%%HOMEPATH\scidata.cfg%` on Microsoft Windows and `~/.scidata` on other operating systems. The file is expected to be a text file. Leading and trailing white space is ignored, as well as lines starting with `#`. The parameters are taken from lines in the form `<key> = <value>`, with the keywords `author` and `email`. Optional white space before and after the equal sign is ignored. The keywords are case-insensitive.

## Suggested Parts

Standardization simplifies data exchange as well as reuse of data. Therefore, it is suggested to store the data payload of a container in the following part structure:

- `/info`: informative parameters
- `/sim`: raw simulation results
- `/meas`: raw measurement results
- `/data`: parameters and data required to achieve results in `/sim` or `/meas`
- `/eval`: evaluation results derived from `/sim` and/or `/meas`
- `/log`: log files or other unstructured data


