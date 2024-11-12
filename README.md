# TNA-TSA-Anomaly-2024 
Monitoring Sea Surface Temperature (SST) in the Atlantic is vital for predicting Brazil's rainfall, as it affects the Intertropical Convergence Zone (ITCZ) and moisture distribution. SST anomalies influence precipitation patterns, making their tracking essential for accurate climate forecasts.

This Python code analyzes sea surface temperature (SST) anomaly data from a NetCDF file provided by NOAA.  Here's a breakdown:

**1. Data Acquisition:**

- The code first defines a function `download_data` to download the NetCDF file from a specified URL if it doesn't already exist in the `/content/` directory of the Google Colab environment.
- It uses `wget` for downloading.

**2. Data Loading and Preprocessing:**

- The `load_netcdf_data` function reads the NetCDF file using the `netCDF4` library.
- It extracts SST anomalies, latitudes, longitudes, and time data.  Crucially, it converts the time variable from numerical values to datetime objects using `nc.num2date`.
- It defines latitude and longitude ranges for two regions of interest: TNA (Tropical North Atlantic) and TSA (Tropical South Atlantic).

**3. Regional Mean SST Calculation:**

- The `mean_sst_in_region` function calculates the mean SST anomaly within a specified region (defined by latitude and longitude ranges).  It applies masks to the latitude and longitude arrays to select the relevant data.

**4. Time Series Plotting:**

- The `plot_sst_difference` function generates plots showing the difference in mean SST anomalies between TNA and TSA.
- Two plots are generated: One for the last 30 days and another for the entire year present in the data.
- The plots show the difference in anomalies, as well as individual anomaly time series for TNA and TSA.
- The x-axis is formatted to show dates clearly.  The plots include a horizontal line at 0 for reference, a legend, gridlines, and source/climatology information.

**5. Anomaly Variation Analysis:**

- The code then analyzes the SST anomaly *variation* over a period of 7 days. It calculates the difference between the SST anomaly on the last day of the data and the SST anomaly 7 days prior.
- `mean_variation_in_region` calculates the mean variation for TNA and TSA regions.

**6. Map Plotting:**

- The `plot_anomaly_variation` function creates a map visualizing the 7-day SST anomaly variation.
- Uses `cartopy` to create a map with coastlines and oceans.
- A colormap (blue to white to red) represents the magnitude and direction of the anomaly variation.
- The TNA and TSA regions are highlighted with colored boxes. The average variations in these regions are shown with text labels on the plot.
- Source and reference climatology are added.

**7. Main Execution Block:**

- The `if os.path.exists(file_path):` block checks if the downloaded file exists.
- The core analysis (loading data, calculating anomalies, creating plots) happens inside this block.
- Error handling is in place to print an error message if the file does not exist.



**In summary:** The code downloads NOAA SST anomaly data, calculates regional mean anomalies and their variations over time, and generates plots to visualize these trends. The visualizations include both time series and spatial maps, making the data analysis more comprehensive.
