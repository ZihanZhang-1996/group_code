The contrast of RSoXS is strongly dependent on the dispersion ($\delta$) and the absorption ($\beta$) components of the refractive index (n) as represented in the equations below. The refractive index [$n(E)$] at a photon energy $E$ can be calculated using equation:

\begin{equation}
\begin{aligned}
     n(E)=&1-\delta(E)+i\beta(E)\\
     =&1-\frac{r_0}{2\pi}\lambda^2\sum_j N_j[f_{1j}(E)+if_{2j}(E)]
\end{aligned}
\label{eq:contrast1}
\end{equation}


The contrast function ($C$) can be estimated from equation:
\begin{equation}
    C\propto\{(\Delta n)^2\}\propto\{(\Delta\delta)^2+(\Delta\beta)^2\}
\label{eq:contrast2}
\end{equation}
Where $r_0$ is the classical electron radius, $\lambda$ is the wavelength of incident X-rays, $N_M$ the number density of an atom ($M$), $f_1$ and $f_2$ are the real and imaginary parts of the complex atomic scattering factor, and the summation is performed for all atoms ($M$).

Kramers–Kronig relations:
\begin{equation}
\chi_1(\omega)=\frac{1}{\pi} \mathcal{P} \int_{-\infty}^{\infty} \frac{\omega^{\prime} \chi_2\left(\omega^{\prime}\right)}{\omega^{\prime 2}-\omega^2} d \omega^{\prime}+\frac{\omega}{\pi} \mathcal{P} \int_{-\infty}^{\infty} \frac{\chi_2\left(\omega^{\prime}\right)}{\omega^{\prime 2}-\omega^2} d \omega^{\prime}
\end{equation}

\begin{equation}
\chi_2(\omega)=-\frac{2}{\pi} \mathcal{P} \int_0^{\infty} \frac{\omega \chi_1\left(\omega^{\prime}\right)}{\omega^{\prime 2}-\omega^2} d \omega^{\prime}=-\frac{2 \omega}{\pi} \mathcal{P} \int_0^{\infty} \frac{\chi_1\left(\omega^{\prime}\right)}{\omega^{\prime 2}-\omega^2} d \omega^{\prime}
\end{equation}