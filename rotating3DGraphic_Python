import matplotlib.pyplot as plt
import numpy as np
from scipy.optimize import curve_fit
from matplotlib.animation import FuncAnimation # Importing the animation module for creating dynamic plots

# My raw data arrays as provided
my_matrix_x = np.array([0.15, 0.175, 0.2, 0.225, 0.25, 0.275, 0.3, 0.325, 0.35, 0.375, 0.4, 0.425, 0.45, 0.15, 0.175, 0.2, 0.225, 0.25, 0.275, 0.3, 0.325, 0.35, 0.375, 0.4, 0.425, 0.45, 0.15, 0.175, 0.2, 0.225, 0.25, 0.275, 0.3, 0.325, 0.35, 0.375, 0.4, 0.425, 0.45])
my_matrix_y = np.array([140, 140, 140, 140, 140, 140, 140, 140, 140, 140, 140, 140, 140, 120, 120, 120, 120, 120, 120, 120, 120, 120, 120, 120, 120, 120, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100, 100])
my_matrix_z_str = [19.9467839614219, 17.4302325779657, 15.3503015690599, 13.5509769649853, 12.9074130130905, 12.3331320166279, 12.3628704318582, 12.8965479890528, 13.7284310273646, 15.5319633256349, 18.4985815597338, 23.7605949707626, 31.4762094938244, 35.1241645700323, 28.7610296741957, 23.7327648793543, 19.6962039322882, 17.372186442498, 15.5486098775112, 14.6592586988368, 14.5239592839563, 14.8742078222816, 16.3348604886269, 19.0602006153924, 23.9501717539603, 31.6040424339607, 'null', 'null', 'null', 'null', 'null', 26.0025659309128, 21.9489438205299, 19.5556840050451, 18.313140355813, 18.6632530773208, 20.6234116887367, 24.9924897289063, 32.2961194326079]

# Processing my Z-data to replace 'null' strings with NumPy's NaN (Not a Number)
# This allows numerical operations and proper filtering of missing values.
my_matrix_z_processed = [float(val) if val != 'null' else np.nan for val in my_matrix_z_str]
my_matrix_z = np.array(my_matrix_z_processed)

# Filtering out data points where Z is NaN.
# This ensures that my surface fitting and plotting only use valid data.
valid_indices = ~np.isnan(my_matrix_z)
x_data = my_matrix_x[valid_indices]
y_data = my_matrix_y[valid_indices]
z_data = my_matrix_z[valid_indices]

# Defining the function for surface fitting.
# I'm using a general second-degree polynomial (ax + by + cx^2 + dy^2 + exy + f)
# This function takes (x,y) coordinates and returns the corresponding Z value based on parameters.
def func(xy, a, b, c, d, e, f):
    x, y = xy
    return a * x + b * y + c * x**2 + d * y**2 + e * x * y + f

# Providing an initial guess for the polynomial parameters (a, b, c, d, e, f).
# Good initial guesses can help the optimization algorithm converge faster and more reliably.
initial_guess = [1, 1, 0.1, 0.1, 0.1, 1]

# Attempting to fit the surface to my data points.
# curve_fit finds the optimal parameters for 'func' that best approximate my (x_data, y_data, z_data).
params = None # Initialize params to None in case fitting fails
try:
    params, covariance = curve_fit(func, (x_data, y_data), z_data, p0=initial_guess)
except RuntimeError as e:
    # If the fitting process encounters an error (e.g., convergence issues), I'll print a message.
    print(f"Error attempting to fit the curve: {e}")
    print("It might be necessary to adjust the fitting function or the initial guess.")

# --- Setting up the 3D Plot ---

# Creating a new figure for my plot with a specified size.
fig = plt.figure(figsize=(10, 8))
# Adding a 3D subplot to the figure. '111' means 1x1 grid, first subplot.
ax = fig.add_subplot(111, projection='3d')

# Plotting the fitted surface if the curve fitting was successful.
if params is not None:
    # Generating a meshgrid of X and Y values to create a smooth surface.
    # These points span the range of my original data.
    x_surf = np.linspace(x_data.min(), x_data.max(), 50)
    y_surf = np.linspace(y_data.min(), y_data.max(), 50)
    X_surf, Y_surf = np.meshgrid(x_surf, y_surf)
    
    # Calculating the Z values for the fitted surface using the optimized parameters.
    Z_surf = func((X_surf, Y_surf), *params)
    
    # Plotting the 3D surface. 'cmap' defines the color map, 'alpha' sets transparency.
    ax.plot_surface(X_surf, Y_surf, Z_surf, cmap='viridis', alpha=0.7)

# Adding labels to my axes for clarity.
ax.set_xlabel('Lean Loading factor (moles CO2/moles MEA)')
ax.set_ylabel('Reboiler Temperature (ºC)')
ax.set_zlabel('Energy (kJ/kg CO2)')
# Setting a descriptive title for my plot.
ax.set_title('Energy Response Surface as a Function of Lean Loading Factor and Reboiler Temperature')

# Setting the initial viewing angle for my 3D plot.
# 'elev' is the elevation angle (vertical rotation), 'azim' is the azimuth angle (horizontal rotation).
ax.view_init(elev=20, azim=0) # My preferred starting view

# --- Animation Function ---

# This function will be called repeatedly by the animation object to update the plot's view.
def rotate(frame):
    # 'frame' is an integer representing the current frame number, incrementing with each call.
    # I'm updating the azimuthal angle ('azim') to create a horizontal rotation.
    # 'elev' is kept constant (20 degrees) to maintain a consistent vertical perspective.
    # 'frame * 1' means the azimuth will increase by 1 degree per frame.
    ax.view_init(elev=20, azim=frame * 1)
    # I need to return the figure object for the animation to update correctly.
    return fig,

# --- Creating and Running the Animation ---

# FuncAnimation orchestrates the animation process.
# - 'fig': The figure to animate.
# - 'rotate': My function that updates the view for each frame.
# - 'frames=1000': The total number of frames in my animation. This will make it rotate for 1000 degrees.
# - 'interval=100': The delay between frames in milliseconds. 100ms means 10 frames per second (1000ms/100ms).
# - 'blit=False': Setting this to False is generally recommended for 3D plots as it can be problematic with True.
ani = FuncAnimation(fig, rotate, frames=1000, interval=100, blit=False)

# Displaying the animation. This will open an interactive window with the rotating plot.
plt.show()
