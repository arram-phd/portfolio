.. _plaqueassay:

Automated Cell Cluster Detection 
===================================

.. figure:: images/Plaque_assay/well5_merge.PNG

    Mammary epithelial cells (blue) stained with an antibody recognizing HMGB1 (green). 9 x 9 stiched image of 20X images captured on a Nikon Ti-2 eclipse fluorescence microscope.


During paracine signaling, cells send receive ligands that trigger gene expression responses. 
As a result, express proteins in groups or "clusters" where nearby cells express a certain protein, while others do not. 

Quantification of these clusters tranditionally require manual identification and drawing of the outlines for each cluster. This is a time intensitve method and requires painstaking effort by the researhcer.

This script automatically counts the number of clusters in an image and quantifies characteriss of each cluster using image processing techqniques described below.
This includes the number of cells in each cluster, area and location of each cluster. 
Furthermore, it quantifies the intensity of protein expression in each cluster. 

This method drastically speeds up the analysis time and standardizes the workflow. This leads to less human error and more consistency acroess researchers and datasets. 

This script can be used on any immunofluorescence of any protein!

.. Flu researchers in the Diaz-Munoz Lab at UC Davis often conduct "Plaque Assays," where cells in culture are exposed to various Flu viruses. 
.. The virus infects cells and then spreads to nearby cells and this results in "plaques" being formed.

.. Traditionally, the quantification of these plaques relied on manual counting, a labor-intensive process susceptible to human error. 
.. To address this limitation, I developed a MATLAB pipeline that revolutionizes the plaque assay methodology.

.. This script not only automates plaque counting but also goes beyond, extracting comprehensive properties for each plaque.
.. It analyzes crucial aspects such as area, the number of infected cells, and the intensity of viral protein expression. 
.. This MATLAB pipeline significantly enhances the precision and efficiency of the assay, bringing it into the 21st century. 
.. This method not only simplifies the research process but also allows for a better understanding of viral infection dynamics. 

Workflow
-----------

.. figure:: images/Plaque_assay/Sidebyside_merge.png

    We will be looking at the whole image and a zoomed in region of the image. 



Object masks are generated from immunofluorescence images depicting HMGB1 (in green) and Hoescht staining (in blue). 
The Hoescht image undergoes nuclear segmentation to quantify the number of nuclei within each mask. 
To enhance detectability, images are binarized using a user-defined threshold (imbinarize), followed by the removal of small, noisy pixels (bwareaopen). 
The masks are then subjected to dilation and filling operations (imdilate and imfill) to achieve a smoother appearance without any holes. 
Then, the masks undergo an erosion step (imerode) to reverse the previous dilation process.

.. figure:: images/Plaque_assay/workflow.png


To count the number of cells in a cluster, object masks
were overlaid with the nuclear segmentation resulting from Hoechst-based segmentation.
Incorrect masks were filtered out by thresholding the min/max mask area and removing
masks that did not contain any nuclei. Finally, each mask are counted and characterized to
obtain pixel intensities (protein expression) and object areas.

.. figure:: images/Plaque_assay/Final_segmented_islands.png

    YFP channel image: counts overlayed onto each cluster

If we zoom into the image, we can visualize the mask created by the object segmentation. 

.. figure:: images/Plaque_assay/Final_segmented_islands_zoom.png

    YFP channel image: Cluster (green) and mask overlay (red)

Nuclear segmentation
----------------------

To count the number of cells in each plaque, nuclear segmentaion is conducted in the blue channel image (Hoescht stain).

.. list-table:: 

    * - .. figure:: images/Plaque_assay/Nuclear_masks.png

           All segmented nuclear maks (red)

      - .. figure:: images/Plaque_assay/Nuclear_masks_zoom.png

           Zoomed in to our region of interest


Then the cluster masks are overlayed onto the segmented nuclear masks, and the number of cells in mask is counted.

.. figure:: images/Plaque_assay/Final_segmented_islandmasks_with_nucoverlay_zoom.png

    Nuclear masks (red) ovelayed onto cluster masks (green). Number of cells within each mask are indicated. 



This script allows us to count and properly quantify the clusters in each image! 
It will output the number of clusters, location area and intensity of each cluster, and the number of cells in each cluster in a table format. 


.. Acknowledgements
.. -----------------
.. This experimental and computational pipeline was developed in collaboration with the Diaz-Munoz lab. 
.. Specifically, Illechukwu Agu devloped the experimental Cluster assay with viral protein staining, and also captured the images. 


