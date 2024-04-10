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
This is achieved by going to `Loading -> Change Channel Setup`, which opens a new window.
Here you should see a dropdown that is set to Default, with no other options present. 
By pressing `Add Channels Setup`, you can setup your own channel names, which will appear in this dropdown.
For the sample analysis, press `Add Channel` 7 times (so the total number of channels are 8).
And name them in the order of "DAPI", "CD8", "CD20", "CD3", "CD68", "CD56", "CK" and "background".
The corresponding colors used in the manuscript, which can be selected from the dropdown, are blue, red, yellow, green, orange, magenta, cyan and black.
![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/Channel_setup.png)

You can save this channel setup by clicking `Save Setup to File`, or load saved setup by clicking `Load Setup`.
Once done, press `Save and Close`.

Now this setup should appear in the dropdown as:
"DAPI:blue, CD8:red, CD20:yellow, CD3:green, CD68:orange, CD56:magenta, CK:cyan, background black"

Alternatively, if you press `Load default channels`, again a new dropdown option will appear: 
"TNBC: DAPI:blue, CD8:red, CD20:yellow, CD3:green, CD68:orange, CD56:magenta, CK:cyan"

Select the channel setup of preference, and press confirm.

### Image loading
You can open image loading window by going through `Loading -> Load Image` or directly pressing the `Load Image` in the toolbar. This allows you to load multiple tiff images. 

Go ahead and load the provided image, "sample image (1).tif". This should now provide you with a list of channels and colors at the right, with `Display` button at the bottom. Press `Display` to see the image you just loaded. By clicking/unclicking the checkboxes you can quickly turn individual channels on/off. Here, under the `Display` button, you also find a dropdown menu, where currently the option is "sample image (1).tif". As you load more images they are appended to this dropdown menu, and here you can change the image you are currently working on.
![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/Image_loading.png)

#### Load Folder
`Load Folder` is specifically designed for loading outputs of Vectra imaging, where images with "_component_data.tif" ending are loaded, with the options of combining images, stitching them (based on their coordinates outputted from the Vectra imaging platform) and downscaling.

### Threshold Foreground
The first step of the image analysis is to discriminate the tissue from non tissue. You can reach the foreground thresholding through `Image Analysis -> Threshold` Foreground, or by clicking `Threshold Foreground` in the toolbar.

This will open a new window with the name Foreground Selection, where on the left you see the image, and the right a histogram, belonging to the first channel, and under it a number of options and levels. The histogram is clickable and allows you to update the thresholds for foreground. At the top in the toolbar, you also see a `Adaptive Threshold` preceded by an unclicked checkbox and a default value of 100. Now change this value to 1000 and click the checkbox. This should change the histogram displayed at the right, but not affect the image. What this option does is it subtracts the original image uniform filtered by the value displayed here from the original image. This is the background correction that is used throught our analysis. Whenever you change the size of the `Adaptive Threshold` you need to unclick and click the checkbox again to activate the new size.

Now that you know how to generate histograms of background corrected image, let's assign some thresholds. When you hover in the histogram area, you can see the location of your cursor at the top of the histogram. The x-values will be the thresholds selected. You assign the thresholds by click-and-drag on the histogram.  When you do so, you will realize that the image will change, where the non-tissue will be diplayed grayish, and tissue display will remain the same. Once you are satisfied with the foreground of a selected channel, you can press `Add Mask` at the right, and this will then show the range of the histogram you have selected. Note that if the upper boundary of the range you selected is higher than the maximum value in the image, infinity will be assigned to the upper boundary instead. If you are not happy with the channel selected, you can always press `Remove Mask`, which will remove the current channel. You can switch between channels by the dropdown menu under the histogram. Note that when the channel is changes, it will change also the `Adaptive Threshold` to the saved settings for individual channels. In  case there was no prior saving, this means the `Adaptive Threshold` will be set to 100 and turned off.

TIP: If you want to assign a minimal value for the threshold, without assigning a maxima, after click-and-drag, double click with the mouse. Now when you press `Add Mask`, you will see that the maximum value does not change. Here, if you already have a range selected, the maximum again does not change and remains the previous assigned value. If you want the upper boundary to be infinity instead, you will first have to click `Remove Mask` before the click-drag-double click sequence.

#### Reproduction of the analysis in the manuscript
1. Change the channel to "DAPI".
2. Change the `Adaptive Threshold` to 1000.
3. Click on the checkbox next to `Adaptive Threshold` (or unclick and click). 
4. click-and-drag the mouse at the histogram, aiming for a value around 0.1, and release, double-click. You will see the image change.
5. Press `Add Mask` to add the mask corresponding to DAPI to the Foreground. You will see the numbers next to the blue-DAPI change from "0.0 - inf" to the selected threshold values.

Repeat steps 1-5 for CD8, CD20, CD3, CD68, CD56 and CK. If the values you observe next to a channel differentiate significantly from "0.1 - inf", select this channel, press `Remove Mask` and repeat steps 1-5 for this channel.
When all channels are assigned, press Show Joint Mask to see your foreground mask. This is then all the masks added together. So signals in the background corrected image of any channel higher than the assigned threshold value (which should be around 0.1) is now a part of the foreground mask

![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/Foreground_selection.png)

When satisfied, press `Save and Quit` to save and close the Threshold Foreground, and return to the main image analysis window. With a new "channel" with the name Foreground. Here you can visualize the selected foreground.

### Foreground gap-filling
In the foreground image you just created, you will realize a lot of small regions assigned to non-foreground. You will now correct this by "Filling Holes". You can reach this option through `Image Analysis -> Fill Holes` or by clicking `Fill Holes` in the toolbar. This will now open the new window titled "Fill Images" with a yellow (foreground) - purple (background) image in the left with a dropdown at top right displaying "Foreground" and two histograms at the right with titles "Foreground Area Limits" at top and "Background Area Limits" at the bottom, with the ranges indicated. Here, on both the histograms, click-and-drag to select the range from around 5000 until the upper range of the histogram. You will realize the image, the histograms and the range at the histogram title changing when you do so. Again note that if the upper limit of your selected range is larger than the image size, then the upper limit is set to the default value of infinity. When you are satisfied witht he result, click `Register Filled Image` at the left top to overwrite your Foreground image.

![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/Foreground_filling.png)

### Image filtering
Go to `Image Analysis -> Filter Image` to open the image filtering module. Here you can observe how different filters and analytical/logical operations change the image. You can add the filtered image as a new channel through `Add Image`, overwrite the current channel through `Overwrite Image`, use the image only for filtration by clicking `Use for Phenotyping`, or remove a previous filter through `Remove Image`.

#### Gaussian filtering of CK signal
Prior to tissue segmentation, we should register (i.e., `Add Image`) the gaussian filtered CK channel for tumor segmentation. First change the channel to CK by changing the second from right dropdown at the top from "DAPI" to "CK". To facilitate the observation in real-time the effect of this filtration, best to also change the top right dropdown from "Composite" to "white" or to a color of choice.

Leave the first dropdown at "Gaussian filter", change the number from 3 to 10, change the operation from "-" to "and-", the second filter from "Gaussian filter" to "Minimum" and the second number from 30 to 5000. Here the operation "and-" performs the second filter on the image that has already been processed with the first filter and then performs the subtraction of the second filtered image from the first; whereas the "-" operation performs both filters on the original image and then performs the subtraction. At this point, the image has not changed yet. You need to activate the individual filters by clicking the checkboxes. If the checkboxes are clicked while changing the filter type or size, the image is updated in real-time. Here, leave the checkbox after "Normalize" unclicked. This function normalizes the range of the filtered image so the minimal signal is 0 and the maximum is 1, which is not what we want for these images.

![](https://github.com/ErasmusMC-Bioinformatics/TME_Analyzer/blob/main/images/CK_filter.png)

Once you are satisfied with the gaussian blurred CK signal for the tissue segmentation, press `Add Image` and press `Okay`. If you now look at the main image analysis window, you will see the new channel of CK_filtered appearing on the list of channels at the right.

#### Gaussian filtering of CK signal

### Tissue segmentation

### Tissue gap-filling

### Cell segmentation

### Cell phenotyping

### Export Data

### Data Analysis