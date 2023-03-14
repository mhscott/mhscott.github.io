---
title: There's a Wrapper for That
category: Constitutive Model
---

Many publications describe software design patterns for reusing object-oriented software. The most widely read book on design patterns is so influential that it has its own [Wikipedia page](https://en.wikipedia.org/wiki/Design_Patterns). In this book, the "Gang of Four" offers two guiding principles for software design patterns. 

The first principle is to program to an interface, not an implementation, i.e., seek general solutions, not specific solutions that work for only a few cases. The second principle is to favor object composition over class inheritance as a means of code reuse, i.e., treat objects you want to reuse as unchangeable units.

Although numerous software design patterns appear at all levels of the OpenSees framework, this post will highlight the
[_Decorator_](https://en.wikipedia.org/wiki/Decorator_pattern) pattern and
its application to _UniaxialMaterial_ and _NDMaterial_ objects.  

The _Decorator_ pattern creates a new object that modifies the functionality of an object of the same type. Not only does the _Decorator_ pattern allow for extensive code reuse, it can lead to rapid development of new models and quick changes to existing models without having to add new code.

Writing this post, I hope to mitigate unnecessary code duplication in OpenSees. So, before you code up a new material model, think to yourself "there might be a wrapper for that", then re-read this post.

---
<br>
There are several _UniaxialMaterial_ wrapper classes. After briefly describing the functionality of each wrapper, I will show how the wrapper affects a canonical wrapped material through one load cycle. The wrapped material is a _HardeningMaterial_ object with elastic modulus, _E_=3, yield stress, $$\sigma_y$$=1, and kinematic hardening modulus, _H<sub>kin</sub>_=0.1.

In a case of addition by subtraction, the _PathIndependentMaterial_ class does not call `commitState()` on its wrapped _UniaxialMaterial_ object, making the stress-strain response path-independent.

![Path-independent material](/assets/images/WrapperPathIndependent.png)

An undocumented wrapper, the _TensionOnlyMaterial_ class returns zero stress and tangent for any _UniaxialMaterial_ object that returns negative stress. The wrapper also doesn't call `commitState()` when there is negative stress in the wrapped material.

![Tension only material](/assets/images/WrapperTensionOnly.png)

The _MinMaxMaterial_ forwards strain to its wrapped _UniaxialMaterial_ object and returns its stress and tangent, until the material strain is greater than a specified maximum strain or less than a specified minimum. After this condition is met in `commitState()`, the wrapper does nothing, like the wrapped material has failed. Below is a demonstration with the minimum strain set to -0.8.

![Min-max material](/assets/images/WrapperMinMax.png)

Another undocumented wrapper, the _SimpleFractureMaterial_ imposes tensile fracture on its wrapped _UniaxialMaterial_ object. After reaching the specified tensile strain, the wrapper will provide compressive stress based on an estimate of the elastic strain. Below is a demonstration with the maximum tensile strain set to 0.8.

![Simple fracture material](/assets/images/WrapperSimpleFracture.png)

The _InitStrainMaterial_ wrapper imposes an initial strain on its wrapped _UniaxialMaterial_ object. After the constructor is called, nothing special is done by the wrapper. Below is a demonstration with the initial strain equal to -0.1.

![Initial strain material](/assets/images/WrapperInitialStrain.png)

Similarly, the _InitStressMaterial_ imposes an initial stress on its wrapped _UniaxialMaterial_ object. Remember that it's a good idea to do a single analysis step with $$\Delta t$$=0 when using the initial strain and initial stress wrappers in your model. Below is a demonstration with the initial stress equal to 0.3.

![Initial stress material](/assets/images/WrapperInitialStress.png)

The _FatigueMaterial_ class wraps any _UniaxialMaterial_ object and accumulates damage according to Miner's Rule using a modified rainflow cycle counting algorithm. After the fatigue life is exhausted, the wrapper returns zero stress and tangent. Please see the [wiki documentation](https://opensees.berkeley.edu/wiki/index.php/Fatigue_Material) for this wrapper to find more details.

An undocumented wrapper, the _DamperMaterial_ class uses the stress-strain response of any _UniaxialMaterial_ object as a stress-strain rate relationship for damping. This conversion is done by a simple transfer of strain rate to strain and tangent to damping tangent in the material state determination methods.

You might wonder why I omitted the _ParallelMaterial_ and _SeriesMaterial_ classes from this list. The reason is they are examples of the
[_Composite_](https://en.wikipedia.org/wiki/Composite_pattern) pattern, not the
_Decorator_ pattern.

---
<br>
There are also wrappers for _NDMaterial_ objects. The _PlaneStrainMaterial_ sets the appropriate strain components to zero before calling its wrapped three-dimensional _NDMaterial_ object. We should also have one of these for the axisymmetric strain condition.

The _PlaneStressMaterial_, _PlateFiberMaterial_, and _BeamFiberMaterial_ (versions for 2D and 3D beams), classes use static condensation in order to impose the appropriate stress conditions on their wrapped three-dimensional _NDMaterial_ objects.

The _ContinuumUniaxial_ class performs static condensation to get from a three-dimensional _NDMaterial_ object to a uniaxial state of stress. Because it goes between the _NDMaterial_ and _UniaxialMaterial_ interfaces, the _ContinuumUniaxial_ class is technically an [_Adapter_](https://en.wikipedia.org/wiki/Adapter_pattern), not a _Decorator_.

The _PlateFromPlaneStressMaterial_ wrapper adds linear-elastic shear behavior to a plane stress material in order to get the plate fiber stress condition. This wrapper does not need to perform static condensation. The _PlateRebarMaterial_ wrapper (also technically an _Adapter_) uses a _UniaxialMaterial_ object and an orientation angle to represent reinforcement in a shell layer.

The _InitStressNDMaterial_ is the _NDMaterial_ version of the _InitStressMaterial_ wrapper shown above. It only works for plane strain and three-dimensional material models, but there's no reason it can't be adapted for initial stresses in beam and plate materials.

The _InitStressNDMaterial_, _BeamFiberMaterial_, and _ContinuumUniaxial_ wrappers are undocumented. This is my doing.

---
<br>
Well, that should wrap up this post. Information on other design patterns used in the material and element models of OpenSees can be found in
[Scott et al (2008)](http://dx.doi.org/10.1061/(ASCE)0733-9445(2008)134:4(562))
while information on the design patterns used in the high level model building and solution strategies can be found in
[McKenna et al (2010)](http://dx.doi.org/10.1061/(ASCE)CP.1943-5487.0000002).