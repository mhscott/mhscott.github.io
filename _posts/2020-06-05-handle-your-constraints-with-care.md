---
title: Handle Your Constraints with Care
category: Linear Analysis
---

Manipulating the nodal equilibrium equations is necessary to enforce constraints between degrees of freedom (DOFs) at two or more nodes in a structural model. These multi-point constraints arise from assumptions of axial and flexural rigidity of frame elements, e.g., rigid diaphragms, and also between two nodes at the same location where some of the DOFs are the same, e.g., moment releases.

In addition to the `Plain` constraint handler, which only enforces single-point constraints like pin, roller, and fixed boundary conditions, as well as pressure constraints in the PFEM, OpenSees has three multi-point constraint handling methods: `Penalty`, `Lagrange`, and `Transformation`.

The ["Cook book"](https://www.amazon.com/Concepts-Applications-Finite-Element-Analysis/dp/0471356050)
describes the theory behind these methods and how they handle constraints at the global level. Their implementation in OpenSees is a little different, where _FE_Element_ and _DOF_Group_ objects enforce the constraints locally among affected DOFs.

An unfortunate side effect of enforcing multi-point constraints is some convergence tests and equation solvers will not get the job done if you use the `Penalty` or `Lagrange` method. It's also possible for the `Transformation` method to give incorrect results if a nodal DOF is involved in more than one constraint, even when one of the constraints is single-point.

OpenSees doesn't warn you that you're doing something funky with constraint handlers. Like wearing brown socks with black shoes, you're just supposed to know that it doesn't work. I will demonstrate where you can go wrong with this simple example.

![Rigid beam model](/assets/images/RigidBeam.png)

Half of the span is flexible while the other half is rigid, using the rigidLink command in OpenSees with node 2 retained and node 3 constrained (`rigidLink -beam 2 3`). Due to axial and flexural rigidity, nodes 2 and 3 should have the same horizontal displacement and the same rotation. The difference in vertical displacement between nodes 2 and 3 should be equal to the rotation times the rigid length.

## Penalty Method

The penalty method adds artificial springs of high stiffness to the nodal equilibrium equations in order to enforce the constraints. The spring stiffnesses, or penalty numbers, should be large enough to enforce the constraints, but not so large as to cause ill-conditioning of the system of equations.

Although you can use different penalty numbers for each constraint, OpenSees takes only two penalty numbers: one for all the single-point constraints and one for all the multi-point constraints. Penalty numbers about three or four orders of magnitude higher than some characteristic stiffness of the model usually work pretty well. For the penalty numbers in this example, we'll use
_EI/L x 10<sup>4</sup>_ where _L_=240 in is the span length.

Because small changes in displacement can lead to large changes in force with very stiff springs, using the `NormUnbalance` convergence test with penalty constraints is ill-advised. As shown below, the convergence test gets stuck even though the norm of the displacement increment is very small.

![Convergence with NormUnbalance and Penalty constraints](/assets/images/PenaltyBad.png)

The equilibrium iteration will converge if we use the `NormDispIncr` convergence test. You can verify that the nodal displacements satisfy the constraints imposed by the rigid beam.

![Convergence with NormDispIncr and Penalty constraints](/assets/images/PenaltyGood.png)

## Lagrange Multipliers

With the `Lagrange` constraint handler, constraint equations are added to the system of nodal equilibrium equations with Lagrange multipliers as the additional unknowns. Because the single- and multi-point constraints are handled simultaneously with Lagrange multipliers, some gymnastics are required to make things work.

Because the constraint equations can be scaled arbitrarily, there are scale factors, $$\alpha_S$$ and $$\alpha_M$$, required for Lagrange multipliers. The default values of 1.0 will not work--you need to make these factors large like penalty numbers. Like in the Penalty method, we'll use _(EI/L) x 10<sup>4</sup>_ for the scale factors.

Along with these constraint equations come zeros along the diagonal of the simultaneous system of nodal equilibrium and nodal constraint equations. This is a problem for several solvers available in OpenSees, including the default `ProfileSPD` which fails due to zeros on the diagonal.

![Convergence with default solver and Lagrange constraints](/assets/images/LagrangeBad.png)

To get around this, you will need to use a general solver like `UmfPack`, `FullGeneral`, or `SparseGeneral -piv` with Lagrange multipliers. As shown below, compatible nodal displacements are obtained.

![Convergence with sparse solver and Lagrange constraints](/assets/images/LagrangeGood.png)

## Transformation Method

The transformation method uses static condensation to enforce multi-point constraints. There are no issues with this approach in terms of convergence tests or equation solvers. However, the Transformation method can become confused, or "non-invariant", when there are DOFs involved in more than one constraint. For this example, the Transformation method gives an incompatible solution without any analysis errors or warnings.

![Solution with daisy-chained Transformation constraints](/assets/images/TransformationBad.png)

There is a terse summary of the issue on the
[Transformation constraint handler wiki page](https://opensees.berkeley.edu/wiki/index.php/Transformation_Method): "If multiple nodes are constrained, make sure that the retained node is not constrained in any other constraint." This basically means don't daisy chain the constraints like you would power strips.

For the beam example of this post, the vertical DOF at node 3 is retained in the single point constraint. This roller cannot be a retained node in the `rigidLink` command. If we switch the retained and constrained nodes in the `rigidLink` command (`rigidLink -beam 3 2`), the results make sense.

![Solution with consistent Transformation constraints](/assets/images/TransformationGood.png)

Regardless of which constraint handling method you use, always check your output when using multi-point constraints. Even with compatible nodal displacements, the reactions can appear to violate equilibrium, but I'll save that for another post.