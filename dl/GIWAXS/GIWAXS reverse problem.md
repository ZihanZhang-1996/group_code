# GIWAXS reverse problem
Zihan Zhang (zihan.zhang-1@colorado.edu)

Reversed GIWAXS problem: derive the lattice parameters from the given GIWAXS pattern. 

A brute-force search program 

## Blue print of the program
- [ ] GIWAXS reverse problem
    - [x] Input
        - [x] Given peak positions (In the first quadrant only)
        - [x] A guess of $\vec{G}$
    - [x] DOF analysis
        - [x] Original 9 degrees of freedom 
        - [x] [$a^*_x$,$a^*_y$,$a^*_z$] [$b^*_x$,$b^*_y$,$b^*_z$] [$c^*_x$,$c^*_y$,$c^*_z$]
        - [x] Force $\vec{a}^*$ to be in [$a_x^*$,0,$a_z^*$] direction (kills 1 degrees of freedom (DOF=8))
        - [x] Find the peak that is nearest to [0,0,0] (gives $a_x^*$ and $a_z^*$, (DOF=6))
        - [ ] Remove peaks $\vec{a}^*$, $2\vec{a}^*$, $3\vec{a}^*$, ...
        - [ ] Find the next nearest peak (gives [$b_{xy}^*$,$b_z^*$] and kills 2 DOF (DOF=4))
    - [x] Algorithm
        - [x] Monte Carlo (good for high dimensions), Markov chain (Brute-force search. This is a ill-conditioned problem with a lot of local minimums.)
        - [x] Cost function: add up the distance of each given peak to the nearest simulated peak. add up the distance of each simulated peak to the nearest given peak. (make sure every peak find their friend)
    - [ ] Optimization
		- [ ] use gradient decent after MT to boost the convergence
		- [ ] convert the output lattice parameter to a high symmetry format



Import libraries

=== "Code"

    ```python
    import numpy as np
	import scipy.ndimage as ndimage
	import diffraction as diff
	import matplotlib.pyplot as plt
	import matplotlib.cm as cm
	import time as time
	from matplotlib.widgets import Button
	import scipy.io
	from matplotlib.widgets import RangeSlider
	import matplotlib.patheffects as path_effects
    ```
Input

=== "Code"

    ```python
	qxy_exp=[0,4]
	qz_exp=[0,4]
	Bragg_peaks_exp=[
		[0,1],
		[0,2],
		[0,3],
		[2,0],
		[2,1],
		[2,2],
		[2,3],
		[2.818,0],
		[2.818,1],
		[2.818,2],
		[2.818,3],
		[1.414,0.5],
		[1.414,1.5],
		[1.414,2.5],
		[3.16,0.5],
		[3.16,1.5],
		[3.16,2.5],
		[4.24,0.5],
		[4.24,1.5],
		[4.24,2.5],
		[4,0],
		[4,1],
		[4,2],
		[4,3]
	]
	hkl=5
    ```

=== "Functions"
	```python
	def make_cif(Bragg_peaks):
		# b1=[np.random.uniform(-ub, ub),0,np.random.uniform(-ub, ub)]
		b2x=np.random.uniform(-1.5, 1.5)
		b3x=np.random.uniform(-5, 5)
		b3y=np.random.uniform(-5, 5)
		b3z=np.random.uniform(-5, 5)
		b1=[0,0,1]
		b2=[b2x,np.sqrt(2.25-b2x*b2x),0.5]
		b3=[b3x,b3y,b3z]
		return b1,b2,b3

	def find_c(array):
		x_values_with_leading_zero = [pair[1] for pair in array if pair[0] == 0]
		if not x_values_with_leading_zero:
			return None
		return min(x_values_with_leading_zero)

	def closest_distance(array, point):
		# Convert the input to numpy arrays if they aren't already
		array = np.array(array)
		point = np.array(point)

		# Compute squared distances
		squared_distances = np.sum((array - point)**2, axis=1)
		
		# Find the minimum squared distance and take its square root to get the actual distance
		min_distance = np.sqrt(np.min(squared_distances))
		
		return min_distance

	def Bragg_peaks(b1,b2,b3,hkl_dimension):
		# grid for Miller index
		i=np.linspace(-hkl_dimension,hkl_dimension,2*hkl_dimension+1)
		H,K,L=np.meshgrid(i,i,i)
		
		# The position of Bragg peaks in reciprocal space
		G1=H*b1[0]+K*b2[0]+L*b3[0]
		G2=H*b1[1]+K*b2[1]+L*b3[1]
		G3=H*b1[2]+K*b2[2]+L*b3[2]

		q2=G1*G1+G2*G2+G3*G3
		F=1
		Bpeaks=np.concatenate((G1,G2,G3), axis=0)
		# return Bpeaks,pow(G1*G1+G2*G2,0.5),pow(G3*G3,0.5)
		return pow(G1*G1+G2*G2,0.5),pow(G3*G3,0.5)

	def FT(a1,a2,a3):
		# Lattice parameters M matrix in cartesian coordinate(angstrom)
		M=[a1,a2,a3]
		M=np.asarray(M)
		
		# New lattice parameter
		aa1=M[0,:]
		aa2=M[1,:]
		aa3=M[2,:]

		# reciprocal lattice
		volume=np.matmul(aa3,np.cross(aa1,aa2))
		b1=2*np.pi*np.cross(aa2,aa3)/volume
		b2=2*np.pi*np.cross(aa3,aa1)/volume
		b3=2*np.pi*np.cross(aa1,aa2)/volume
		print(b1,b2,b3)
	```
Main 
=== "Code"
	```python
	total_distance0=99999999
	for i in range(500000):
		b1,b2,b3=make_cif(Bragg_peaks_exp)
		qxy,qz=Bragg_peaks(b1,b2,b3,hkl)
		qxy=qxy.ravel()
		qz=qz.ravel()
		combined = np.column_stack((qxy, qz))
		j=0
		total_distance=0
		for ii in Bragg_peaks_exp:
			total_distance=total_distance+closest_distance(combined,Bragg_peaks_exp[j])
			j=j+1
		j1=0
		for ii in combined:
			if combined[j1,0]<qxy_exp[1]:
				if combined[j1,1]<qz_exp[1]:
					total_distance=total_distance+closest_distance(Bragg_peaks_exp,combined[j1])
			j1=j1+1
		if total_distance<total_distance0:
			total_distance0=total_distance
			FT(b1,b2,b3)
	```


Performance:

The result from a test on PC. (~ 25 miniutes cpu time)

=== "Lattice parameter"
	```python
	[ 1.09233506 -2.95321592  6.28318531] 
	[-2.18850426  5.89804628  0.        ] 
	[-1.84097256 -4.0263532   0.        ]

	## rotate this lattice and re-define lattice parameters to make it symmetric, you'll get

	a = [-pi, pi, 0]
	b = [-pi,-pi, 0]
	c = [pi, 0, 2pi]
	```

<figure>
  <img src="../assets/GIWAXS_reverse2.png" width=600px;>
  <figcaption>Fig 1. (a) The reverse problem (b) Simulation using the lattice parameter found by the program.</figcaption>
</figure>



