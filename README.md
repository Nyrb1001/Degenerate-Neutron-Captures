# Degenerate Neutron Capture Reaction Rates

## Overview
This library contains a set of tabulated and approximated degenerate $(n,\gamma)$ reaction rates. All reaction cross sections are calculated using TALYS, assuming the target nucleus is in its ground state.

## Tabulated Values

The folder "Tabulated" contains numerically calculated reaction rates for a set of nuclei that are involved in mass ranges up to a a NS-BH merger event. We've provide two sets of data; one which contains the Fermi-Dirac based reaction rate, 
and another that contains the ratio between the Fermi-Dirac rate and classical Maxwell-Boltzmann rate. The benefit of using the ratio is that one could adapt these rates to an arbitrary library (ie, Reaclib). 
However, the rates are sensive to the  nuclear physics input that determines the cross section and thus one should choose a library that best matches your chosen system. We've included a sample TALYS input within the respective libraries folder.
At this moment, we have one set of calculation based on nuclear input similar to the BRUSLIB defaults. 

The folder structure is <code>/Tabulated/LIBRARY/raw_fermi/zzzXx/AAXx/AAXx_fermi.dat </code> for the rates
and
<code>/Tabulated/LIBRARY/ratio/zzzXx/AAXx/AAXx_ratio.dat </code> for the ratios.

For a given nucleus, the each rate/ratio file contains 29 non-uniformally spaced temperatures between $1\times10^-4$ GK and $10$GK, and 90 uniformally spaced chemical potential values between 0 MeV and 25 MeV. There are three columns in each file, the first being temperature $[GK]$, the second is chemical potential $[MeV]$ and the last is either the rate $[cm^3 mol^{-1} s^{-1}]$ or the ratio depending on which subdirectory you are in. We've restricted the temperature to reduce the impact of thermally excited cross section states.

## Functional approximation to degenerate reaction rates
    
Our library also includes a set of fit parameters that can let one approximate degenerate capture rates as a function call, based entirely on the nuclear cross section.
Our approximation assumes that given a nucleus-neutron system with centre-of-mass energy $E$, the neutron capture cross section $\sigma(E)$ can be approximated as:
    
$$
    \sigma(E)=\sigma_a E^\nu+\sum_{i=1}^N \frac{\sigma_i}{\pi} \frac{\frac{1}{2} \Omega_i}{\left(E-E_i\right)^2+\left(\frac{1}{2} \Omega_i\right)^2}
$$
    
where $\sigma_a [mb], \nu, \sigma_i [mb], \Omega_i [MeV]$, and $E_i [MeV]$ are all fit to a numerical cross section output from TALYS. 
Then, assuming that the temperature $T$ is much lower than the neutron chemical potential $\mu_n$, we may employ the Sommerfeld expansion to arrive at an equation for the reaction rate:

````math
\begin{align}
\langle\sigma v\rangle_{F D}= & \sqrt{\frac{8}{\pi m_n T}} \frac{1}{\mathcal{F}_{1 / 2}(y)}\left[\sigma_a T^{\nu+1} \Gamma(\nu+2) \mathcal{F}_{\nu+1}(y)+\sum_{i=1}^N \frac{\sigma_i}{\pi}\left[\frac{\omega_i}{2} \ln \left(\frac{\left(x_i-y\right)^2+\omega_i^2}{x_i^2+\omega_i^2}\right)\right.\right. \\
& \left.\left.x_i\left(\arctan \left(\frac{x_i}{\omega_i}\right)-\arctan \left(\frac{x_i-y}{\omega_i}\right)\right)+\frac{\pi^2}{6} \frac{\omega_i\left(\omega_i^2-y^2+x_i^2\right)}{\left(\omega_i^2+\left(y-x_i\right)^2\right)^2}\right]\right]
\end{align}
````


where $\mathcal{F_v}$ is the Fermi-Dirac integral, $m_n$ is the neutron mass, and $\Gamma(\nu)$ is the Euler-gamma function. The parameters are written in reduced units i.e  $x = \frac{E}{T}$,  $y = \frac{\mu_n}{T}$,  $x_i = \frac{E_i}{T}$, $\omega_i = \frac{\Omega_i}{2T}$ with $T$ and $\mu_n$ in MeV. We've chosen the number of peaks N to be, at most, two. In cases where N = 1 or N = 0 provides a better fit, the data tables will have the sigma_N parameter equal to zero. The above function is only tested for temperatures below 0.3 MeV, and for chemical potenentials below 10 MeV.


Note the above equations are only valid for exothermic reactions (ie, a reaction threshold of 0). For endothermic reactions, we use the same fit proposed by Shternin. et al. given by: 

$$
    \sigma(E)=\sigma_a(E-E_0)^\nu
$$

where $E_0$ is the threshold energy for a reaction. Please refer to [Shternin et. al (2012)](https://arxiv.org/pdf/1207.6064.pdf)  for more information on how to implement degeneracy corrections in this situation.

The file "fits.dat" contains 11 columns, formatted as (%i %i %1.4E*9). The first two columns contain the neutron number and proton number for the targte nucleus. The remaining 8 columns contain the paramaters: 
$\sigma_a [mb], \nu, E_0 [MeV], \sigma_1 [mb], E_1, \Omega_1 [MeV], \sigma_2 [mb], E_2 [MeV]$, and $\Omega_2 [MeV]$.

