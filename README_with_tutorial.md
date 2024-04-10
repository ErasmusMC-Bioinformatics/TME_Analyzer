# TME_Analyzer
## Getting started

### Venv

Get python version 3.8.10
- Create a venv: `python.exe -m venv .venv`
- Activate the environment `./.venv/Scripts/Activate.ps1`; 
  - Maybe with an execution policy:  `powershell -ExecutionPolicy Bypass -File "./.venv/Scripts/Activate.ps1"`
- install dependencies: `python.exe -m pip install -r requirements.txt`
  - note that requirements.txt give the minimal packages, and this should get all the packages in the requirements_extended.txt file
  
And run: `python.exe TME_analyzer.py`

### Conda

Run: `mamba env create -f environment.yml`

And run: `python.exe TME_analyzer.py`

## Cx-Freeze

Pack the project into a single binary with: 
```python setup.py build```

## Analysis tutorial

### Channel setup

By default, TME-A names image channels as C1, C2, etc. when you open an image with it.
The channel setup should be input to the software before loading of images in order to have the proper naming of the channels, together with the display colors.
This is achieved by going to Loading->Change Channel Setup, which opens a new window.
Here you should see a dropdown that is set to Default, with no other options present. 
By pressing "Add Channels Setup", you can setup your own channel names, which will appear in this dropdown.
For the sample analysis, press "Add Channel" 7 times (so the total number of channels are 8).
And name them in the order of "DAPI", "CD8", "CD20", "CD3", "CD68", "CD56", "CK" and "background".
The corresponding colors used in the manuscript, which can be selected from the dropdown, are blue, red, yellow, green, orange, magenta, cyan and black.
![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/Channel_setup.png)

You can save this channel setup by clicking "Save Setup to File", or load saved setup by clicking "Load Setup".
Once done, press "Save and Close".

Now this setup should appear in the dropdown as:
"DAPI:blue, CD8:red, CD20:yellow, CD3:green, CD68:orange, CD56:magenta, CK:cyan, background black"

Alternatively, if you press "Load default channels", again a new dropdown option will appear: 
"TNBC: DAPI:blue, CD8:red, CD20:yellow, CD3:green, CD68:orange, CD56:magenta, CK:cyan"

Select the channel setup of preference, and press confirm.

### Image loading
You can open image loading window by going through Loading->Load Image or directly pressing the "Load Image" in the toolbar. This allows you to load multiple tiff images. 

Go ahead and load the provided image, "sample image (1).tif". This should now provide you with a list of channels and colors at the right, with "Display" button at the bottom. Press "Display" to see the image you just loaded. By clicking/unclicking the checkboxes you can quickly turn individual channels on/off. Here, under the "Display" button, you also find a dropdown menu, where currently the option is "sample image (1).tif". As you load more images they are appended to this dropdown menu, and here you can change the image you are currently working on.
![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/Image_loading.png)

#### Load Folder
"Load Folder" is specifically designed for loading outputs of Vectra imaging, where images with "_component_data.tif" ending are loaded, with the options of combining images, stitching them (based on their coordinates outputted from the Vectra imaging platform) and downscaling.

### Threshold Foreground
The first step of the image analysis is to discriminate the tissue from non tissue. You can reach the foreground thresholding through Image Analysis->Threshold Foreground, or by clicking "Threshold Foreground" in the toolbar.

This will open a new window with the name Foreground Selection, where on the left you see the image, and the right a histogram, belonging to the first channel, and under it a number of options and levels. The histogram is clickable and allows you to update the thresholds for foreground. At the top in the toolbar, you also see a "Adaptive Threshold" preceded by an unclicked checkbox and a default value of 100. Now change this value to 1000 and click the checkbox. This should change the histogram displayed at the right, but not affect the image. What this option does is it subtracts the original image uniform filtered by the value displayed here from the original image. This is the background correction that is used throught our analysis. Whenever you change the size of the "Adaptive Threshold" you need to unclick and click the checkbox again to activate the new size.

Now that you know how to generate histograms of background corrected image, let's assign some thresholds. When you hover in the histogram area, you can see the location of your cursor at the top of the histogram. The x-values will be the thresholds selected. You assign the thresholds by click-and-drag on the histogram.  When you do so, you will realize that the image will change, where the non-tissue will be diplayed grayish, and tissue display will remain the same. Once you are satisfied with the foreground of a selected channel, you can press "Add Mask" at the right, and this will then show the range of the histogram you have selected. If you are not happy with the channel selected, you can always press "Remove Mask", which will remove the current channel. You can switch between channels by the dropdown menu under the histogram. Note that when the channel is changes, it will change also the "Adaptive Threshold" to the saved settings for individual channels. In  case there was no prior saving, this means the "Adaptive Threshold" will be set to 100 and turned off.

TIP: If you want to assign a minimal value for the threshold, without a maxima, first click "Remove Mask" and after click-and-drag, double click with the mouse. Now when you press "Add Mask", you will see that the maximum value does not change. Here, if you already have a range selected, the maximum again does not change and remains the previous assigned value.

#### Reproduction of the analysis in the manuscript
1. Change the channel to "DAPI".
2. Change the "Adaptive Threshold" to 1000.
3. Click on the checkbox next to "Adaptive Threshold" (or unclick and click). 
4. click-and-drag the mouse at the histogram, aiming for a value around 0.1, and release, double-click. You will see the image change.
5. Press "Add Mask" to add the mask corresponding to DAPI to the Foreground. You will see the numbers next to the blue-DAPI change from "0.0 - inf" to the selected threshold values.

Repeat steps 1-5 for CD8, CD20, CD3, CD68, CD56 and CK. If the values you observe next to a channel differentiate significantly from "0.1 - inf", select this channel, press "Remove Mask" and repeat steps 1-5 for this channel.
When all channels are assigned, press Show Joint Mask to see your foreground mask.



When satisfied, press "Save and Quit" to save and close the Threshold Foreground, and return to the main image analysis window.

### Foreground gap-filling


### Image filtering

### Tissue segmentation

### Tissue gap-filling

### Cell segmentation

### Cell phenotyping

### Export Data

### Data Analysis