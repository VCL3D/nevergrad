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

Our complete objective as formulated above is a linear weighted combination of these terms as weighted by the respective weights <img src="https://render.githubusercontent.com/render/math?math=\lambda"> <!-- $\lambda$ -->. More details can be found in [3].

<!--![Animation](./assets/images/animationc.png)-->
<p align="center">
<img width=500 src="../assets/images/animation.png"/>
</p>

![Errors](./assets/images/errors.png)