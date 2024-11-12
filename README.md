# TNA-TSA-Anomaly-2024 

Monitoring Sea Surface Temperature (SST) anomalies in the Atlantic is crucial for predicting rainfall in Brazil, given its influence on the Intertropical Convergence Zone (ITCZ) and moisture distribution. SST anomalies play a significant role in shaping precipitation patterns, making their tracking essential for accurate climate forecasts.

The following Python code analyzes SST anomaly data obtained from a NetCDF file provided by NOAA. Below is a structured breakdown of the key components of the code:

## **1. Data Acquisition**

The NOAA provides daily SST anomalies accessible at: [NOAA OISST](https://psl.noaa.gov/data/gridded/data.noaa.oisst.v2.highres.html).

### Key Information:
- **Climatology**: Anomalies are calculated relative to a climatological baseline from 1971 to 2000.
- **Temporal Coverage**:
  - Daily values: September 1981 - September 2024
  - Weekly values: September 1981 - September 2024
  - Monthly values: September 1981 - September 2024
  - Long-term monthly average (1991-2020)
- **Spatial Coverage**: Covers a global grid with 0.25 degrees latitude x 0.25 degrees longitude (1440 x 720 points).
- **Limits**: Ranges from 89.875°S to 89.875°N, and 0.125°E to 359.875°E.
- **Update Frequency**: Daily
- **File Format and Size**: Compliant with CF metadata standard in NetCDF4 format.

### Data Download Function:
- The `download_data` function uses `wget` to download the NetCDF file from a specified URL if it’s not already present in the `/content/` directory of the Google Colab environment.

## **2. Data Loading and Preprocessing**

- The `load_netcdf_data` function reads the NetCDF file utilizing the `netCDF4` library.
- It extracts valuable attributes including:
  - SST anomalies,
  - Latitude and longitude data,
  - Time data, converting numerical values to datetime objects using `nc.num2date`.
- Latitude and longitude ranges are defined for two focal regions: TNA (Tropical North Atlantic) and TSA (Tropical South Atlantic).

## **3. Regional Mean SST Calculation**

### TNA and TSA Definitions:
- **TNA** (Tropical North Atlantic): SST anomalies are calculated within the boundary 55°W - 15°W and 5°N - 25°N. For specifics, check [NOAA TNA Info](https://stateoftheocean.osmc.noaa.gov/sur/atl/tna.php).
  
- **TSA** (Tropical South Atlantic): SST anomalies pertain to the Gulf of Guinea in the eastern South Tropical Atlantic, defined by 30°W - 10°E and 20°S - 0°. More details available at [NOAA TSA Info](https://stateoftheocean.osmc.noaa.gov/sur/atl/tsa.php).

### Mean SST Calculation:
- The `mean_sst_in_region` function computes the mean SST anomaly for the designated regions by applying masks based on latitude and longitude.

## **4. Time Series Plotting**

- The `plot_sst_difference` function visualizes the difference in mean SST anomalies between TNA and TSA.
- It generates two plots:
  - One for the last 30 days,
  - Another covering the entire year available in the dataset.
- The plots feature:
  - Anomalies with respect to time,
  - A reference line at zero for context,
  - Legends and gridlines for clarity,
  - Source and climatology information.

## **5. Anomaly Variation Analysis**

- The code analyzes SST anomaly *variation* over a 7-day period, measuring the change between the SST anomaly on the final day of the data against the anomaly from 7 days prior.
- The mean variation for TNA and TSA is computed using the `mean_variation_in_region`.

## **6. Map Plotting**

- The `plot_anomaly_variation` function employs `cartopy` for mapping the 7-day SST anomaly variation.
- Features include:
  - Coastline and ocean delineation,
  - A colormap representing the magnitude and direction of the anomaly variation,
  - Highlighted boxes for TNA and TSA regions with text labels for average values,
  - Source and climatology references displayed.

## **7. Main Execution Block**

- The block wraps the main execution logic, verifying the existence of the downloaded file with `if os.path.exists(file_path):`.
- It encompasses the core analysis steps (data loading, mean calculation, plotting) and includes error handling to output a message if the file is missing.

---

## **Summary**
This code enables users to download, analyze, and visualize NOAA SST anomaly data effectively. 
