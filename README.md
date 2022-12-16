# Plotting a Macroscopic Fundamental Diagram for Data Scientists in Python forTraffic Flow in Congested Conditions

A Macroscopic Fundamental Diagram, MFD, investigates the flow of traffic through a network of roads that span a spatial dimension. The relationship between the variables speed, flow, and density can be shown in three diagrams and together make up the MFD. The diagram can then be used to identify the flow, speed, and density capacities at which traffic conditions deteriorate and ultimately breakdown. This information can help determine traffic regulation in real time when combining an MFD with a Graph Convolutional Network, GCN, for traffic forecasting.

    Flow = Speed * Density <=> q = u * k
    Speed = Flow / Density <=> u = q / k
    Density = Flow / Speed <=> k = q / u

This is a quick and simple example that data scientists can modify for their professional or academic papers in traffic congestion management. Sample data provided by ETH Zürich to demonstrate was use to illustrate an MFD in Python. The sensors are located in the small city Bolton, England presented in the map below.

A brief file structure overview of the repository is provided. The macroscopic_fundamental_diagram.py is in the root directory. The data folder houses a csv file containing flow, speed, and density data generated by street sensors. The MFD is generated in the output folder.

    /
    macroscopic_fundamental_diagram.py

    - / data /
    eth_bolton_sensor_data_utd19.csv
    eth_bolton_sensors_utd19.csv

    - / output /
    mfd_density_flow.jpg
    mfd_speed_density.jpg
    mfd_speed_flow.jpg
    bolton_sensor_map.jpg

Before jumping into the code the following requirements and packages are needed to run the code:

    Python 3.8.7

    pip3 install os 
    pip3 install numpy
    pip3 install matplotlib 
    pip3 install pandas

First the packages that were just installed are imported into our file macroscopic_fundamental_diagram.py

    import os
    import numpy as np; np.random.seed(0)
    from matplotlib import pyplot as plt
    import matplotlib.transforms as transforms
    import pandas as pd
    from numpy import *
    import matplotlib as mpl
<hr>
Next a function def speed_density_diagram is created for the Speed-Density Diagram. This function takes our dataframe as a parameter.

Starting the function some of our prickely outliers are eliminated. This should be adjusted based on the dataset.

    ## ------------------ Munich Speed Density Diagram
    def speed_density_diagram(df):

        df_speed_density = df.loc[
                         (df.SPEED< 2000) &
                         (df.SPEED > 0) &
                         (df.DENSITY< 250) &
                         (df.DENSITY> 0), :]

Next the scatter plot is created and axes minimums are defined.

    ax = df_speed_density.plot.scatter(x="DENSITY", y="SPEED", figsize=(8, 8),    marker='.', alpha=0.5,  color='#333333', s=3)

    #Set axis minimums to 0
    plt.axis([0, None, 0, None])
    
Next Uf and Kjam are calulated from using the max() function.

    Uf = df_speed_density["SPEED"].max(); # Uf
    Kjam = df_speed_density["DENSITY"].max(); # Kjam
    
Then the mid point of the diagonal is calculated using Uf and Kjam.

    # Calculate the midpoint of the diagonal
    mid_x = (0 + Kjam) / 2
    mid_y = (Uf + 0) / 2
    
Next the diagonal is added to the plot.

    plt.plot([0,mid_x], [Uf, mid_y], 'k-', lw=1.5, color= '#ffd700',)
    plt.plot([mid_x, Kjam], [mid_y, 0], 'k--', lw=1.5, color= '#ffd700',)
    
Then the box consisting of two lines is plotted.

    # Draw the line to y maximum from y axis
    plt.hlines(y = mid_y,  xmin=0, xmax=mid_x, color= '#ffd700', linestyles="--")
    # line to y maximum from x axis
    plt.vlines(x = mid_x,  ymin=0, ymax=mid_y, color= '#ffd700', linestyles="--")
    
Next the labels are added to lines.

    # Add line label to Qcap
    trans = transforms.blended_transform_factory(ax.get_yticklabels()[0].get_transform(), ax.transData)
    ax.text(mid_x+0.3, 1, "$\it{Q}$$_\mathbf{cap}$", color="#ffd700", ha="left")

    # Add line label to Uf
    trans = transforms.blended_transform_factory(ax.get_yticklabels()[0].get_transform(), ax.transData)
    ax.text(0.3, Uf+1, "$\it{U}$$_\mathbf{f}$", color="#ffd700", ha="left")

    # Add line label to Ucap
    plt.text(mid_x,mid_y+5,"(" + str(round(mid_x)) + ", " + str(round(mid_y)) + ")", color="#ffd700", weight="bold")

    # Add line label to Kcap
    trans = transforms.blended_transform_factory(ax.get_xticklabels()[0].get_transform(), ax.transData)
    ax.text(0.3, mid_y+1, "$\it{K}$$_\mathbf{cap}$", color="#ffd700", ha="left", fontname = 'Times New Roman' )

    # Add line label to Kjam
    trans = transforms.blended_transform_factory(ax.get_xticklabels()[0].get_transform(), ax.transData)
    ax.text(Kjam+0.3, 1, "$\it{K}$$_\mathbf{j}$", color="#ffd700", ha="left", fontname = 'Times New Roman' )
    
Then the axes are labeled.

    # label the axes
    ax.set_xlabel('DENSITY (vehicles/kilometer/lane)',fontweight="bold")
    ax.set_ylabel('SPEED (kilometers/minutes)',fontweight="bold")
    ax.xaxis.labelpad = 20
    ax.yaxis.labelpad = 20
    
The diagram title is added.

    # Add a title
    ax.title.set_color('#333333')
    plt.title("Speed Density Diagram", fontweight="bold", fontdict={'fontsize':14}, pad=20)
    
Finally the Speed Density diagram is saved in the output folder and generated.

    plt.tight_layout()

    #save the diagram
    print("Saving diagram...")
    plt.savefig(OS_PATH + "/output/mfd_speed_density.jpg", dpi=1000)

    #show the diagram
    print("Generating diagram...")
    plt.show()
<hr>
Next the function for the speed flow diagram is created and also takes the dataframe as a parameter.

  def speed_flow_diagram(df):

      df_speed_flow = df.loc[
                          (df.FLOW< 2000) &
                          (df.FLOW > 0) &
                          (df.SPEED< 250) &
                          (df.SPEED> 0), :]
                        
Next the scatter plot is created.

       ax = df_speed_flow.plot.scatter(x="FLOW", y="SPEED", figsize=(8, 8), marker='.', alpha=0.5,  color='#333333', s=3)

After resorting the data arrays the max values for flow and max value for speed are found.

      # Find the x value to the y maximum change to numpy arrays
      x_data = np.array([])
      for row in df_speed_flow["FLOW"]:
          x_data = np.append(x_data, row)

      y_data = np.array([])
      for row in df_speed_flow["SPEED"]:
          y_data= np.append(y_data, row)

      # Resort the arrays
      order = x_data.argsort()
      y_data = y_data[order]
      x_data = x_data[order]

      max_x = df_speed_flow["FLOW"].max();
      max_y= np.interp(max_x, x_data, y_data,  left=None, right=None, period=None)

Next the diagonal and the lines for the box are added to the plot using the max values.

      # Line to x maximum from y axis
      plt.plot([0, max_x], [0, max_y], 'k--', lw=1.5, color= '#ffd700',)
      # Line to y maximum from y axis
      plt.hlines(y = max_y,  xmin=0, xmax=max_x, color= '#ffd700', linestyles="--")
      # Line to y maximum from x axis
      plt.vlines(x = max_x,  ymin=0, ymax=max_y, color= '#ffd700', linestyles="--")

The line labels are added.

      # Add line label to Qcap
      trans = transforms.blended_transform_factory(ax.get_yticklabels()[0].get_transform(), ax.transData)
      ax.text(max_x+9, 3, "$\it{Q}$$_\mathbf{cap}$", color="#ffd700", ha="left")

      # Add line label to Kcap
      plt.text(max_x+12,max_y+3,"$\it{K}$$_\mathbf{cap}$ (" + str(round(max_x,2)) + ", " + str(round(max_y)) + ")", color="#ffd700", weight="bold")

      # Add line label to Ucap
      trans = transforms.blended_transform_factory(ax.get_xticklabels()[0].get_transform(), ax.transData)
      ax.text(9, max_y+3, "$\it{U}$$_\mathbf{cap}$", color="#ffd700", ha="left", fontname = 'Times New Roman' )

Using the max values the diagram’s axes limits are set.

      # Set axes limits
      plt.axis([0, max_x +50, 0, max_y +50])

The axes labels are added.

      # Label the axes
      ax.set_xlabel('FLOW (vehicles/15 minutes/lane))', fontweight="bold")
      ax.set_ylabel('SPEED (kilometers/15 minutes)', fontweight="bold")
      ax.xaxis.labelpad = 20
      ax.yaxis.labelpad = 20

The title is added.

      # Add a title
      ax.title.set_color('#333333')
      plt.title("Speed Flow Diagram", fontweight="bold", fontdict={'fontsize':14}, pad=20)

Finally the Speed Flow diagram is saved in the output folder and generated.

      #save the diagram
      print("Saving diagram...")
      plt.savefig(OS_PATH + "/output/mfd_speed_flow.jpg", dpi=1000)

      #show the diagram
      print("Generating diagram...")
      plt.show()
<hr>
Finally a function for the flow density diagram is created.

  def flow_density_diagram(df):

      # Remove outliers
      df_flow_density = df.loc[
              (df.FLOW< 2000) &
              (df.FLOW > 0) &
              (df.DENSITY< 50) &
              (df.DENSITY> 0), :]

Once again Kjam is calculated.

      Kjam = df_flow_density["DENSITY"].max()

Next the scatter plot is created.

      # Plot the scatter diagram
      ax = df_flow_density.plot.scatter(x="DENSITY", y="FLOW", figsize=(8, 8), marker='.', alpha=0.5,  color='#333333', s=3)

After resorting the data arrays the max values for density and max value for flow are found.

      # Find the x value to the y maximum change to numpy arrays
      x_data = np.array([])
      for row in df_flow_density["DENSITY"]:
          x_data = np.append(x_data, row)
        
      y_data = np.array([])
      for row in df_flow_density["FLOW"]:
          y_data= np.append(y_data, row)
        
      # resort the arrays
      order = y_data.argsort()
      y_data = y_data[order]
      x_data = x_data[order]

      max_y = df_flow_density["FLOW"].max()
      max_x = np.interp(max_y, y_data, x_data,  left=None, right=None, period=None)

Next the the diagonal and the lines creating the box are added to the plot.

      # line from origin to the max
      plt.plot([0, max_x], [0, max_y], 'k--', lw=1.5, color= '#ffd700',)
      # line to y maximum from y axis
      plt.hlines(y = max_y,  xmin=0, xmax=max_x, color= '#ffd700', linestyles="--")
      # line to y maximum from x axis
      plt.vlines(x = max_x,  ymin=0, ymax=max_y, color= '#ffd700', linestyles="--")

Labels are added to the lines.

      # Add line label to Qcap
      trans = transforms.blended_transform_factory(ax.get_yticklabels()[0].get_transform(), ax.transData)
      ax.text(0.3, max_y+10, "$\it{Q}$$_\mathbf{cap}$", color="#ffd700", ha="left")

      # Add line label to Ucap
      plt.text(max_x+0.7,max_y+10,"$\it{U}$$_\mathbf{cap}$ (" + str(round(max_x,2)) + ", " + str(round(max_y)) + ")", color="#ffd700", weight="bold")

      # Add line label to Kcap
      trans = transforms.blended_transform_factory(ax.get_xticklabels()[0].get_transform(), ax.transData)
      ax.text(max_x+0.3, 10, "$\it{K}$$_\mathbf{cap}$", color="#ffd700", ha="left", fontname = 'Times New Roman' )

      # Add line label to Kjam
      trans = transforms.blended_transform_factory(ax.get_xticklabels()[0].get_transform(), ax.transData)
      ax.text(Kjam+0.3, 10, "$\it{K}$$_\mathbf{jam}$", color="#ffd700", ha="left", fontname = 'Times New Roman' )

Next a polynomial fit is calculated and added to the plot. This should be adjusted based on the data.

      # Polynomial fit
      def fit(ax, x,y, sort=True):
          z = np.polyfit(x, y, 1)
          fit = np.poly1d(z)
          print(fit)
          if sort:
              x = np.sort(x)
          ax.plot(x, fit(x), label="Polynomial f(x)^1: " '''+ str(fit)''', lw=1.5, color= '#ffd700', alpha=1)  
          ax.legend()

      # Plot the Polynomial fit
      fit(ax, df_flow_density["DENSITY"],df_flow_density["FLOW"], sort=True) 

Using the max values the diagram’s axes limits are set.

      # Set axis limits
      plt.axis([0, max_x+50, 0, max_y+50])

The diagram’s axes are labeled.

      # Label the axes
      ax.set_xlabel('DENSITY (vehicles/kilometer/lane)', fontweight="bold")
      ax.set_ylabel('FLOW (vehicles/15 minutes/lane)', fontweight="bold")
      ax.xaxis.labelpad = 20
      ax.yaxis.labelpad = 20

The diagram’s title is added.

      # Add a title
      ax.title.set_color('#333333')
      plt.title("Density Flow Diagram", fontweight="bold", fontdict={'fontsize':14}, pad=20)
    
Finally the Density Flow diagram is saved in the output folder and generated.

      plt.tight_layout()

      #save the diagram
      print("Saving diagram...")
      plt.savefig(OS_PATH + "/output/mfd_density_flow.jpg", dpi=1000)

      #show the diagram
      print("Generating diagram...")
      plt.show()
     
<hr>
Next the dataframe is loaded from the csv file using the os path. The data is formatted and NaNs are removed. Queries and error removals are optional.

    ### Fetch the data from eth_bolton_sensor_data_utd19.csv

    OS_PATH = os.path.dirname(os.path.realpath('__file__'))

    # Data Import Path
    MST_SENSOR_DATA_CSV   = OS_PATH + '/data/eth_bolton_sensor_data_utd19.csv'

    # Data Import
    df = pd.read_csv(MST_SENSOR_DATA_CSV)

    # Keep only relevant columns
    df = df.loc[:, ("DATE", "INTERVAL", "ID", "FLOW" ,"DENSITY","SPEED", "CITY")]

    # Convert to dataframe
    df = pd.DataFrame(df)

    # drop all rows with any NaN and NaT values
    df = df[df['FLOW'].notna()]
    df = df[df['SPEED'].notna()]
    df = df[df['DENSITY'].notna()]

    df =  df.loc[(df!=0).any(axis=1)]

    # Error Removal
    df = df.drop_duplicates(subset='DENSITY', keep="last")

    # Query
    #df = df[(df['DATE'].str.contains("2017-11-13")==True)]

Next default chart formats are defined.

    # Set default chart font
    plt.rcParams["font.family"] = "Times New Roman"

    # Set data junk size for polynomial trends
    mpl.rcParams['agg.path.chunksize'] = 10000

    # Set defaults for all charts
    params = {        
            'lines.linewidth': 2,
            'figure.titlesize': 14,
            'figure.titleweight': 'bold',
            'font.family':  'Times New Roman',
            'font.weight':  'bold',
            'text.color': '#333333',
            'axes.titlesize': 12,
            'axes.titlecolor': '#333333',
            'axes.titleweight': 'bold',
            'axes.edgecolor': '#333333',
            'axes.labelweight': 'bold',
            'axes.labelsize': 10,
            'xtick.color': '#333333',
            'ytick.color': '#333333',
            'legend.loc' : 'upper right',
            'figure.constrained_layout.use': True,
            }
    plt.rcParams.update(params)

Finally the functions are called and the dataframe is passed as a parameter to each function.

  speed_density_diagram(df)
  speed_flow_diagram(df)
  flow_density_diagram(df)

The functions together output a Macroscopic Fundamental Diagram for the city of Bolton, England.

    mfd_speed_density.jpg
    mfd_speed_flow.jpg
    mfd_density_flow.jpg

Data Provided by ETH Zurich https://utd19.ethz.ch/

The GitHub repository can be found here.
