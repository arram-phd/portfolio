.. _supmixing:

Spectral Unmixing
===================

.. figure:: images/unmixing2.png

    How do we image four different fluorescent proteins at once? 

During my internship in the Leonetti group at Chan Zuckerberg Biohub, my project involved correcting for crosstalk in confocal microscopy images. 
My mentor and I configured the microscope for dual-camera imaging. This required the simultaneous excitation of two fluorescent proteins, and then using a beam-splitter to send low and high wavelength emission to different cameras, respectively. 
The configuration allowed for imaging of four different fluorescent proteins in a sample.

The main challenge with this system was that some emitted light from one fluorescent protein may bleed through to the undesired camera, thereby resulting in crosstalk. 

To overcome this, our approach was to model and simulate the light path of the microscope and calculate fluorophore sensitivity constants for a given set of fluorescent proteins in each of the imaging channels. 

.. figure:: images/unmixing1.png


I used Python to create a function that inputs a set of fluorescent proteins and microscope acquisition settings to return the channel-fluorophore sensitivity constants, which then can be used to perform linear unmixing of pixel intensities of the captured image. 

.. figure:: images/unmixing6.png

    Our light path model

Imagine the following experimental setup: BFP and mScarlet imaged simultaneously on the left camera, along with mNeonGreen and iRFP760 simultaneously on the right camera. 
The model predicted that 1% of the signal in the red channel would be emission from BFP, and 16% of the signal in the far red will be emitted from mScarlet.

.. figure:: images/unmixing4.png

    Light path simulation of BFP and mScarlet | mNeonGreen and iRFP760

We could then linearly unmix an image to correct for the crosstalk.

How it works
------------

The spectral model calculates the expected signal intensities using the specfic configuration of the microscope, and the properties of each fluorescent proteins requested. 
The shaed region below is the product of all of the listed properties, summed across wavelengths.

.. figure:: images/unmixing5.png

    Expected signal calculation for iRFP670

The python code will do this calculate for each input fluorophore in each channel. 

.. figure:: images/unmixing7.png

    Four requested fluorophores in four channels

The script will output these shaded values as a normalized matrix which gives the user the expected signal per fluorophore per channel as percentages. 
These values can then be used to correct for crosstalk in the images taken on this particular microscope given these specific acquisition settings. 


Code Availability
------------------
https://github.com/abzzram/specmix









