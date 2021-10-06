# XCompact3d-toolbox: Data for Tutorials

Useful links:

  * XCompact3d: https://github.com/xcompact3d/Incompact3d
  * XCompact3d-toolbox: https://github.com/fschuch/xcompact3d_toolbox
  * Tutorials: https://xcompact3d-toolbox.readthedocs.io/en/latest/tutorial.html

## Available Cases

* 2D Flow around a Cylinder (`cylinder.nc`):

  ```python
  >>> import xcompact3d_toolbox as x3d
  >>> dataset, prm = x3d.tutorial.open_dataset("cylinder")
  >>> print(dataset)
  <xarray.Dataset>
  Dimensions:  (i: 2, t: 201, x: 257, y: 128)
  Coordinates:
    * x        (x) float64 0.0 0.07812 0.1562 0.2344 ... 19.77 19.84 19.92 20.0
    * y        (y) float64 0.0 0.09375 0.1875 0.2812 ... 11.62 11.72 11.81 11.91
    * t        (t) float64 0.0 0.75 1.5 2.25 3.0 ... 147.0 147.8 148.5 149.2 150.0
    * i        (i) object 'x' 'y'
  Data variables:
      u        (i, x, y, t) float32 ...
      pp       (x, y, t) float32 ...
      epsi     (x, y) float32 0.0 0.0 0.0 0.0 0.0 0.0 ... 0.0 0.0 0.0 0.0 0.0 0.0
  Attributes:
      xcompact3d_version:          v3.0-397-gff531df
      xcompact3d_toolbox_version:  1.0.1
      url:                         https://github.com/fschuch/xcompact3d_toolbo...
      dataset_license:             MIT License
  ```

* Poli-dispersed Turbidity Current (coming soon).

## Converting the dataset from a simulation to netCDF

This is an example showing how to create the file(s):

```python
>>> import xcompact3d_toolbox as x3d
>>> # Load the parameters file from the disc
>>> prm = x3d.Parameters(loadfile="input.i3d", raise_warning=True)
>>> # Specify the options for the dataset [optional]
>>> prm.dataset.set(stack_velocity=True)
>>> # Load the entire dataset from the disc
>>> dataset = prm.dataset[:] # may not work for large scale simulations
>>> # Load the geometry at epsilon.bin
>>> dataset["epsi"] = prm.dataset.load_array(
...     "./data/geometry/epsilon.bin", add_time=False
... )
>>> # Specify extra atributes to help reproducibility [optional]
>>> dataset.attrs = dict(
...     xcompact3d_version = "v3.0-397-gff531df",
...     xcompact3d_toolbox_version = x3d.__version__,
...     url = "https://github.com/fschuch/xcompact3d_toolbox_data",
...     dataset_license = "MIT License",
...     prm = str(prm),
... )
>>> # To save space and make it easier to share [optional]:
>>> #     Convert float64 to float32
>>> dataset = dataset.astype(np.float32)
>>> #     Compute span-wise average for all variables
>>> dataset = dataset.mean("z")
>>> #     Drop span-wise velocity
>>> dataset = dataset.drop_sel(i="z")
>>> # Write the dataset to a netcdf file, keeping all data
>>> # together with its dimensions, coordinates and atributes
>>> dataset.to_netcdf("cylinder.nc")
```

## Copyright and License

All content is under [MIT License](https://github.com/fschuch/xcompact3d_toolbox_data/blob/main/LICENSE).