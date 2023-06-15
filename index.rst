:tocdepth: 1

.. sectnum::

.. Metadata such as the title, authors, and description are set in metadata.yaml

Abstract
========================

Iterative improvement of LUT through balance forces. 
Instructions and results.


Introduction and Motivation:
================================



Instructions:
================

Find EFD data to use for LUT improvement
--------------------------------------------
- Query the EFD to find the start and end time of the data you want to use for LUT improvement. 
Try to find the exact time where the sweep from 0deg to 90deg (or from 90 deg to 0 deg) started.
You will have to select the times in utc. 

Here is an example of how you can query and plot the data to find the elevations

.. code-block:: python

   start = Time('2023-05-31 08:35:0Z', scale='utc')
   end = Time('2023-05-31 09:05:0Z', scale='utc')

   # Retrieve elevations
   elevations = await client.select_time_series(
      'lsst.sal.MTMount.elevation',
      ['actualPosition', 'timestamp'],  
      start, 
      end,
   )  
   elevations = elevations['actualPosition'].resample('1T').mean()
   elevations.plot()
   plt.xlabel('Time (utc)')
   plt.ylabel('elevation (deg)')


Once you have chosen the times you want to look at, write them down. You will need them for the next step.

LUT Improvement Script
--------------------------------------------

- Clone the ts_aos_utils repository that you can find `here <https://github.com/lsst-ts/ts_aos_utils/>`__
Do the following

.. code-block:: bash

   git clone https://github.com/lsst-ts/ts_aos_utils/

- Go to the directory where you cloned the repository and run the script, which is located at ``python/lsst/ts/aos/utils/scripts``

- Run the script M1M3LUT.py which will generate a LUT file in the same directory. You can run the script as follows

.. code-block:: python

   python3 M1M3LUT.py force_type start_time end_time axis --lut_path --polynomial_degree --resample_rate

   # axis = ['X', 'Y', 'Z']
   # force_type = ['Balance', 'Applied']
   # --lut_path = path to the LUT file you want to improve
   # --polynomial_degree = degree of the polynomial you want to fit the data to
   # --resample_rate = resample rate of the data you want to use for the LUT improvement. 

- You will not have to change the polynomial degree or the resample rate. The default values are 5 and 1T respectively.

- An example below:

.. code-block:: python

   python3 M1M3LUT.py 'Balance' '2023-05-31 08:35:0Z' '2023-05-31 09:05:0Z' 'X' --lut_path="path/to/ts_m1m3support/SettingFiles/Tables/"


Updating the LUT Serial
---------------------------------------------

TBD by Petr



Test rundown:
================
- Find times in EFD data to use for LUT improvement
- Run the script to generate a new LUT file for Z, Y and X axis. You will have to run the script three times. You can choose 'Balance' type to start with.
- Update the Serial
- Do a 0 to 90 deg (or 90deg to 0 deg) sweep again and repeate the previous steps.
- Do this 5 times.
