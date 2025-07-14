# CreateMAXprojection_inDirectory

This ImageJ macro batch-processes all `.tif` images in a user-selected directory by performing maximum intensity Z-projections and saving each resulting projection as a separate TIFF in a new output folder.

## Description

This macro automates the processing of z-stacked `.tif` images by prompting the user to select an input directory, generating a maximum intensity projection for each image stack, and saving the resulting projections in a new subfolder named **Max Projections**. The script ensures the output folder does not already exist to avoid overwriting files.

## Usage

1. Open ImageJ or Fiji.
2. Copy and paste this macro into the Script Editor.
3. Run the script.
4. When prompted, select the folder containing your `.tif` z-stacks.

## Notes
1. Ensure the Bio-Formats plugin is installed and up to date.
2. This script works best with TIFF images that are z-stacks and compatible with the Bio-Formats Importer.
3. If the output folder already exists, the script will exit to prevent overwriting files.

## Macro Code

```javascript
// CreateMAXprojection_inDirectory
// This ImageJ macro processes all images in a user-selected folder by opening each image,
// creating a max projection of z-stacks, and saving each channel as a separate TIFF in a 
// new output subfolder.

input = getDirectory("Choose a folder with z-stack TIFFs");  // Prompt user to select input directory

list = getFileList(input);  // Get a list of all files and folders in the input directory

output = input + "Max Projections" + File.separator;  // Define the output directory path for max projection images

setBatchMode(true);  // Enable batch mode to speed up processing and suppress image updates

if (File.exists(output))  // Check if the output directory already exists
   exit("Destination directory already exists; remove it and then run this macro again");  // Exit if the folder exists to prevent overwriting

File.makeDirectory(output);  // Create the output directory

for (i = 0; i < list.length; i++) {  // Loop through every file/folder in the input directory

    if (endsWith(list[i], ".tif")) {  // Process only files ending with ".tif"
        
        run("Bio-Formats Importer", "open=[" + input + list[i] + "] autoscale color_mode=Colorized rois_import=[ROI manager] view=Hyperstack stack_order=XYCZT");  
        // Open the TIFF file using Bio-Formats Importer with specific options (autoscale, colorized, hyperstack view)

        run("Z Project...", "projection=[Max Intensity]");  // Perform a maximum intensity Z projection on the opened stack

        saveAs("tiff", output + getTitle);  // Save the resulting max projection image as TIFF in the output folder

        close();  // Close the max projection image

        close();  // Close the original opened image stack
    }
}

