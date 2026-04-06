---
layout: post
title: Richefeu 2006 — stress transmission in wet granular materials
date: 2026-04-06 09:30:00 +0000
description: Research note and source text on stress transmission, capillary bonding, and particle-pressure partitioning in wet granular materials.
img: post-example.jpg
tags: [dem, wet-granular, richefeu2006, research]
---
V. Richefeua, F. Radja¨ı, and M. S. El Youssoufi 

LMGC, UMR CNRS 5508, Cc. 048, Universit´e Montpellier 2, Place Eug`ene Bataillon, 34095 Montpellier Cedex 5, France 

Received: date / Revised version: date 

Abstract. We analyze stress transmission in wet granular media in the pendular state by means of threedimensional molecular dynamics simulations. We show that the tensile action of capillary bonds induces a self-stressed particle network organized in two percolating “phases” of positive and negative particle pressures. Various statistical descriptors of the microstructure and bond force network are used to characterize this partition. Two basic properties emerge: 1) The highest particle pressure is located in the bulk of each phase; 2) The lowest pressure level occurs at the interface between the two phases, involving also the largest connectivity of the particles via tensile and compressive bonds. When a confining pressure is applied, the number of tensile bonds falls off and the negative phase breaks into aggregates and isolated sites. 

PACS. 45.70.-n Granular Systems – 83.80.Fg Granular solids 

# 1 Introduction

The particle-scale origins of the strength and flow properties of dry granular materials has been a subject of intensive research since several decades. It is now generally admitted that the scale-up of particle interactions to the macroscopic scale is more subtle than initially expected because of mediation by a disordered microstructure with a rich statistical behavior [1,2]. Considerable work has thus been devoted to characterize the microstructure and its manifestations in the form of highly inhomogeneous distributions of interparticle forces and fluctuating particle velocities [3,4,5,6,7,8,9,10,11,12,13]. The bimodal character of the force network [14], exponential probability functions of strong forces (force chains) [15,16,8,17], and clustering of dissipative contacts are recent examples of this non trivial phenomenology [18]. Insightful analogies have also emerged with other fields such as jamming transition in colloidal matter [19,20,21,22], slow relaxation in glassy materials [23], and fluid turbulence [12]. 

Most of our present knowledge on the subject excludes, however, cohesive bonding between particles. Although one expects strong similarities due to the common granular microstructure, the presence of cohesion brings about new mechanisms that tend to transform the nature of the problem. At the macroscopic scale, the shear strength needs to be described in terms of the Coulomb cohesion in addition to the internal angle friction [24]. On the other hand, the interplay between cohesive bonds, friction and rotation frustration of the particles leads to novel features such as particle aggregation that control static and dy-

namic properties of the material [25,26]. A well-known example is the wet sand where small amounts of water affect significantly the bulk behavior [27,28,29]. The phenomena arising from cohesion are of particular interest to the processing (compaction, granulation. . . ) of fine powders [30,31]. It seems thus that a systematic investigation of the microstructure in cohesive granular media should open new scopes for modeling granular materials. 

In this paper, we analyze the force network in wet granular assemblies of spherical particles. We are interested in a basic question: how cohesive grains keep together to form a self-sustained structure in the absence of confinement (no container and no confining stresses)? The packing can reach an equilibrium state as a result of attraction forces and elastic repulsion between particles without or with self-stressed structures. While the particles are balanced in both cases, the attraction force at each contact is exactly balanced by an elastic repulsion force in the first case. In contrast, in the second case all contacts are not in their equilibrium state due to steric hinderance between particles. Hence, a network of tensile and compressive forces is formed inside the packing. These selfequilibrated forces can be induced through various loading histories such as consolidation [32,33] or differential particle swelling [34]. In wet granular media in the pendular state, the self-stresses appear naturally due to the tensile action of capillary bonds bridging the gaps between neighboring particles within a debonding distance. We focus in this paper on the structure of these self-stresses induced by capillary bonds. 

We use 3D molecular dynamics method in which capillary attraction between spherical particles is implemented 

as a force law expressing the capillary force as a function of the distance, water volume, and particle diameters. The total water volume is distributed homogeneously between particles. The packing is analyzed at equilibrium under zero confining pressure. The main goal of our analysis is to characterize the organization of particle pressures which take positive or negative values according to their positions in the network of self-equilibrated bond forces. We will see that this organization involves a genuin partition of the particles in two phases of negative and positive pressures. In the following, we first describe the simulation method and our model of capillary cohesion. In Sections 3 and 4, we study in detail the probability density functions of the forces and the connectivity of particles via tensile and compressive bonds. Then, in Sections 5 and 6 we introduce particle stresses and we analyze their distribution and correlation with the connectivity of the particles. Section 7 is devoted to the influence of external pressure. We conclude with a discussion about the main findings of this work. 

# 2 Numerical method

We used the molecular dynamics (MD) method with a velocity Verlet integration scheme [35,36]. The force laws involve normal repulsion, capillary cohesion, Coulomb friction, and normal damping. The normal force has three different sources, 

$$
f _ {n} = f _ {n} ^ {e} + f _ {n} ^ {d} + f _ {n} ^ {c}. \tag {1}
$$

The first term is the repulsive contact force depending linearly on the normal distance $\delta _ { n }$ between the particles (Fig 1(a)): 

$$
f _ {n} ^ {e} = \left\{ \begin{array}{l l} - k _ {n} \delta_ {n} & \text {f o r} \quad \delta_ {n} <   0 \\ 0 & \text {f o r} \quad \delta_ {n} \geq 0 \end{array} , \right. \tag {2}
$$

where $k _ { n }$ is the normal stiffness. The second term represents a viscous damping force depending on the normal velocity $\dot { \delta } _ { n }$ : 

$$
f _ {n} ^ {d} = \left\{ \begin{array}{l l} 2 \alpha_ {n} \sqrt {m k _ {n}} \dot {\delta} _ {n} & \text {f o r} \quad \delta_ {n} <   0 \\ 0 & \text {f o r} \quad \delta_ {n} \geq 0 \end{array} , \right. \tag {3}
$$

where $m = m _ { i } m _ { j } / ( m _ { i } + m _ { j } )$ is the reduced mass of the particles $_ i$ and $j$ , $\alpha _ { n }$ is a damping rate varying in the range [0, 1[ and that accounts for the rate of normal dissipation or the restitution coefficient between particles [37]. 

The last term in Eq. 1 is the capillary force depending on the liquid bond parameters, namely the gap $\delta _ { n }$ , the liquid bond volume $V _ { b }$ , the liquid surface tension $\gamma _ { s }$ , and the particle-liquid-gas contact angle $\theta$ . The capillary force can be obtained by integrating the Laplace-Young equation [38,39,40,41]. However, for molecular dynamics simulations, we need an explicit expression of $f _ { n } ^ { c }$ as a function of the liquid bond parameters. We propose the following simple form: 

$$
f _ {n} ^ {c} = \left\{ \begin{array}{l l} - \kappa R & \text {f o r} \delta_ {n} <   0 \\ - \kappa R e ^ {- \delta_ {n} / \lambda} & \text {f o r} 0 \leq \delta_ {n} \leq \delta_ {n} ^ {\max } \\ 0 & \text {f o r} \delta_ {n} > \delta_ {n} ^ {\max } \end{array} . \right. \tag {4}
$$

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/d6f12e0e03d80fca82316851b295df67a4cf10aa857cac75f92e0c5474fe5286.jpg)


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/3e1ede4604cf10ea3cd48323565266ada18cec2dc00ae76ccb87203730a964cf.jpg)


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/4d3eefe206c6cf7aeab95f6d7c79a2d6a0c2daa941faee19ad4cf9117913efbf.jpg)



Fig. 1. (a) Geometry of a capillary bridge; (b) Capillary force $f _ { n } ^ { c }$ as a function of the gap $\delta _ { n }$ between two particles for different values of the liquid volume $V _ { b }$ and size ratio $r$ according to the model proposed in this paper (solid lines), and from direct integration of the Laplace-Young equation (open circles); (c) Scaled plot of the capillary force as a function of gap from the direct data shown in (b).


where ${ R } = \sqrt { { R } _ { i } { R } _ { j } }$ is the geometrical mean of particle radii, and $\lambda$ is a length scale to be discussed below. The prefactor $\kappa$ is given by [42,43,44] 

$$
\kappa = 2 \pi \gamma_ {s} \cos \theta , \tag {5}
$$

and $\delta _ { n } ^ { m a x }$ is the debonding distance given by [45] 

$$
\delta_ {n} ^ {\text {m a x}} = \left(1 + \frac {\theta}{2}\right) V _ {b} ^ {1 / 3}. \tag {6}
$$

The capillary bridge is stable as long as $\delta _ { n } < \delta _ { n } ^ { m a x } .$ . In the simulations, the bridge is removed as soon as the debonding distance is reached, and the liquid is redistributed among the contacts belonging to the same particle in proportion to grain sizes [29]. At contact ( $\delta _ { n } = 0$ ), $\kappa R$ corresponds to the largest attraction force between two particles. In the simulations presented in this paper, we assume that particles are perfectly wettable, i.e. $\theta = 0$ . This is a good approximation for water and glass beads. 

The length $\lambda$ governs the exponential falloff of the capillary force in Eq. 4. It should depend on the liquid volume $V _ { b }$ , a mean radius $R ^ { \prime }$ , and the ratio $r { = } \operatorname* { m a x } \{ R _ { i } / R _ { j } ; R _ { j } / R _ { i } \}$ . A reasonable choice is 

$$
\lambda = c h (r) \left(\frac {V _ {b}}{R ^ {\prime}}\right) ^ {1 / 2}, \tag {7}
$$

where $c$ is a constant prefactor, $h$ is a function depending on the ratio $r$ , and $R ^ { \prime }$ is the harmonic mean ( $R ^ { \prime } =$ $2 R _ { i } R _ { j } / ( R _ { i } + R _ { j } ) )$ . When introduced in Eqs. 7 and 4, this form yields a nice fit for the capillary force obtained from direct integration of the Laplace-Young equation by simply setting $h ( r ) = r ^ { - 1 / 2 }$ and $c \simeq 0 . 9$ . Fig. 1(b) shows the plots of Eq. 4 for different values of the liquid volume $V _ { b }$ and size ratio $r$ together with the corresponding data from direct integration. The fit is excellent for $\delta _ { n } = 0$ (at contact) and for small gaps. 

Figure 1(c) shows the same plots of the direct data as in Fig. 1(b) but in dimensionless units where the forces are normalized by $\kappa R$ and the lengths by $\lambda$ . We see that the data collapse indeed on the same plot, indicating again that the force $\kappa R$ and the expression of $\lambda$ in Eq. 7 characterize correctly the behavior of the capillary bridge for $\theta = 0$ . From the same data, we also checked that the geometric mean $R = \sqrt { R _ { i } R _ { j } }$ introduced in Eq. 4 provides a better fit than the harmonic mean $2 R _ { i } R _ { j } / ( R _ { i } + R _ { j } )$ proposed by Derjaguin for polydisperse particles in the limit of small gaps [46]. 

For the friction force $\mathbf { f } _ { t }$ , we used a viscous-regularized Coulomb law [37,47,48] 

$$
\mathbf {f} _ {t} = - \min  \left\{\gamma_ {t} \| \dot {\boldsymbol {\delta}} _ {t} \|, \mu \left(f _ {n} - f _ {n} ^ {c}\right) \right\} \frac {\dot {\boldsymbol {\delta}} _ {t}}{| | \dot {\boldsymbol {\delta}} _ {t} | |}, \tag {8}
$$

where $\gamma _ { t }$ is a tangential viscosity parameter, $\mu$ is the coefficient of friction, and $\dot { \pmb \delta } _ { t }$ is the sliding velocity. In relaxation to equilibrium, $\dot { \pmb \delta } _ { t }$ declines but never vanishes due to residual kinetic energy. The equilibrium state is practically reached as soon as we have $\gamma _ { t } | | \dot { \delta } _ { t } | | < \mu ( f _ { n } - f _ { n } ^ { c } )$ at all contacts. This means that the friction force is inside the Coulomb cone everywhere in the system. 

The simulations were performed with a packing composed of $N = 8 0 0 0$ spheres with diameters $d = 1$ , 1.5, and 2 mm, in equal numbers. The system was subjected to an isotropic pressure $p _ { m }$ via six rigid walls and no gravity in order to obtain an as homogeneous state as possible. For the same reason, the friction with the walls was set to zero although wall effects can not be fully removed. The parameters used in the simulations are displayed in Table 1. The choice of the water volume has no influence on the value of 


Table 1. Parameters used in the simulations.


<table><tr><td>Parameter</td><td>Symbol</td><td>Value</td><td>Unit</td></tr><tr><td>Normal stiffness</td><td>kn</td><td>1000</td><td>N/m</td></tr><tr><td>Damping rate</td><td>αn</td><td>0.8</td><td>-</td></tr><tr><td>Tangential viscosity</td><td>γt</td><td>1</td><td>Ns/m</td></tr><tr><td>Capillary force prefactor</td><td>κ</td><td>0.4</td><td>N/m</td></tr><tr><td>Gravimetric water content</td><td></td><td>0.007</td><td>-</td></tr><tr><td>Liquid density</td><td></td><td>1000</td><td>kg.m-3</td></tr><tr><td>Particle density</td><td></td><td>2700</td><td>kg.m-3</td></tr><tr><td>Friction coefficient</td><td>μ</td><td>0.4</td><td>-</td></tr><tr><td>Time step</td><td></td><td>10-6</td><td>s</td></tr></table>

the largest capillary force in the pendular state. We also note that the liquid bonds are homogeneously distributed into all gaps within the debonding distance [49]. With a gravimetric water content of 0.007, the coordination number is $\simeq 6$ . Experiments show that the distribution of liquid bonds depends on the preparation protocol involving the water volume, mixing procedure, and waiting times [50,29]. 

# 3 Force distributions

We start out by considering force distributions first in a system subjected to a negligibly small average compressive stress $p _ { m } \simeq 0$ Pa. Fig. 2 displays the probability density function (pdf) of the normal forces both in tensile (negative) and compressive (positive) ranges. We observe a distinct peak centered on $f _ { n } = 0$ and two nearly symmetrical parts decaying exponentially from the center: 

$$
P \propto e ^ {- \alpha \left| f _ {n} \right| / f _ {0}}, \tag {9}
$$

with $\alpha \simeq 4$ within statistical precision for both negative and positive forces, and $f _ { 0 } = \kappa R _ { m a x }$ , where $R _ { m a x }$ is the largest particle radius. It has been observed that in dry granular media the distribution is not purely exponential in the whole range of bond forces [16,8,17,51]. Below the average force, the distribution tends to be uniform or a decreasing power law with a weak exponent [52]. In the present case of wet cohesive materials, the exponential behavior extends to the center of the distribution. It is important to remark that this peak does not represent non-transmitting contacts. It rather corresponds to contacts where capillary attraction is balanced by elastic repulsion, i.e. $k _ { n } \delta _ { n } + f _ { 0 } = 0$ . We will see below that this peak persists under the action of a compressive confining stress, suggesting that its presence reflects a feature of force transmission in wet granular materials. 

The tensile range is cut off at $f _ { n } = - f _ { 0 }$ corresponding to the largest capillary force. Although the confining stress is zero, positive forces as large as $2 f _ { 0 }$ can be found in the system. In contrast to dry granular materials, the pdf shows a peak at $f _ { n } ~ = ~ 0$ which is the average force in the present case. In fact, in an unconfined assembly of dry rigid particles, no self-stresses appear and the forces vanish at all contacts. In our wet material, the presence of 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/7b2ccb698949615b4e7cbd8fbc9d11c6b1b2f23913d6ebcfe71e391ff73c6872.jpg)



Fig. 2. Probability density function of normal forces normalized by the largest capillary force $f _ { 0 }$ for zero confining pressure.


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/f5e7b9674cf7694b028680a309f7d745bf14bb7f1c459e398e5000d00dfa429d.jpg)



Fig. 3. (Color online) A map of tensile (green) and compressive (red) forces in a thin layer cut out in the packing. Line thickness is proportional to the magnitude of the force.


liquid bonds induces tensile and compressive self-stresses although the average force is zero. 

Figure 3 shows the force network in a narrow slice nearly three particle diameters thick. The tensile and compressive forces are represented by segments of different colors joining particle centers. The line thickness is proportional to the force. It is remarkable that tensile and compressive force chains can be observed although the slice is quite narrow. The bond coordination number $z$ (average number of bonds per particle) is $\simeq 6 . 1$ including nearly 2.97 compressive bonds and 3.13 tensile bonds. 

# 4 Connectivity of the bond network

We analyze the connectivity of the particles via capillary bonds by considering the fraction $\rho ( k ^ { + } , k ^ { - } )$ of particles 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/56fc644f7edd2783d4d9de35b633b86736d1c4fa574d7c67ad0df69cb0887470.jpg)



Fig. 4. Grey level map of the connectivity function $\rho ( k ^ { + } , k ^ { - } )$ .


with exactly $k ^ { + }$ compressive bonds and $k ^ { - }$ tensile bonds. This function is displayed in Fig. 4 as grey level map in the parameter space $( k ^ { + } , ~ k ^ { - } )$ for our system. The map is symmetric with respect to the line $k ^ { + } = k ^ { - }$ , reflecting thus the symmetrical roles of compressive and tensile networks in the absence of confining stresses. A peak value of $\rho$ occurs at $k ^ { + } = k ^ { - } = 2$ . Obviously, the condition of particle equilibrium cannot be fulfilled with $k ^ { + } \leq 1$ and $k ^ { - } \leq 1$ and the corresponding levels are zero on the map. The particles with $k ^ { + } = 2$ and $k ^ { - } = 0$ define chains of compressive bonds whereas particles with $k ^ { + } = 0$ and $k ^ { - } = 2$ correspond to chains of tensile bonds. But such “pure” chains are rare. At larger values of $k ^ { + }$ and $k ^ { - }$ the fractions decline basically due to geometrical hinderance between particles. 

An interesting feature of the connectivity map (Fig. 4) is the existence of a population of particles involving no tensile bonds (the row $k ^ { - } = 0$ ) as well as a population of particles involving no compressive bonds (the column $k ^ { + } = 0$ ). Reduced connectivity functions $\rho ^ { + }$ and $\rho ^ { - }$ can be defined by summing up the function $\rho ( k ^ { + } , k ^ { - } )$ along columns and rows, respectively: 

$$
\rho^ {+} (k ^ {+}) = \sum_ {k ^ {-}} \rho (k ^ {+}, k ^ {-}),
$$

$$
\rho^ {-} \left(k ^ {-}\right) = \sum_ {k ^ {+}} \rho \left(k ^ {+}, k ^ {-}\right). \tag {10}
$$

The plots of these functions are shown in Fig. 5. They nearly coincide as expected from the symmetry observed in Fig. 4. We have $\rho ^ { + } ( 0 ) = \rho ^ { - } ( 0 ) \simeq 0 . 0 8$ , corresponding respectively to particles in purely extensional or compressional local environments. A maximum occurs at $k ^ { + } =$ $k ^ { - } = 2$ or 3. 

# 5 Particle stresses

Until now, we focussed on forces and their distributions with regard to the tensile or compressive nature of the bonds. For the description of stress transmission in our 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/28468c81c481ae214611713b3648210236c0722cc455ee7fcbd39dc87cc25635.jpg)



Fig. 5. Reduced connectivity functions at zero confining pressure for tensile and compressive bonds.


system we need, however, to characterize the inhomogeneit at the scale of particles representing the smallest entities for which the equations of motion are resolved in MD simulations. At this scale, a Cauchy stress in the sense of continuous media cannot be defined. But, it can be shown that the so-called internal moment tensor $_ M$ has the same properties as the Cauchy stress tensor $\sigma$ , and its definition extends mathematically to discrete media at all scales down to the particle scale [53,54]. For a particle $i$ subjected to forces $\mathbf { f } ^ { i j }$ from its contact neighbors $j$ at the contact points $\mathbf { r } ^ { i j }$ , the internal moment tensor $M _ { i }$ is given by [53] 

$$
\boldsymbol {M} _ {i} = \sum_ {j \neq i} \mathbf {f} ^ {i j} \otimes \mathbf {r} ^ {i j}, \tag {11}
$$

where $\otimes$ designs a tensorial product. The internal moment tensor is additive and independent of the origin of the coordinate frame. For a collection of particles in a control volume $V$ , the total internal moment $_ M$ is simply the sum of the particle moments: 

$$
M = \sum_ {i \in V} M _ {i}. \tag {12}
$$

This tensor has the dimension of a moment and it becomes homogeneous to a stress when divided by the control volume: 

$$
\boldsymbol {\sigma} = \frac {1}{V} \sum_ {i \in V} M _ {i}. \tag {13}
$$

At large scales (containing a sufficiently large number of particles), the volume is well-defined and the stress tensor is equivalent to the internal moment tensor divided by this volume. However, at the particle scale, while $M _ { i }$ is defined in a unique way, the choice of the volume $V$ is a matter of convenience. It is reasonable to take into account the free volume $V _ { i }$ of each particle $i$ , as the sum of the volume of the particle and part of the surrounding pore space. On average, we have $V _ { i } = ( 1 / 6 ) \pi d _ { i } ^ { 3 } / \nu$ , where $d _ { i }$ is the particle diameter and $\nu$ denotes the packing fraction. With this choice, the particle stress tensor $\pmb { \sigma } _ { i }$ takes the 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/3b01359fea35adc8bf32da8354f9cc56d291eac183256aeddcecc67dc659310e.jpg)



Fig. 6. Probability density function of particle pressures normalized by reference pressure $p _ { 0 }$ (see text) in the unconfined packing.


following form: 

$$
\boldsymbol {\sigma} _ {i} = 6 \frac {\nu}{\pi d _ {i} ^ {3}} \sum_ {j \neq i} \mathbf {f} ^ {i j} \otimes \mathbf {r} ^ {i j}. \tag {14}
$$

Remark that when summing this form over many particles in a control volume as in Eq. 13, each contact $i j$ enters the summation two times, belonging once to particle $_ i$ and once to particle $j$ . Since $\mathbf { f } ^ { i j } = - \mathbf { f } ^ { j i }$ , the contribution of the contact $_ { i j }$ to stress is $\mathbf { f } ^ { i j } \otimes ( \mathbf { r } ^ { i j } - \mathbf { r } ^ { j i } )$ , a writing that involves the center-to-center vector instead of position vectors of the contact points if the origin of coordinates for each particle is chosen coincides with the center of the particle. However, we consider here only the particle stress, and at this scale, according to Eq. 14, only the position vectors of contact points are involved. 

Here, we explore the particle pressures (average particle stresses) $p _ { i } = ( 1 / 3 ) \mathrm { t r } \pmb { \sigma } _ { i }$ . Each particle can take on positive or negative pressures according to the nature of the forces exerted by contact neighbors. The pdf of particle pressures is displayed in Fig. 6. The pressures have been normalized by a reference pressure $p _ { 0 } ~ = ~ f _ { 0 } / \langle d \rangle ^ { 2 }$ . The distribution is symmetric around and peaked on zero pressure, and each part is well fit by an exponential form. Obviously, the exponential shape of particle pressure distributions reflects statistically that of bond forces. In dry granular media, since the normal forces are all of the same sign (compressive) and particle pressure results from the summation of individual bond forces on a particle, the probability is expected to vanish as the pressure goes to zero. In contrast, Fig. 6 shows that the exponential shape of particle pressure distribution extends to the center of the pdf. This can be related to the fact that all normal forces are not of the same sign. On the other hand, the zero pressure corresponds to a state where a particle is balanced under the combined action of tensile and compressive forces. Since such particle states are not marginal here, they might reflect a particular organization of the stresses in the wet packing (see below). 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/d270d90332d807fbb43809f0d80ea15c3036d18c341266bffd3d386271532360.jpg)



Fig. 7. (Color online) The map of partial pressures $p ( k ^ { + } , k ^ { - } )$ .


# 6 Partition of particle pressures

An interesting observation about the connectivity map (Fig. 4) was the presence of particles with only tensile or only compressive bonds. In terms of particle pressures, these populations carry negative or positive pressures, respectively. However, this information is not rich enough as it does not specify the pressure levels in these populations. The question is how the particle pressure is locally correlated with the particle connectivity. For particle $i$ , the connectivity is specified by the number $k _ { i } ^ { + }$ of compressive bonds and the number $k _ { i } ^ { - }$ of tensile bonds. Let us now consider the set $S ( k ^ { + } , k ^ { - } )$ of particles $_ i$ such that $k _ { i } ^ { + } = k ^ { + }$ and $k _ { i } ^ { - } = k ^ { - }$ . The partial pressure carried by this set is the sum of particle pressures in this set divided by the total number of particles: 

$$
p \left(k ^ {+}, k ^ {-}\right) = \frac {1}{N} \sum_ {i \in \mathcal {S} \left(k ^ {+}, k ^ {-}\right)} p _ {i}. \tag {15}
$$

This is obviously an additive quantity so that the average stress $\begin{array} { r } { p _ { m } = \sum _ { ( k ^ { + } , k ^ { - } ) } p ( k ^ { + } , k ^ { - } ) } \end{array}$ . The function $p ( k ^ { + } , k ^ { - } )$ provides detailed information about the way particle pressures are distributed with respect to the bond network. In other words, this function describes the relationship between the pressure sustained by a particle and its first neighbors with which it is bonded. 

Figure 7 shows the map of partial pressures $p ( k ^ { + } , k ^ { - } )$ in the parameter space $( k ^ { + } , k ^ { - } )$ . Interestingly, we observe a bipolar structure of partial pressures which is antisymmetric with respect to the line $k ^ { + } = k ^ { - }$ within statistical precision. The pressures are positive in the range $k ^ { + } > k ^ { - }$ and negative in the range $k ^ { + } < k ^ { - }$ . Hence, the line $k ^ { + } ~ = ~ k ^ { - }$ defines the transition zone between the two parts with $p ( k ^ { + } , k ^ { - } = k ^ { + } ) \simeq 0$ . This line corresponds to the largest population of particles according to the connectivity map (Fig. 4). The pressure extrema are located at ( $k ^ { + } = 4$ , $k ^ { - } = 0$ ) for positive pressures and at ( $k ^ { + } = 0$ , $k ^ { - } = 4$ ) for negative pressures. 

The bipolar structure of the pressure map suggests that the particles of positive and negative pressures define 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/ee3f37fd1dd0f4df97c3cc36f9161de47f2450af9b09f99db74f5e814fb03f46.jpg)



Fig. 8. A representation of the packing with particles of negative (white) and positive (black) pressures.


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/e9ee5fa853746ac2088f649819c383639d586fb60a15876ce115bdb21f5f2f1e.jpg)



Fig. 9. Average numbers of tensile ( $z ^ { - }$ ) and compressive (z+) bonds per particle as well as the partial coordination number $z = z ^ { - } + z ^ { + }$ ) as a function of the particle pressure in the unconfined packing.


two separate phases throughout the system. In this picture, the population of particles along the line $k ^ { + } = k ^ { - }$ corresponds to the particles at the interface between the two phases. This interpretation is backed by Fig. 8 displaying the packing where the two phases are represented in black and white. The particles of either positive or negative pressure percolate throughout the system. The two phases are intimately intermingled with a large interface between them. The particles at the interface belong to the line $k ^ { + } = k ^ { - }$ corresponding to a fraction 0.125 of particles. The morphology of the two phases is approximately filamentary with variable thickness. 

The correlation between the bond connectivity and particle pressures can alternatively be determined by considering the average numbers of tensile and compressive bonds per particle, denoted $z ^ { - }$ and $z ^ { + }$ , as a function of the particle pressure $p$ . In order to evaluate these functions, 

we consider the set $S ( p )$ of particles $i$ with a pressure $p _ { i }$ in the range $[ p , p + \varDelta p ]$ , where $\varDelta p$ is the pressure increment. The partial coordination numbers $z ^ { - }$ and $z ^ { + }$ are defined by 

$$
\begin{array}{l} z ^ {+} (p) = \frac {1}{N (p)} \sum_ {i \in \mathcal {S} (p)} k _ {i} ^ {+}, \\ z ^ {-} (p) = \frac {1}{N (p)} \sum_ {i \in \mathcal {S} (p)} k _ {i} ^ {-}, \tag {16} \\ \end{array}
$$

where $N ( p )$ is the number of particles in the set. These functions are plotted in Fig. 9 in the case $p _ { m } = 0$ . The plot of $z ^ { - } ( p )$ is an approximate mirror image of $z ^ { + } ( p )$ with respect to $p = 0$ . Three zones can be discerned. For $p <$ $- p _ { 0 }$ , we have $z ^ { - } \simeq 5$ and $z ^ { + } \simeq 0 . 5$ . This zone represents mainly the particles belonging to the bulk of the negative phase. For $p > p _ { 0 }$ , we have $z ^ { - } \simeq 0 . 5$ and $z ^ { + } \simeq 4$ . The particles in this zone belong to the bulk of the positive phase. In the range $- p _ { 0 } < p < p _ { 0 }$ , $z ^ { + }$ increases and $z ^ { - }$ declines. The intersection occurs at $p = 0$ where $z ^ { - } =$ $z ^ { + } \simeq 3$ . This zone corresponds to the particles located at the interface between negative and positive phases. 

The above findings underline that stress transmission in wet granular media is non trivial. In particular, they support the partition of the packing into two well-defined phases both in terms of particle connectivities and in terms of particle pressures. The peculiarity of this partition is that the extrema of particle pressures are located in the bulk of each phase (Fig. 4) whereas the maximum of connectivity between particles via tensile and compressive bonds resides at their interface (Fig. 7). Along this interface, not only the bond forces are balanced on each particle, as everywhere in the packing, but the tensile and compressive bonds contribute equally in such a manner that the particle pressures are extremely low. 

# 7 Influence of confining pressure

In the absence of a confining stress, the capillary bonds are at the origin of self-stresses or self-equilibrated forces that we analyzed in preceding sections. Hence, the observed symmetry between tensile and compressive bonds is a consequence of static equilibrium at zero confining stress. The question remains whether the partition of particle pressures, as depicted above, still holds when a wet packing is subjected to (compressive) confining stress. In practice, however, we cannot isolate self-equilibrated forces for each particle from those induced by the external stress (the actual force network being the sum of the two networks). This is because the external pressure drives the packing to a new equilibrium state with modified microstructure. Hence, the self-stresses for $p _ { m } = 0$ , i.e. before rearrangements, do not correspond to the rearranged state for $p _ { m } \neq 0$ . While it can be conjectured that the self-stresses in the presence of a confining pressure will display the same “bipolar” behavior as for $p _ { m } = 0$ , we consider here the force network and particle pressures without distinction between induced and self-equilibrated forces. 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/951f5edf4e3aabb5482b840b2134d7aa57047bdd9e61dc1ed54eda8e1589321a.jpg)



Fig. 10. Probability density function of normal forces normalized by the largest capillary force $f _ { 0 }$ in the confined packing.


We applied an isotropic stress $p _ { m } = 1 0 0$ Pa to the wet packing prepared with $p _ { m } = 0$ . The packing was then allowed to relax to equilibrium under the action of the applied pressure. This level of confinement is high compared to the reference pressure $p _ { 0 }$ ( $p _ { m } / p _ { 0 } \simeq 0 . 5$ ), yet not too high to mask fully the manifestations of capillary cohesion. The same packing was also compressed isotropically for $p _ { m } = 1 0 0$ Pa without capillary cohesion (dry packing). 

The pdf of normal forces is shown in Fig. 10 for both packings. The symmetry of the distribution around $f _ { n } =$ 0 is now broken (compare to Fig. 2). The distributions are roughly exponential for both tensile and compressive forces but the exponents are different. We also note that the exponent for compressive forces is nearly the same as for the dry sample. On the other hand, although the strictly zero forces have been removed from the statistics, a distinct peak centered on zero force is present for the wet sample and absent from the dry sample. If the occurrence of this peak in the unconfined case ( $p _ { m } = 0$ ) is attributed to the interfacial zone, its persistence in the confined case suggests that a negative pressure phase continues to coexist with the positive pressure phase (which is now the dominant phase assisted with the confining stress). This point will be analyzed below in relation to the distribution of particle pressures. 

The coordination numbers for compressive and tensile bonds are 4.85 and 0.85, respectively. This shows that the application of a confining pressure has transformed a fraction of tensile bonds into compressive bonds. The plots of the connectivity functions $\rho ^ { + } ( k ^ { + } )$ and $\rho ^ { - } ( k ^ { - } )$ are displayed in Fig. 11. Each function is normalized to unity as in Fig. 5 for the unconfined packing. The function $\rho ^ { - } ( k ^ { - } )$ decreases with $k ^ { - }$ from a peak at $k ^ { - } = 0$ , showing that nearly half of the particles have no tensile contact at all. The function $\rho ^ { + } ( k ^ { + } )$ has a peak at $k ^ { + } = 4$ and only a small fraction $\simeq 0 . 0 5$ of particles have no compressive bond $\rho ^ { + } ( k ^ { + } = 0 ) \simeq 0 . 0 5 ,$ ). 

Force patterns with one or more tensile bonds correspond to various local configurations where equilibrium of the particles cannot be ensured only by compressive con-

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/856881c20df0e7acc0957d2f46730bdceb20c5bb609aa0627d1b60057b9d2796.jpg)



Fig. 11. Reduced connectivity functions in the confined packing for tensile and compressive bonds.


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/3024bba28d383350d6cc2130bb0c1b427f2478390955435611155d5a70566aba.jpg)


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/ecd1de1fc58a7296c6a9a6d45c742a5162ea8fbe9468e0b8e0dd635796aff609.jpg)



Fig. 12. (Color online) Two examples of local patterns of tensile (green) and compressive (red) forces surrounding particles with negative (green) and positive (red) pressures.


tacts. Typically, a tensile bond between two particles is induced by transverse particles that are forced into the space between the two particles. One example is shown in Fig. 12(a). For this reason, when a packing is subjected to a stress deviator, most tensile bonds occur along the direction of extension [29]. The upshot of tensile bonds when they are located in a purely compressive environment is thus to reinforce the shear strength. We also observe many particles with 2, 3 or 4 tensile bonds. One example is shown in Fig. 12(b) where the tensile actions of several particles creates a “cage” of compressive bonds between their neighboring particles. This kind of patterns may be locally self-equilibrated and thus form aggregates that could move as a rigid body when the packing deforms. 

The pdf of particle pressures is shown for dry and wet samples in Fig. 13. Large positive pressures decay exponentially in both cases. In the dry case, a local maximum is observed at $p \simeq 1 . 5 p _ { 0 }$ as a signature of the confining stress. In this range of pressures, we observe a plateau for the wet sample. In the range of negative pressures, the distribution is no more exponential. This means that the organization of tensile forces does not fulfill the conditions that underly exponential distribution of strong forces in granular media [15]. In particular, the network of tensile forces is no more percolating throughout the packing as in the unconfined case. A map of positive and negative par-

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/ee08979274a7f80d7c260439805aaeff0ab93d6c6ff05c65cc1764efd6c675cc.jpg)



Fig. 13. Probability density function of particle pressures normalized by a reference pressure $p _ { 0 }$ (see text) in the confined packing.


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/5fcc51daabde7af166522d29dbbcb22486c48e9366e6f4afea7a5911129243a8.jpg)



Fig. 14. A representation of a thin layer inside the confined packing with negative (white) and positive (black) particle pressures.


ticle pressures in a thin layer inside the packing is shown in Fig. 14. The positive pressures are dominant and negative pressure particles are mostly isolated or appear in the form of small clusters. Although the negative pressures do not define a bulk phase any more, the peak centered on zero pressure in Fig. 13 can still be considered as a reminiscence of the interface between the two phases. 

This last point appears more clearly in the plots of partial coordination numbers $z ^ { - }$ and $z ^ { + }$ as a function of the particle pressure $p$ in Fig. 15. We again discern three zones as in Fig. 9 for the unconfined packing. The peak $z ^ { + } \simeq 6$ appearing around $p \simeq 2 . 5 p _ { 0 }$ is the effect of the confining pressure. At the same time, the level of $z ^ { - }$ in the range of negative pressures is reduced to $\simeq 3 . 5$ (from $\simeq 5$ in the unconfined packing). The intersection occurs at $p = 0$ with $z ^ { - } = z ^ { + } \simeq 2$ . 

![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/151330ed3bd5123646b92c7bcc3771f8af1940c19229570e08249cd35f987f65.jpg)



Fig. 15. Average numbers of tensile ( $z ^ { - }$ ) and compressive ( $z ^ { + }$ ) bonds per particle as well as the partial coordination number ( $z = z ^ { - } + z ^ { + }$ ) as a function of the particle pressure in the confined packing.


![image](https://cdn-mineru.openxlab.org.cn/result/2026-04-02/ad2fd936-8953-4b8d-bc89-466510495ded/f777c9ebe18eb5db1f748a1074e0490932390863ce66db3dae0bd597cf983e44.jpg)



Fig. 16. A representation of the packing with particle pressures above (white) and below (black) the mean pressure $p _ { m }$ .


We see that many features of stress transmission in the unconfined packing persist when a confining stress is applied. In particular, in both cases, a large class of particles of weak pressure (close to zero of either sign) is present. This class was interpreted in the unconfined case as belonging to the interface between two percolating phases. Obviously, this interface is ill-defined for $p _ { m } = 1 0 0$ Pa where the negative phase appears in the form of either isolated particles or very small aggregates. Nevertheless, our results clearly indicate that tensile bonds and negative pressures play the same role with respect to the equilibrium properties of the particles wherever they are present. 

Obviously, as stated before, the molecular dynamics method cannot be used as such for the analysis of selfstresses in the presence of an external pressure. This is because in molecular dynamics, the resolution of governing equations proceeds from the knowledge of the posi-

tions of only the first neighbors of each particle and not from an explicit construction of the system of equations as in contact dynamics [55,56] An approach for the analysis of self-stresses was presented in Radja¨ı et al. [57] using “singular value decomposition” in the framework of the contact dynamics method. However, a rough estimation of the self-stresses can be obtained by simply subtracting the mean pressure $p _ { m }$ from the particle pressures. Fig. 16 displays a snapshot of the particle pressures where the pressures below and above $p _ { m }$ are distinguished. The apparent clustering of particle pressures is quite comparable to that observed in Fig 8. This indicates that it is very likely that if the self-stresses were isolated from those induced by the external pressure, they would display the same nearly symmetric “bipolar” structure as that observed at zero confining pressure. 

# 8 Conclusions

We analyzed the statistical properties of the network of self-equilibrated forces in a wet granular material by means of 3D molecular dynamics simulations. Various descriptors of the microstructure and bond force network were shown to carry the signature of an ingenious organization of particle pressures in two distinct clusters of respectively positive and negative pressures, each percolating throughout the packing. This partition is not meant as a formal distinction between negative and positive pressures but rather related to the way the two populations share the space and connect to the bond network. This “phase separation” is characterized by two interesting properties. First, the highest pressures occur in the heart of each phase, whereas the lowest pressure levels constitute the interface between the two phases. Secondly, this interface bears the largest coordination numbers via tensile and compressive bonds. In the presence of confining stresses, the same phenomenology can be expected for self-stresses although these can not be directly accessed from the force data. 

It is important to remark that the homogeneity of selfstresses in our simulations results from the homogeneous distribution of capillary bonds. Obviously, the self-stresses can be more or less localized in different portions of the material or involve gradients if the liquid bonds are distributed in a nonuniform manner in the bulk. In the same way, although the boundary conditions are isotropic, the self-stresses may be locked in an anisotropic state as a result of friction and geometrical hinderance effects. In particular, if the capillary bonds are placed only in the gaps between particle pairs with privileged orientation, the selfstresses might organize into an anisotropic scheme. Such anisotropic distributions of liquid bonds may also appear as a result of handling the material. The choice of a homogeneous distribution of liquid bonds in our simulations was motivated by the requirement of representative statistics for the analysis of the system. However, it would be interesting to study the influence of the liquid distribution on the patterns of self-stresses. 

The partition of self-stresses implies that the overall equilibrium of the packing is ensured by mesoscopic structures involving length scales larger than the particle size. These length scales are likely to control the size of aggregates during flow or other packing properties of cohesive granular materials. On the other hand, the effect of selfstresses on the tensile strength or Coulomb cohesion of wet granular materials is of interest to wet processing of grains in chemical engineering and merits to be studied along these lines. In the same way, the influence of solid fraction is an important aspect with evident application to compaction and consolidation of cohesive packs. 

This work was accomplished within the “granular solids” group of the LMGC. We thank F. Souli´e for his help with the validation of the capillary law used in our simulations. The data were treated by means of the 3D software mgpost (www.lmgc.univmontp2.fr/ $\sim$ richefeu). 

# References



1. H. J. Herrmann, J.-P. Hovi, and S. Luding, Physics of dry granular media - NATO ASI Series E 350 (Kluwer Academic Publishers, Dordrecht, 1998) 





2. S. Roux and F. Radja¨ı, in Mechanics for a New Millennium, edited by H. Aref and J.W. Philips (Kluwer Acad. Pub., Netherlands, 2001), p. 181 





3. M. Oda, Soils and foundations 12, 17 (1972) 





4. J. Christoffersen, M. M. Mehrabadi, and S. Nemat-Nasser, J. Appl. Mech. 48, 339 (1981) 





5. L. Rothenburg and R. J. Bathurst, Geotechnique 39, 601 (1989) 





6. H. M. Jaeger and S. R. Nagel, Reviews of Modern Physics 68, 1259 (1996) 





7. F. Calvetti, G. Combe, and J. Lanier, Mech. Coh. Frict. Materials 2, 121 (1997) 





8. D. M. Mueth, H. M. Jaeger, and S. R. Nagel, Phys. Rev. E 57, 3164 (1998) 





9. M. R. Kuhn, Mechanics of Materials 31, 407 (1999) 





10. J.-N. Roux, Phys. Rev. E 61, 6802 (2000) 





11. H. Troadec, F. Radja¨ı, S. Roux, and J.-C. Charmet, Phys. Rev. E 66, 041305 (2002) 





12. F. Radja¨ı and S. Roux, Phys. Rev. Lett. 89, 064302 (2002) 





13. R. Y. Yang, R. P. Zou, and A. B. Yu, Phys. Rev. E 65, 041302-1 (2002) 





14. F. Radja¨ı, D. E. Wolf, M. Jean, and J. J. Moreau, Phys. Rev. Lett. 80, 61 (1998) 





15. S. N. Coppersmith, C. Liu, S. Majumdar, O. Narayan, and T. A. Witten, Phys. Rev. E 53, 4673 (1996) 





16. F. Radja¨ı, M. Jean, J. J. Moreau, and S. Roux, Phys. Rev. Lett. 77, 274 (1996) 





17. T. S. Majmudar and R. P. Behringer, Nature (London) 435, 1079 (2005) 





18. L. Staron, J.-P. Vilotte, and F. Radja¨ı, Phys. Rev. Lett. 89, 204302 (2002) 





19. M. E. Cates, J. P. Wittmer, J.-P. Bouchaud, and P. Claudin, Phys. Rev. Lett. 81, 1841 (1998) 





20. A. J. Liu and S. R. Nagel, Jamming and Rheology (Taylor & Francis, London, 2001) 





21. O. Dauchot and G. Marty, Phys. Rev. Lett. 95, 265701 (2005) 





22. E. I. Corwin, H. M. Jaeger, and S. R. Nagel, Nature 435, 1075 (2005) 





23. J. B. Knight, C. G. Fandrich, C. N. Lau, H. M. Jaeger, and S. R. Nagel, Phys. Rev. E 51, 3957 (1995) 





24. D. M. Wood, Soil behaviour and critical state soil mechanics (Cambridge University Press, Cambridge, England, 1990) 





25. A. Castellanos, J. M. Valverde, and M. A. S. Quintanilla, Phys. Rev. E 64, 041304 (2001) 





26. N. Fillot, I. Iordanoff, Y. Berthier, Journal of Tribology 126, 606 (2006) 





27. D. J. Hornbaker, R. Albert, I. Albert, A.-L. Barabasi, and P. Schiffer, Nature (London) 387, 765 (1997) 





28. T. C. Halsey and A. J. Levine, Phys. Rev. Lett. 80, 3141 (1998) 





29. V. Richefeu, M. S. El Youssoufi, and F. Radja¨ı, Phys. Rev. E 73, 051304 (2006) 





30. G. K. Reynolds, C. F. W. Sanders, A. D. Salman, and M. J. Hounslow, in Granular Materials: fundamental and applications, edited by S. J. Antony, W. Hoyle, and Y. Ding (RS.C, Cambridge, 2004), p. 296 





31. A. Castellanos, Advances in Physics 54, 263 (2005) 





32. I. Preechawuttipong, Ph.D. Thesis, Universit´e Montpellier 2, 2002 





33. F. Radja¨ı, I. Preechawuttipong, and R. Peyroux, in Continuous and discontinuous modelling of cohesive-frictional materials, edited by P. A. Vermeer, S. Diebels, W. Ehlers, H. J. Hermann, S. Luding, and E. Ramm (Springer, Berlin, 2001), p. 149 





34. M. S. El Youssoufi, J.-Y. Delenne, and F. Radjai, Phys. Rev. E 71, 051307 (2005). 





35. P. A. Cundall and O. D. L. Strack, Geotechnique 29, 47 (1979) 





36. M. P. Allen and D. J. Tildesley, Computer Simulation of Liquids (Oxford University Press, Oxford, 1987) 





37. J. Sch¨afer, S. Dippel, and D. E. Wolf, J. Phys. I France 6, 5 (1996) 





38. M. A. Erle, D. C. Dyson, and N. R. Morrow, AIChE Journal 17, 115 (1971) 





39. G. Lian, C. Thornton, and M. J. Adams, J. Colloid Int. Sci. 161, 138 (1993) 





40. T. Mikami, H. Kamiya, and M. Horio, Chemical Engineering Science 53, 1927 (1998) 





41. F. Souli´e, F. Cherblanc, M. S. El Youssoufi, and C. Saix, Int. J. Numer. Analyt. Meth. Geomech. 30, 213 (2006) 





42. C. Willett, M. Adans, S. Johnson, and J. Seville, Langmuir 16, 9396 (2000) 





43. L. Bocquet, E. Clarlaix, F. Restagno, C. R. Physique 3, 207 (2002) 





44. S. Herminghaus, Advances in Physics 54, 221 (2005) 





45. G. Lian, C. Thornton, and M. J. Adams, Chem. Eng. Sci. 53, 3381 (2004) 





46. J. N. Israelachvili, Intermolecular & surface forces (Academic Press, London, 1993) 





47. S. Dippel, G. G. Batrouni, and D. E. Wolf, Phys. Rev. E 56, 3645-3656 (1997) 





48. S. Luding, in Physics of dry granular media - NATO ASI Series E350, edited by H. J. Herrmann, J.-P. Hovi, and S. Luding (Kluwer Academic Publishers, Dordrecht, 1998), p. 285 





49. V. Richefeu, Ph.D. thesis, Universit´e Montpellier 2, 2005 





50. Z. Fournier, D. Gerimichalos, S. Herminghaus, M. M. Kohonen, F. Mugele, M. Scheel, M. Schulz, B. Schulz, C. Schier, R. Seemann, and A. Shudelny, Applied Physics: Condensed Matter 17, 477 (2005) 





51. M. van Hecke, Nature 435, 1041 (2005) 





52. F. Radja¨ı, S. Roux, and J. J. Moreau, Chaos 9, 544 (1999) 





53. J. J. Moreau, in Friction, Arching, Contact Dynamics edited by D. E. Wolf and P. Grassberger (World Scientific, Singapore, 1997), p. 233 





54. L. Staron, F. Radja¨ı, and J.-P. Vilotte, Eur. Phys. J. E 18, 311 (2005) 





55. J. J. Moreau, Eur. J. Mech. A Solids (Suppl.) 13, 93 (1994) 





56. M. Jean, Comput. Methods Appl. Mech. Engrg. 177, 235 (1999) 





57. F. Radja¨ı, L. Brendel, and S. Roux, Phys. Rev. E 54, 861 (1996)
