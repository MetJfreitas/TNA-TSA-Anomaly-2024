#Install libraries

#!pip install netCDF4
#!pip install numpy
#!pip install matplotlib
#!apt-get install libgeos-dev -qq
#!pip install basemap-data-hires -qq
#!pip install basemap -qq
#!pip install cartopy

# Import necessary libraries
import os
import netCDF4 as nc
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.dates as mdates
from datetime import timedelta
import cartopy.crs as ccrs
import cartopy.feature
from matplotlib.colors import Normalize, LinearSegmentedColormap

# Function to download the file
def download_data(url, file_path):
    """Downloads a file if it doesn't already exist."""
    if not os.path.exists(file_path):
        os.system(f"wget {url}")
    else:
        print("File already exists, using existing file.")

# File path and download URL
file_path = "/content/sst.day.anom.2024.nc"
url = "https://downloads.psl.noaa.gov//Datasets/noaa.oisst.v2.highres/sst.day.anom.2024.nc"

# Download the file
download_data(url, file_path)

# Function to load data from the NetCDF file
def load_netcdf_data(file_path):
    """Loads sea surface temperature anomaly data from the NetCDF file."""
    dataset = nc.Dataset(file_path)
    sst_anomaly = dataset.variables['anom'][:]
    latitudes = dataset.variables['lat'][:]
    longitudes = dataset.variables['lon'][:]
    time = dataset.variables['time'][:]
    time_reference = nc.num2date(time, units=dataset.variables['time'].units)
    return dataset, sst_anomaly, latitudes, longitudes, time_reference

# Defining the limits of the areas for TNA and TSA
tna_lat_range = (5, 25)       # Latitudes for TNA
tna_lon_range = (305, 345)    # Longitudes for TNA (adjusted from 0 to 360)
tsa_lat_range = (-20, 0)      # Latitudes for TSA
tsa_lon_range = (330, 350)    # Longitudes for TSA (adjusted from 0 to 360)

# Function to calculate the mean temperature anomaly in a defined region
def mean_sst_in_region(sst_anomaly, latitudes, longitudes, lat_range, lon_range):
    """Calculates the mean temperature anomaly within a defined region."""
    lat_mask = (latitudes >= lat_range[0]) & (latitudes <= lat_range[1])
    lon_mask = (longitudes >= lon_range[0]) & (longitudes <= lon_range[1])
    lat_indices = np.where(lat_mask)[0]
    lon_indices = np.where(lon_mask)[0]

    # Extract the anomaly of the defined region
    sst_region = sst_anomaly[:, lat_indices.min():lat_indices.max()+1, lon_indices.min():lon_indices.max()+1]
    sst_mean = np.mean(sst_region, axis=(1, 2))
    return sst_mean

# Function to plot the difference of temperature anomalies
def plot_sst_difference(dates, diff_tna_tsa, tna_sst_mean, tsa_sst_mean, title, start_date):
    """Plots the difference of temperature anomalies between TNA and TSA."""
    plt.figure(figsize=(12, 6))
    ax = plt.gca()
    plt.plot(dates, diff_tna_tsa, label='Difference TNA - TSA', color='black')
    plt.axhline(0, color='red', linestyle='--', linewidth=0.8)
    plt.plot(dates, tna_sst_mean, label='TNA Anomaly', color='green')
    plt.plot(dates, tsa_sst_mean, label='TSA Anomaly', color='orange')

    plt.title(title)
    plt.ylabel('Temperature Anomaly Difference (°C)')
    plt.legend()
    plt.grid()

    # Configure the dates on the x-axis
    plt.gca().xaxis.set_major_formatter(mdates.DateFormatter('%d-%m'))
    plt.gca().xaxis.set_major_locator(mdates.DayLocator())
    plt.xlim(start_date, dates[-1])

    plt.gcf().autofmt_xdate()
    
    # Add source and reference climatology
    ax.text(0.05, -0.17, 'Reference Climatology: 1971-2000', fontsize=10, ha='left', transform=ax.transAxes)
    ax.text(0.05, -0.20, 'Source: https://psl.noaa.gov/data/gridded/data.noaa.oisst.v2.highres.html', fontsize=10, ha='left', transform=ax.transAxes)

    plt.tight_layout()
    plt.show()

# Load data from the file
dataset, sst_anomaly, latitudes, longitudes, time_reference = load_netcdf_data(file_path)

# Calculate the mean SST for TNA and TSA
tna_sst_mean = mean_sst_in_region(sst_anomaly, latitudes, longitudes, tna_lat_range, tna_lon_range)
tsa_sst_mean = mean_sst_in_region(sst_anomaly, latitudes, longitudes, tsa_lat_range, tsa_lon_range)

# Calculate the differences in anomalies for the last 30 days
num_days = 30
end_date = time_reference[-1]
start_date = end_date - timedelta(days=num_days)
mask_recent = (time_reference >= start_date) & (time_reference <= end_date)

dates_recent = time_reference[mask_recent]
tna_sst_mean_recent = tna_sst_mean[mask_recent]
tsa_sst_mean_recent = tsa_sst_mean[mask_recent]
diff_tna_tsa_recent = tna_sst_mean_recent - tsa_sst_mean_recent

# Convert the dates to a format suitable for matplotlib
dates_recent_mdates = mdates.date2num(dates_recent)

# Plot the difference of SST for the last 30 days
plot_sst_difference(dates_recent_mdates, diff_tna_tsa_recent, tna_sst_mean_recent, tsa_sst_mean_recent, 
                    'Daily Temperature Anomaly Difference between TNA and TSA (Last 30 days)', dates_recent_mdates[0])

# Yearly analysis
start_of_year = time_reference[0]
mask_year = (time_reference >= start_of_year) & (time_reference <= end_date)
dates_year = time_reference[mask_year]
tna_sst_mean_year = tna_sst_mean[mask_year]
tsa_sst_mean_year = tsa_sst_mean[mask_year]

# Difference of anomalies for the year
diff_tna_tsa_year = tna_sst_mean_year - tsa_sst_mean_year
dates_year_mdates = mdates.date2num(dates_year)

# Configure and plot the variation of temperature anomalies over the year
plt.figure(figsize=(12, 6))
ax = plt.gca()
plt.plot(dates_year_mdates, diff_tna_tsa_year, label='Difference TNA - TSA', color='black')
plt.axhline(0, color='red', linestyle='--', linewidth=0.8)
plt.plot(dates_year_mdates, tna_sst_mean_year, label='TNA Anomaly', color='green')
plt.plot(dates_year_mdates, tsa_sst_mean_year, label='TSA Anomaly', color='orange')

plt.title('Daily Temperature Anomaly Difference between TNA and TSA in 2024')
plt.ylabel('Temperature Anomaly Difference (°C)')
plt.legend()
plt.grid()

# Configure the format of the dates on the x-axis
months_locator = mdates.MonthLocator()
months_formatter = mdates.DateFormatter('%b')
ax.xaxis.set_major_locator(months_locator)
ax.xaxis.set_major_formatter(months_formatter)
plt.gcf().autofmt_xdate()

plt.tight_layout()
plt.show()

# Function to calculate the mean variation in a region
def mean_variation_in_region(variation, latitudes, longitudes, lat_range, lon_range):
    """Calculates the mean anomaly variation in a region."""
    lat_mask = (latitudes >= lat_range[0]) & (latitudes <= lat_range[1])
    lon_mask = (longitudes >= lon_range[0]) & (longitudes <= lon_range[1])
    lat_indices = np.where(lat_mask)[0]
    lon_indices = np.where(lon_mask)[0]

    if len(lat_indices) == 0 or len(lon_indices) == 0:
        print("Error: No data found in the specified region.")
        return np.nan

    variation_region = variation[lat_indices.min():lat_indices.max()+1, lon_indices.min():lon_indices.max()+1]
    return np.nanmean(variation_region)

# Function to plot anomaly variation on a map
def plot_anomaly_variation(lon, lat, anomaly_variation, date_str_last, date_str_minus_7, avg_variation_tna, avg_variation_tsa):
    """Plots the variation of temperature anomalies on a map."""
    fig, ax = plt.subplots(figsize=(14, 8), subplot_kw={'projection': ccrs.PlateCarree()})
    ax.set_extent([-90, 10, -60, 60], crs=ccrs.PlateCarree())
    ax.add_feature(cartopy.feature.LAND, facecolor='lightgray', edgecolor='black', linewidth=1.0)
    ax.add_feature(cartopy.feature.OCEAN, zorder=0)

    c = ax.pcolormesh(lon, lat, anomaly_variation,
                       cmap=LinearSegmentedColormap.from_list("blue_white_red", [(0.0, 0.0, 1.0), (1.0, 1.0, 1.0), (1.0, 0.0, 0.0)]),
                       shading='auto', norm=Normalize(vmin=-5, vmax=5))
    plt.colorbar(c, ax=ax, orientation='vertical', label='Anomaly Variation (°C)', pad=0.02)

    # Adding boxes to highlight TNA and TSA
    tna_box = plt.Rectangle((tna_lon_range[0]-360, tna_lat_range[0]), tna_lon_range[1]-tna_lon_range[0], 
                            tna_lat_range[1]-tna_lat_range[0], linewidth=2, edgecolor='green', facecolor='none', linestyle='--')
    tsa_box = plt.Rectangle((tsa_lon_range[0]-360, tsa_lat_range[0]), tsa_lon_range[1]-tsa_lon_range[0], 
                            tsa_lat_range[1]-tsa_lat_range[0], linewidth=2, edgecolor='orange', facecolor='none', linestyle='--')

    ax.add_patch(tna_box)
    ax.add_patch(tsa_box)

    # Label the areas
    ax.text(tna_lon_range[0]-360 + (tna_lon_range[1]-tna_lon_range[0])/2, tna_lat_range[1], 'TNA',
            color='green', fontsize=8, ha='center', va='bottom', fontweight='bold')
    ax.text(tsa_lon_range[0]-360 + (tsa_lon_range[1]-tsa_lon_range[0])/2, tsa_lat_range[1], 'TSA',
            color='orange', fontsize=8, ha='center', va='bottom', fontweight='bold')

    ax.plot([-180, 180], [0, 0], color='black', linewidth=1.0, linestyle='--')

    # Setting the axis labels
    ax.set_xticks(np.arange(-90, 11, 10), crs=ccrs.PlateCarree())
    ax.set_yticks(np.arange(-60, 61, 15), crs=ccrs.PlateCarree())
    ax.xaxis.set_major_formatter(plt.FuncFormatter(lambda x, _: f'{int(x)}°'))
    ax.yaxis.set_major_formatter(plt.FuncFormatter(lambda y, _: f'{int(y)}°'))

    # Determining the color based on the means
    color_tna = 'red' if avg_variation_tna > 0 else 'blue'
    color_tsa = 'red' if avg_variation_tsa > 0 else 'blue'

    # Adding mean variation texts
    ax.text(0.05, -0.10, f'TNA Variation: {avg_variation_tna:.2f} °C', fontsize=12, ha='left', color=color_tna, transform=ax.transAxes)
    ax.text(0.05, -0.13, f'TSA Variation: {avg_variation_tsa:.2f} °C', fontsize=12, ha='left', color=color_tsa, transform=ax.transAxes)

    # Adding source and reference climatology
    ax.text(0.05, -0.17, 'Reference Climatology: 1971-2000', fontsize=10, ha='left', transform=ax.transAxes)
    ax.text(0.05, -0.20, 'Source: https://psl.noaa.gov/data/gridded/data.noaa.oisst.v2.highres.html', fontsize=10, ha='left', transform=ax.transAxes)

    ax.set_title(f'SST Variation between the days: {date_str_minus_7} and {date_str_last}', fontsize=14)

    # Adjust layout
    plt.subplots_adjust(bottom=0.15)
    plt.show()

# Main Analysis
if os.path.exists(file_path):
    # Load data
    dataset, sst_anomaly, latitudes, longitudes, time_reference = load_netcdf_data(file_path)

    # Analysis for the last 7 days
    num_days = 7
    end_date = time_reference[-1]
    start_date = end_date - timedelta(days=num_days)
    mask_recent_7 = (time_reference >= start_date) & (time_reference <= end_date)

    # Filtering data for the last 7 days
    sst_anomaly_recent_7 = sst_anomaly[mask_recent_7, :, :]
    last_time_index = -1
    day_minus_7_index = last_time_index - 7

    last_anom = sst_anomaly[last_time_index, :, :]
    day_minus_7_anom = sst_anomaly[day_minus_7_index, :, :]
    anomaly_variation = last_anom - day_minus_7_anom

    # Calculate mean variation in the TNA and TSA regions
    avg_variation_tna = mean_variation_in_region(anomaly_variation, latitudes, longitudes, tna_lat_range, tna_lon_range)
    avg_variation_tsa = mean_variation_in_region(anomaly_variation, latitudes, longitudes, tsa_lat_range, tsa_lon_range)

    lon, lat = np.meshgrid(longitudes, latitudes)

    # Extracting dates in formatted string
    date_str_last = time_reference[last_time_index].strftime('%d-%m')
    date_str_minus_7 = time_reference[day_minus_7_index].strftime('%d-%m')

    # Plot the anomaly variation
    plot_anomaly_variation(lon, lat, anomaly_variation, date_str_last, date_str_minus_7, avg_variation_tna, avg_variation_tsa)

    dataset.close()
else:
    print("Error: The specified file does not exist.")
