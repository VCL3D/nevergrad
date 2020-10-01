---
layout: default
title: Objective Function
nav_order: 3
description: "Objective Function Details"
---

# Objective Function

The **objective function** of each experiment is a weighted linear combination of five individual error terms:
<!--
$$
E(\mathbf{p})= \lambda_J E_J(\mathbf{p}) + \lambda_D E_D(\mathbf{p}) + \lambda_S E_S(\mathbf{p}) + \lambda_P E_P(\mathbf{p}) +\lambda_A E_A(\mathbf{p}),
$$
-->

<p align="center">
<img src="https://render.githubusercontent.com/render/math?math=E(\mathbf{p})=%20\lambda_J%20E_J(\mathbf{p})%20%2B%20\lambda_D%20E_D(\mathbf{p})%20%2B%20\lambda_S%20E_S(\mathbf{p})%20%2B%20\lambda_P%20E_P(\mathbf{p})%20%2B\lambda_A%20E_A(\mathbf{p}),"/>
</p>

<!-- $\mathbf{p}$-->
where <img src="https://render.githubusercontent.com/render/math?math=\mathbf{p}"> denote the unknown variables, which in our case are the pose parameters that animate the template mesh into a specific pose in order to fit into the current live data.
The different error terms are:
- an extrapolated 3D Chamfer distance metric, <img src="https://render.githubusercontent.com/render/math?math=E_D">
- a surface alignment matching term, <img src="https://render.githubusercontent.com/render/math?math=E_S">
- a penalization term of mesh self-intersections, <img src="https://render.githubusercontent.com/render/math?math=E_P">
- the 2D projective silhouette error, <img src="https://render.githubusercontent.com/render/math?math=E_J">
- an anthropometric penalization of unnatural human poses, <img src="https://render.githubusercontent.com/render/math?math=E_A">

These can be categorized with respect to their domain:

| <img src="https://render.githubusercontent.com/render/math?math=3D">   |      <img src="https://render.githubusercontent.com/render/math?math=2D">      |  Pose |
|:----------:|:-------------:|:------:|
| Chamfer distance (<img src="https://render.githubusercontent.com/render/math?math=E_D">) <!-- $E_D$ --> |  Silhouette error (<img src="https://render.githubusercontent.com/render/math?math=E_J">) <!-- $E_J$ --> | Anthropometric prior (<img src="https://render.githubusercontent.com/render/math?math=E_A">) <!-- $E_A$ --> |
| Surface alignment(<img src="https://render.githubusercontent.com/render/math?math=E_S">) <!-- $E_S$ --> |       |    |
| Self-penetration error (<img src="https://render.githubusercontent.com/render/math?math=E_P">) <!-- $E_P$ --> |  |     |

<!--
Three of these are defined in the three-dimensional space:
- an extrapolated 3D Chamfer distance metric, <img src="https://render.githubusercontent.com/render/math?math=E_D">
- a surface alignment matching term, <img src="https://render.githubusercontent.com/render/math?math=E_S">
- a penalization term of mesh self-intersections, <img src="https://render.githubusercontent.com/render/math?math=E_P">

One in the two-dimensional space:
- the 2D projective silhouette error, <img src="https://render.githubusercontent.com/render/math?math=E_J">

And a regularization term for the unknown variables (pose parameters):
- an anthropometric penalization of unnatural human poses, <img src="https://render.githubusercontent.com/render/math?math=E_A">
-->

<!--
From a data-fitting perspective, the **data terms** are:
- the Chamfer distance,  <img src="https://render.githubusercontent.com/render/math?math=E_D">
- surface alignment, <img src="https://render.githubusercontent.com/render/math?math=E_S">, and,
- silhouette error, <img src="https://render.githubusercontent.com/render/math?math=E_J">,
-->

<!--
while the **constraints** are provided by:
- the self-penetration term, <img src="https://render.githubusercontent.com/render/math?math=E_P">, and,
- the anthropometric prior term, <img src="https://render.githubusercontent.com/render/math?math=E_A">
-->
or a data fitting perspective:

| **Data Terms**   |      **Constraints**      |
|:----------:|:-------------:|
| Chamfer distance (<img src="https://render.githubusercontent.com/render/math?math=E_D">) <!-- $E_D$ --> |  Self-penetration error (<img src="https://render.githubusercontent.com/render/math?math=E_P">) <!-- $E_P$ --> |
| Surface alignment (<img src="https://render.githubusercontent.com/render/math?math=E_S">) <!-- $E_S$ --> |    Anthropometric prior (<img src="https://render.githubusercontent.com/render/math?math=E_A">) <!-- $E_A$ -->   |
| Silhouette error (<img src="https://render.githubusercontent.com/render/math?math=E_J">) <!-- $E_J$ --> |  |

Our complete objective as formulated above is a linear weighted combination of these terms as weighted by the respective weights <img src="https://render.githubusercontent.com/render/math?math=\lambda"> <!-- $\lambda$ -->. More details can be found in __\[[10](#Perfcap)\]__.

Each pose parameter vector <img src="https://render.githubusercontent.com/render/math?math=\mathbf{p}:=\{\mathbf{R},\mathbf{t},\boldsymbol{\theta}\}">, corresponds to a global root rotation <img src="https://render.githubusercontent.com/render/math?math=\mathbf{R}\in\mathbb{R}^3"> and translation <img src="https://render.githubusercontent.com/render/math?math=\mathbf{t}\in\mathbb{R}^3">, as well as per joint <img src="https://render.githubusercontent.com/render/math?math=j\in[1, J]"> rotation parameters <img src="https://render.githubusercontent.com/render/math?math=\boldsymbol{\theta}\in\mathbb{R}^{h\times3}"> for all joints <img src="https://render.githubusercontent.com/render/math?math=J">,parameterized by their exponential map __\[[1](#ExpMap)\]__.
All template meshes are automatically skinned and rigged with __\[[2](#Pinocchio)\]__.
By animating the rigged and skinned template with the pose parameters <img src="https://render.githubusercontent.com/render/math?math=\mathbf{p}"> we get a re-posed mesh of the template <img src="https://render.githubusercontent.com/render/math?math=\mathbf{\hat{V},\hat{N}}=DQS(\mathbf{V},\mathbf{N},\mathbf{p})">, with <img src="https://render.githubusercontent.com/render/math?math=\mathbf{\hat{V}}"> and <img src="https://render.githubusercontent.com/render/math?math=\mathbf{\hat{N}}"> the template's vertices and normals respectively (connectivity, _i.e._ triangles/faces remain consistent).
For animation we use dual quaternion skinning (DQS) __\[[3](#DQS)\]__.

<p align="center">
<img width=500 src="../assets/images/animation.png"/>
</p>

Four error functions are formulated indirectly to the optimized variables through the animated mesh, while the anthropometric prior <img src="https://render.githubusercontent.com/render/math?math=E_A"> is calculated solely on the pose parameters <img src="https://render.githubusercontent.com/render/math?math=\mathbf{p}">.

Regarding the former, we first calculate the Euclidean Distance Transform (EDT) using a separable Chamfer implementation __\[[4](#Chamfer)\]__ defined on a voxel grid <img src="https://render.githubusercontent.com/render/math?math=\mathbf{G}"> whose bounding box is tightly calculated using the input live data.
Thus our <img src="https://render.githubusercontent.com/render/math?math=3D"> error terms are defined:

- <p align="left"><img width=350 src="https://render.githubusercontent.com/render/math?math=E_D=\frac{1}{V}\sum_{\mathbf{v}\in\mathbf{\hat{V}}}\mathcal{S}_\mathbf{P}(\mathbf{G},\lfloor\mathbf{v}\rfloor)%2B||\mathbf{v}-\lfloor\mathbf{v}\rfloor||_2"></p>
    
    where a sampling operation <img src="https://render.githubusercontent.com/render/math?math=\mathcal{S}"> defined on the EDT grid, samples the distance at each animated vertex <img src="https://render.githubusercontent.com/render/math?math=\mathbf{v}">, clamped within the confines of the bounding box that the EDT was calculated in through <img src="https://render.githubusercontent.com/render/math?math=\lfloor . \rfloor">.
    Given that pose parameters <img src="https://render.githubusercontent.com/render/math?math=\mathbf{p}"> may be explored outside the bounding box that the EDT is defined in, we further supplement the sampled distance, with an approximate distance that is negligible within the bounding box, but allows the error to extrapolate outside its bounds and offer meaningful evaluations.

- <p align="left"><img width=350 src="https://render.githubusercontent.com/render/math?math=E_S=\frac{1}{V}\sum_{(\mathbf{v},\mathbf{n})\in(\mathbf{\hat{V}},\mathbf{\hat{N}})}1-\langle\nabla\mathcal{S}_{\mathbf{P}}(\mathbf{G},\mathbf{\lfloor\!v\rfloor}),\mathbf{n}\rangle^2"></p>

    which represents a surface alignment error using the gradient of the distance field and the animated template's surface normals.

![Errors](./assets/images/errors.png)



<a name="ExpMap"/>__[1]__ Grassia, F. S. (1998). Practical parameterization of rotations using the exponential map. Journal of graphics tools, 3(3), 29-48.

<a name="Pinocchio"/>__[2]__ Baran, I., & Popović, J. (2007). Automatic rigging and animation of 3d characters. ACM Transactions on graphics (TOG), 26(3), 72-es.

<a name="DQS"/>__[3]__ Kavan, L., Collins, S., Žára, J., & O'Sullivan, C. (2007, April). Skinning with dual quaternions. In Proceedings of the 2007 symposium on Interactive 3D graphics and games (pp. 39-46).

<a name="Chamfer"/>__[4]__ Coeurjolly, D., & Montanvert, A. (2007). Optimal separable algorithms to compute the reverse euclidean distance transformation and discrete medial axis in arbitrary dimension. IEEE transactions on pattern analysis and machine intelligence, 29(3), 437-448.


<a name="Perfcap"/>__[10]__ To appear.