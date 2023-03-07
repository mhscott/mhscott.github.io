---
title: Incompetence, Not Malice
category: Programming
---

"Never attribute to malice that which can be explained by incompetence" is a form of 
[Hanlan's razor](https://en.wikipedia.org/wiki/Hanlon%27s_razor), an 
aphorism that explains many actions in academia and elsewhere. For example, we often perceive omissions 
of important details in published work as intentional acts to prevent reproduction of the research. 
In some cases, this is true, while in most, plain 'ol incompetence is to blame. I will share a personal 
example of non-malicious incompetence.

The formulation and OpenSees implementation of the _KrylovNewton_ algorithm, an accelerated Newton
technique, is described in [Scott and Fenves (2010)](https://doi.org/10.1061/(ASCE)ST.1943-541X.0000143). 
The algorithm can overcome some of the issues 
with Newton-Raphson and Modified Newton. Figure 2 of the article shows MATLAB code for the algorithm implementation.

In the years since the article was published, I never paid much attention to the code shown in Figure 2. 
A few people told me the OpenSees implementation of KrylovNewton worked pretty well for their models and the 
paper picked up [some citations](https://scholar.google.com/scholar?oi=bibs&hl=en&cites=5870300280478923797), so all was good.

Then, in 2020, I was developing an example in Python for my nonlinear structural analysis course. Using my
hard copy of the 2010 article, I implemented the algorithm based on the code in Figure 2. The algorithm 
did not work.

After some time, I realized a line of code was missing. The displacement increment, which is required in order 
to compute updates in subsequent iterations, was not being stored after the least squares calculation. Plus, 
there was a sign inconsistency implied by naming the tangent function `jacobian` instead of `stiffness`. 
Here’s the correct MATLAB code:

```matlab
U = U0;
R = residual(U);
m = mmax + 1;

% Main loop
while (norm(R) > tol)

   % Refresh tangent and clear subspace
   if (m > mmax)
      K = stiffness(U); % THIS WAS jacobian(U) IN THE ARTICLE
      [l,u] = lu(K);
      m = 0;
   end

   % Back solve
   r = u \ (l \ R);
   AV(:,m+1) = r;

   % Least squares analysis
   if (m > 0)
      AV(:,m) = AV(:,m) - r;
      c = AV(:,1:m) \ r;
      r = r + V(:,1:m)*c;
      r = r - AV(:,1:m)*c;
   end
   V(:,m+1) = r; % THIS LINE SHOULD HAVE BEEN IN FIGURE 2 OF THE ARTICLE

   % Update state of structure
   U = U + r;
   R = residual(U);
   m = m+1;
end
```

This was not an intentional act of sabotage like when [Raymond’s mother taught Debra her meatball recipe](https://www.imdb.com/title/tt0764391/). 
Although C++ is more difficult to read than MATLAB, the _KrylovNewton_ code had been in the OpenSees repository
for some time before the article was published and the code continues to be there for anyone to see and reproduce. 
I simply left out a line of code when making a figure for an article. Incompetence, not malice.

---
<br>

[Formal erratum](https://doi.org/10.1061/(ASCE)ST.1943-541X.0002748) published in August 2020
