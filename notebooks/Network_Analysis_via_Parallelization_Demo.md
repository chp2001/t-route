# NHD Network Analysis Demo

**Attempting to interpret the `Network_Analysis_via_Parallelization_Demo.ipynb` notebook as a markdown file to explain the process.**

## Steps

### 1. Set up the imports

Case 1: google.colab is available (Not Applicable, would mean running on Google Colab)

Case 2: google.colab is not available (Applicable, running on local machine)

Add `src/python_framework_v01` to the system path

```python
import sys
sys.path.append(r'../src/python_framework_v01')
```

### 2. Import Network Utilities

```python
import nhd_network_utilities_v01 as nnu
import recursive_print
```

The included test case for this cell does the following:

1. Initialize test parameters:

    ```python
    test_rows:list[list[int, int, int, int]] = [
        [50, 178, 51, 0],
        [51, 178, 50, 0],
        ...
    ] # about 40 rows
    test_key_col:int = 0
    test_downstream_col:int = 2
    test_length_col:int = 1
    test_terminal_code:int = -999
    debuglevel:int = 0
    verbose:bool = True
    ```

2. Build the connections object:

    ```python
    # geo_file_rows, mask_set, key_col, downstream_col, length_col, terminal_code, waterbody_col, waterbody_null_code, verbose, debuglevel
    def nnu.build_connections(
        geo_file_rows: List[List[int, int, int, int]]|None = None,
        mask_set: Set[int] | None = None,
        key_col: int | None = None,
        downstream_col: int | None = None,
        length_col: int | None = None,
        terminal_code: int | None = None,
        waterbody_col: int | None = None,
        waterbody_null_code: int | None = None,
        verbose: bool = False,
        debuglevel: int = 0
    ) -> Tuple[Any*17]:
    # return (
    #    connections, # 0
    #    all_keys, # 1
    #    ref_keys, # 2
    #    headwater_keys, # 3
    #    terminal_keys, # 4
    #    terminal_ref_keys, # 5
    #    circular_keys, # 6
    #    junction_keys, # 7
    #    visited_keys, # 8
    #    visited_terminal_keys, # 9
    #    junction_count, # 10
    #    confluence_segment_set, # 11
    #    waterbody_dict, # 12
    #    waterbody_segments, # 13
    #    waterbody_outlet_set, # 14
    #    waterbody_upstreams_set, # 15
    #    waterbody_downstream_set, # 16
    #)
    ```

    ```python
    (
        connections,
        all_keys,
        ref_keys,
        headwater_keys,
        terminal_keys,
        terminal_ref_keys,
        circular_keys,
        junction_keys,
        visited_keys,
        visited_terminal_keys,
        junction_count,
        confluence_segment_set,
        waterbody_dict,
        waterbody_segments,
        waterbody_outlet_set,
        waterbody_upstreams_set,
        waterbody_downstream_set,
    ) = nnu.build_connections(
        geo_file_rows=test_rows, # list[list[int, int, int, int]]
        key_col=test_key_col, # 0
        mask_set={row[test_key_col] for row in test_rows}, # {50, 51, ...}
        downstream_col=test_downstream_col, # 2
        length_col=test_length_col, # 1
        terminal_code=test_terminal_code, # -999
        verbose=verbose, # True
        debuglevel=debuglevel, # 0
    )
    ```

3. Print the connections object:

    ```python
    recursive_print.print_connections(
        headwater_keys=headwater_keys,
        down_connections=connections,
        up_connections=connections,
        terminal_code=test_terminal_code,
        terminal_keys=terminal_keys,
        terminal_ref_keys=terminal_ref_keys,
        debuglevel=debuglevel,
    )
    ```

4. Print the basic network info:

    ```python
    recursive_print.print_basic_network_info(
        connections=connections,
        headwater_keys=headwater_keys,
        junction_keys=junction_keys,
        terminal_keys=terminal_keys,
        terminal_code=test_terminal_code,
        verbose=verbose,
        debuglevel=debuglevel,
    )
    ```

### 3. `Real Networks`

This step imports network data for the `Brazos_LowerColorado_ge5`, `CONUS_ge5`, `CONUS_FULL_RES_v20`, and `CONUS_Named_Streams` datasets.

First, the `supernetworks` dictionary is initialized:

```python
supernetworks = {key: {"data": None, "values": None} for key in ["Brazos_LowerColorado_ge5", "CONUS_ge5", "CONUS_FULL_RES_v20", "CONUS_Named_Streams"]}
```

Then, the `supernetworks` dictionary is populated with the network data:

```python
for sn in supernetworks:
    print(sn)
    supernetworks[sn]["data"], supernetworks[sn]["values"] = nnu.set_networks(
        supernetwork=sn,
        geo_input_folder=geo_input_folder,
        verbose=verbose,
        debuglevel=debuglevel,
    )
```

At this point, it downloads the `CONUS_ge5` dataset, then crashes when trying to load it.
