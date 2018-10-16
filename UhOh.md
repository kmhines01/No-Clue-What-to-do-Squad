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

sciframes = glob.glob('NGC0695.00**.fits')
for imgfname in sciframes:
	ccdsci = astropy.nddata.CCDData.read(imgfname, unit='electron')
	ccdsci-b = ccdproc.subtract_bias(ccdsci, masterbias)
	ccdsci-bf = ccdproc.flat_correct(ccdsci-b, Cmasterflat)
	nwname = imgfname.split('.fits')
	mstrname = nwname[0] + 'bfcorrected.fits'
	ccdsci-bf.write(mstrname)

   ###imgfname is image file name; -b is minus bias; -bf is minus bias and flat; nwname
	is new name, mstrname is master name###

 
