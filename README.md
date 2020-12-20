# Radar-Target-Generation
## Goal
The goal of the project is as following : 
* Configure the FMCW waveform based on the system requirements.
* Define the range and velocity of target and simulate its displacement.
* For the same simulation loop process the transmit and receive signal to determine the beat signal
* Perform Range FFT on the received signal to determine the Range
* Towards the end, perform the CFAR processing on the output of 2nd FFT to display the target.

## Implementation steps for the 2D CFAR process
### CFAR
Constant False Alarm Rate also known as CFAR, is dynamic thresholding technique, which varies the signal detection threshold as a function of neighbouring evvironment sensing. 
The CFAR technique estimates the level of interference in radar range and doppler cells "Training Cells" on either or both the side of the "Cell Under Test". The estimate is then used to decide if the target is in the Cell Under Test (CUT). This process loops across all the range cells and decides the presence of target based on the noise estimate. The background is that when noise is present, the cells around the cell of interest will contain a good estimate of the noise.

Following steps were implemented for 2DCFAR:
* Determine the number of training cells for each dimension tr and td. Similarly, pick the number for guard cells gr and gd.
* Slide the cell under test (CUT) across the complete cell matrix.
* For every iteration sum the signal level within all the training cells. To sum convert the value from logarithmic to linear using dbpow2 function.
* Average the summed values for all of the training cells used.
* Further multiply the offset to it to determine the threshold. After this convert it back to logarithmic using pow2db.
* Next, compare the signal under CUT against this threshold.
* If the CUT level > threshold assign it a value of 1, else equate it to 0.
* To keep the map size same as it was before CFAR, equate all the non-thresholded cell to 0.

The implementation can be found in "radar_target_generation_and_detection.m" file between line 148-197

## Selection of Training, Guard cells and offset
Number of Training cells in range dimension (tr) = 10
Number of Training cells in doppler dimension (td) = 14
Number of Guard cells in range dimension (gr) = 5
Number of Guard cells in doppler dimension (gd) = 10
Offset = 3

## Steps taken to suppress the non-thresholded cells at the edges
The process above will generate a thresholded block, which is smaller than the Range Doppler Map as the CUT cannot be located at the edges of matrix. Hence, few cells will not be thresholded. To keep the map size same set those values to 0. Any cell value that is neither 1 nor a 0, assign it a zero. 
This is done by taking a array with same dimension as the RDM and assign all values to O. Only the CUT which contains the target, will be set to 1.

## Sample output images
### Output after first FFT. The peak in generated with the given initial range of 100m +/- 10m
![picture alt](https://github.com/rakeshp-123/Radar-Target-Generation/blob/main/images/range_from_first_fft.jpg)

### Output after 2D FFT. It shows many noisy small peaks or clutters.
![picture alt](https://github.com/rakeshp-123/Radar-Target-Generation/blob/main/images/RDM.jpg)

### 2D CFAR output removes the clutters.
![picture alt](https://github.com/rakeshp-123/Radar-Target-Generation/blob/main/images/2Dcfar.jpg)

