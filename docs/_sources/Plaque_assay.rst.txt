.. _plaqueassay:

Automated Viral Plaque Assay
==============================

.. figure:: images/Plaque_assay/well5_merge.PNG

    Kidney cells (blue) infected with influenza, resulting in viral plaques (green). 9 x 9 stiched image of 20X images captured on a Nikon Ti-2 eclipse fluorescence microscope.


Flu researchers in the Diaz-Munoz Lab at UC Davis often conduct "Plaque Assays," where cells in culture are exposed to various Flu viruses. 
The virus infects cells and then spreads to nearby cells and this results in "plaques" being formed.

Traditionally, the quantification of these plaques relied on manual counting, a labor-intensive process susceptible to human error. 
To address this limitation, I developed a MATLAB pipeline that revolutionizes the plaque assay methodology.

This script not only automates plaque counting but also goes beyond, extracting comprehensive properties for each plaque.
It analyzes crucial aspects such as area, the number of infected cells, and the intensity of viral protein expression. 
This MATLAB pipeline significantly enhances the precision and efficiency of the assay, bringing it into the 21st century. 
This method not only simplifies the research process but also allows for a better understanding of viral infection dynamics. 

Workflow
-----------

.. figure:: images/Plaque_assay/Sidebyside_merge.png

    We will be looking at the whole image and a zoomed in region of the image. 



Immunofluorescence images of Nucleoprotein (green) and Hoescht (blue) staining are processed
to create object masks. The number of nuclei within each mask are then
counted using nuclear segmentation of the Hoescht image. To create detectable objects,
images are binarized (imbinarize) with a user-defined threshold, followed by the removal of
small, noisy pixels (bwareaopen). The masks are then dilated and filled (imdilate and imfill)
to smoothen them and ensure they do not contain holes. Next, masks are eroded to undo
the dilation step (imerode). 

.. figure:: images/Plaque_assay/workflow.png


To count the number of cells in viral cluster, object masks
were overlaid with the nuclear segmentation resulting from Hoechst-based segmentation.
Incorrect masks were filtered out by thresholding the min/max mask area and removing
masks that did not contain any nuclei. Finally, each mask are counted and characterized to
obtain pixel intensities (viral protein expression) and object areas.

.. figure:: images/Plaque_assay/Final_segmented_islands.png

    YFP channel image: counts overlayed onto each plaque

If we zoom into the image, we can visualize the mask created by the object segmentation. 

.. figure:: images/Plaque_assay/Final_segmented_islands_zoom.png

    YFP channel image: Plaque (green) and mask overlay (red)

Nuclear segmentation
----------------------

To count the number of cells in each plaque, nuclear segmentaion is conducted in the blue channel image (Hoescht stain).

.. list-table:: 

    * - .. figure:: images/Plaque_assay/Nuclear_masks.png

           All segmented nuclear maks (red)

      - .. figure:: images/Plaque_assay/Nuclear_masks_zoom.png

           Zoomed in to our region of interest


Then the plaque masks are overlayed onto the segmented nuclear masks, and the number of cells in mask is counted.

.. figure:: images/Plaque_assay/Final_segmented_islandmasks_with_nucoverlay_zoom.png

    Nuclear masks (red) ovelayed onto plaque masks (green). Number of cells within each mask are indicated. 



This script allows us to count and properly quantify the viral palques in each image! 
It will output the number of clusters, location area and intensity of each cluster, and the number of cells in each cluster in a table format. 


Acknowledgements
-----------------
This experimental and computational pipeline was developed in collaboration with the Diaz-Munoz lab. 
Specifically, Illechukwu Agu devloped the experimental Plaque assay with viral protein staining, and also captured the images. 


