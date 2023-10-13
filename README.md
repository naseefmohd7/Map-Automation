# Map-Automation
#This is a set of codes to prepare multiple maps from a shapefile. The shapefile contain 35 polygons and for each polygon, map created using simple coding.
#this code can be used in Visual Studio, JupyterNotebook etc.


import geopandas as gpd
import matplotlib.pyplot as plt
import os
import re

# file paths
shapefile_path = r"D:\temp\temp10\India.shp"
output_directory = r"D:\temp\temp10\output_maps"

gdf = gpd.read_file(shapefile_path)
print(gdf.columns)

if not os.path.exists(output_directory):
    os.makedirs(output_directory)

# Loop 
for i in range(2, 35):
    selected_identifier = i
    selected_polygon = gdf[gdf['ID1'] == selected_identifier]

    # 'NAME_1' attribute value
    heading = selected_polygon['NAME_1'].values[0]

    heading = re.sub(r'[\\/*?:"<>|]', '', heading)

    # A4 portrait dimensions
    fig, ax = plt.subplots(figsize=(8.27, 11.69))  # A4 size in inches (portrait)

    selected_polygon.boundary.plot(ax=ax, color=None, edgecolor='BLUE')
    ax.set_ylim(selected_polygon.total_bounds[1] - 0.1, selected_polygon.total_bounds[3] + 0.2)  # Adjust the y-axis limits

    # Add title text
    plt.text(0.5, .98, heading, horizontalalignment='center', verticalalignment='top', transform=ax.transAxes, fontsize=12, fontweight='bold')

    # Shape_Area' attribute value
    area_value = selected_polygon['Shape_Area'].values[0]
    area_text = f"Area = {area_value:.2f} Sq Km"

    # Add area text
    plt.text(0.95, .05, area_text, horizontalalignment='right', verticalalignment='bottom', transform=ax.transAxes)

    #output
    output_map_location = os.path.join(output_directory, f"{heading}.pdf")

    # to a PDF file
    plt.savefig(output_map_location, format='pdf', bbox_inches='tight')

    # Clear the current figure to avoid overlapping plots
    plt.clf()
