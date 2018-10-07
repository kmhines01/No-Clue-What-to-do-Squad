import sys
import os 
import glob
import numpy as np
import astropy
from astropy.io import fits
from astropy import units as u
from astropy.nddata import CCDData
import ccdproc
from ccdproc import CCDData
from ccdproc import ImageFileCollection
import matplotlib.pyplot as plt

bias = glob.glob('Bias.00**.fits')
masterbias = ccdproc.combine(bias, method='median', unit=u.electron)
masterbias.write('CCD_MasterBias.fits')

flat = glob.glob('Flat.00**.fits')
masterflat = ccdproc.combine(flat, method='median', unit=u.electron)
masterflat.write('CCD_MasterFlat.fits')
Cmasterflat = ccdproc.subtract_bias(masterflat, masterbias)
Cmasterflat.write('FinalCCD_MasterFlat.fits')

dark = glob.glob('Dark.00**.fits')
masterdark = ccdproc.combine(dark, method='average', unit=u.electron)
Cmasterdark = ccdproc.subtract_bias(masterdark, masterbias)
Cmasterdark.write('FinalCCD_MasterDark.fits')

### this is where the certainty ends ###
 
sciframes = glob.glob('NGC0695.00**.fits')
for i in sciframes:
	Sciccd =  astropy.nddata.CCDData.read(i, unit='electron')
	nbsci = ccdproc.subtract_bias(sciccd, masterbias)

Sciccd1 = astropy.nddata.CCDData.read('NGC0695.0001.fits', unit='electron')
nbsci1 = ccdproc.subtract_bias(sciccd1, masterbias)


 
