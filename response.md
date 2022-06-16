# Author Response

Dear reviewers,

Thank for the time and effort spent by the reviewers in providing useful feedbacks to improve our paper.

## Response to questions

### Reviewer #703A

1. L.134: You say here that "simple name mangling" cannot solve the problem. But isn't your translation doing just that? IIUC, it renames classes to allow multiple version to coexist. Plus, it performs global type inference to resolve uses of unmangled names to the right target.

**Reply:** The issue of simple name mangling is further elaborated in the paper in Section 6.3. We did not, unfortunately, provided a concrete example in the paper.

We can suppose a class `C` requiring two incompatible versions of `D`, where one is renamed through name mangling. However, this would render it impossible to pass the renamed `D` to locations that require `D`. The situation can become more unpredictable when renamed dependencies are involved in transitive dependencies. On the other hand, given two versions of D ver.1 and 2, the translation into D_ver_1 and D_ver_2 still retain the common identity of D between the two versions. Hence, even if they are renamed, they are still treated as D.

2. L.357: Can multiple paths coexist?

**Reply:** From L.349 the paper did mention that multiple *path*s exist for a class definition. However, the compilation later on fixes it to a specific path.

3. Fig. 3: What is the set "CT(C)", and why is this condition needed in this rule?

**Reply:** `n` $\in dom$(CT(`C`)) in Fig.3 basically describes the same condition as `C!n extends D#N {...}`. We will remove this condition in the revised version of the paper.

4. L.656: A constraint of the form X=C!n does not seem to allow any path variables. How can it achieve the same expressiveness?

**Reply:** The proof for this has not been formulated for this in the paper. 

We can observe that all the necessary constraint for a path variable are also present in the simplified constraint. When we generate constraints in BatakJava for an instance `new C(...)` (typed as `X`), it does not only constraint `X = C!n`, but also apply constraint to its ancestor classes. Joining the version components of the constraint given to `new C(...)` and its ancestor classes is equivalent to a path.

### Reviewer #703B

1. What would be a common usage scenario of this language?

**Reply:** Summarizing the motivating example in Section 2, the usage scenario of BatakJava's multi-version programming is to allow gradual updates of upstream dependencies with breaking changes.

2. Is it straightforward to handle more than three versions?

**Reply:** As shown by the typing rules in Section 3, the inference is not limited to two versions.

3. Is it straightforward to apply this approach to other languages?

**Reply:** 

### Reviewer #703C

1. What are compilation times like for your prototype? Do they scale linearly with the size of the program (the way a traditional typechecker that does not call a solver should)?

2. Can a programmer always use the type errors produced by your type checker to guide them to the source of the problem? What if the solver times out?

3. Why do you think BatakJava programs are less likely to have defects than standard Java programs? Can you provide any evidence for that argument?

## Response to main concerns

### Reviewer #703A

The reviewer had issues with the motivation and the practical viability of our approach. It can be summarized into the following points:

1. 

### Reviewer #703B

### Reviewer #703C

### Reviewer #703B

### Reviewer #703C

## Response to other comments

### Reviewer #703A

### Reviewer #703B

### Reviewer #703C