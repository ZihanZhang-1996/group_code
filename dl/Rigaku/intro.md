# ConvertRASX

Extracts image from Rigaku .rasx format and converts it to .tiff and .jpg

Julian Mars (julian.mars@outlook.com)

=== "Import"

    ```python
    import numpy as np
	import matplotlib.pyplot as plt
	from zipfile import ZipFile
	from PIL import Image
	from pathlib import Path
	from glob import glob
	import io as io
    ```
=== "Defs"

    ```python
    def open_rasx(fname):
    """
	    Opens rigaku .rasx image files. File structure is as following:
	        .rasx -> zip
	            -root.xml
	            -DataN
	                -ImageX.bin
	                -MesurementConditions0.xml
	            
	    """
	    with ZipFile(fname) as myzip:
	        with myzip.open('Data0/Image0.bin') as myfile:
	            im = np.frombuffer(myfile.read(),  dtype='uint32', ).reshape(385, 775)
	        with myzip.open('Data0/MesurementConditions0.xml') as myfile:
	            header = myfile.read().decode()
	    return im, header
	def save_tif(fname, im):
	    im = Image.fromarray(im)
	    im.save(fname)
    ```

=== "Path"
	```python
    folder = Path('/Users/zihanzhang/Library/CloudStorage/OneDrive-UCB-O365')
    ```

Convert all Rasx files in the folder:
=== "Code"
	```python
    for f in glob(str(folder)+'/*.rasx'):
	    print(f)
	    f = Path(f)
	    im, header = open_rasx(f)
	    #inverse 1st axis
	    im = im[::-1,:]
	    im=np.log(im)
	    plt.imshow(im)
	    plt.savefig(f.with_suffix('.jpg'), dpi=360)
	    plt.show()
	    save_tif(f.with_suffix('.tiff'), im)
    ```

<figure>
  <img src="../assets/image.png" width=500px;>
  <figcaption>Converted image</figcaption>
</figure>