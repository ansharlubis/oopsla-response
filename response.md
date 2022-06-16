# Author Response

Dear reviewers,

Thank for the time and effort spent by the reviewers in providing useful feedbacks to improve our paper.

The response is structured as follows:

1. Response to points listed by the reviewers as *Questions for Author Response*.
2. Response to points we consider are the main concerns of the reviewers from the *Assessment*.
3. Response to other comments, questions, and concerns uncovered in the previous sections.

## Response to questions

### Reviewer #703A

Reviewer #703A did not list any points as *Questions for Author Response*.

### Reviewer #703B

1. What would be a common usage scenario of this language? 

    *(Related comments)* Do you assume this approach is a workaround for broken backward compatibility?

**Reply:** Summarizing the motivating example in Section 2, the usage scenario of BatakJava's multi-version programming is to allow gradual updates of upstream dependencies with breaking changes. In other words, it is a workaround for broken backward compatibility.

2. Is it straightforward to handle more than three versions?

**Reply:** As shown by the typing rules in Section 3, the inference is not limited to two versions, although the complexity of the solving does grow with the number of versions.

3. Is it straightforward to apply this approach to other languages? 

    *(Related comments)* Why OOP and why class-wise versions? If the same principle is applied to imperative or functional programming languages, what would happen?

**Reply:** Since the approach we take is a relatively simple type inference with global constraints, it should be applicable to other imperative languages (Java itself is imperative).

The reason for choosing OOP is mentioned in L.56 of the paper. Previous work in programming with versions took a functional approach that is difficult to apply for data structure evolution across versions. We argue that programming with versions in a class-based OOP represents the essence of data structure evolution.

### Reviewer #703C

1. What are compilation times like for your prototype? Do they scale linearly with the size of the program (the way a traditional typechecker that does not call a solver should)?

**Reply:** We have not yet conducted any empirical evaluation of the compiler. The simple case study we conducted involved 38 class definitions, totaling around 1200 LOC; the compilation takes 2.1 times longer than the base Java compilation.

The current inference does not scale linearly, it grows exponentially in regards to the number of versions.

2. Can a programmer always use the type errors produced by your type checker to guide them to the source of the problem? What if the solver times out?

**Reply:** The currently implemented prototype does not yet specify where the source of the type problem is. 

We plan to further improve the implementeation by requiring the solver to output problematic variables and directing users to the expressions associated with those variables.

3. Why do you think BatakJava programs are less likely to have defects than standard Java programs? Can you provide any evidence for that argument?

**Reply:** There is a trade-off in adding another variability axis to class definitions. The ability of multi-version programming opens up possibility to program with multiple definitions of the same class at the same time, hopefully making adoption of upstream update easier. However, as pointed out by both Reviewer #703A and #703C, the long-term inclusion of multiple versions can be hard to maintain.

Unfortunately, we cannot provide any concrete evidence yet for these arguments.

## Response to main concerns

### Reviewer #703A

The biggest problem described by the reviewer concerns the motivation and practical viability of our approach. It can be summarized into the following points:

1. Parallel use of multiple versions of a class can lead to diverging system. In long-term practice this may turn into a maintenance disaster.

**Reply:** The current formalization and its prototype serve the role of a base for further exploration into the OOP with versions. Maintainability is one of the important issue that we have to deal with in the future. This point will further be added into Section 7.

A possible approach to this is to keep all the expressions performing as they are in Java, where compilation uses a single version. However, when the compilation fails to compile due to class/method/field availability, the compilation can switch to a multi-version compilation by finding another version of the class within the range of versions defined by the users.

2. Making seemingly innocent change in one part of the program can result in a completely different result for the global type inference.

**Reply:** Adding a new axis to the variability of a class can certainly increase the unpredictability of semantics, however users can be assisted internally within the language by providing explicit version selection for import declarations and banned version declarations from an upstream package. Externally this can also be assisted by providing IDE tool supports that analyze which version is accessible and compatible with an expression.

3. No flexibility in the choice of superclasses. The reviewer is also doubtful if let generalization can allow it.

    *(Related comments)*

    L.530: "This implies that the current calculus does not yet allow different instances of class C!n to inherit different versions of D." -- Is that referring to the inheritance flexibility I complained about above?

**Reply:** The paper in Section 7.2 mentions this as one of the future work. Another approach that was not included in the paper is to introduce version parameters for superclass and field types, similar to how type parameters added in Featherweight Generic Java. This way, each instance can instantiate the parameter with a different version.

4. More flexibility may result in code to blow-up exponentially during translation.

**Reply:** 

5. The compilation requires a whole program analysis, making it not modular and unscalable.

**Reply:**

The reviewer also had the following concerns in regards to the formalization presented in our paper:

1. The formalization appears to be sloppy in some places.

**Reply:** 

2. The constraint system appears to be different from the implementation described later. The paper made an unsubstantiated claim that they are equally expressive.

    *(Related comments)*

    L.656: A constraint of the form X=C!n does not seem to allow any path variables. How can it achieve the same expressiveness?

**Reply:** The proof for this has not been formulated for this in the paper. 

We can observe that all the necessary constraint for a path variable are also present in the simplified constraint. When we generate constraints in BatakJava for an instance `new C(...)` (typed as `X`), it does not only constraint `X = C!n`, but also apply constraint to its ancestor classes. Joining the version components of the constraint given to `new C(...)` and its ancestor classes is equivalent to a path.

### Reviewer #703B

1. Backward compatibility is sometimes intentionally made to force users to update their code for various reasons. Multi-version programming may hinder this update.

**Reply:**

2. When the number of versions accumulate, the target code size and complexity would be larger.

**Reply:** We assume that multi-version programming is used for gradual update of libraries in which single version programming cannot work. The users can specify the versions that they intend to use to avoid generating unnecessary and unrelated code.

### Reviewer #703C

1. My biggest concern is that I'm not sure that BatakJava is actually solving a problem that programmers have, and that even if it is its not doing so in a way that is likely to prevent bugs down the line. 

**Reply:** 

2. BatakJava might enable the programmer to accidentally build software that has unexpected behavior that the "standard" Java compiler would have forbidden.

    *(Related comments)*
    
    251: yes, all of these methods are available in both versions. But there is not a guarantee that their specifications are the same - so it is possible that choosing one version will result in different behavior than choosing a different version. This seems to me like it would be a good source of so-called "spooky action at a distance": a change is seemingly-unrelated code might change the behavior of another seemingly-unrelated piece of code, because one of the two might be the source of a version constraint that changes which implementation of a method gets called at run time. The same issue can occur with dynamic dispatch and generics, and in my experience it is a huge source of problems, especially for students.

**Reply:**

3. The evaluation should involve actual programmers and see whether they think it is a good idea.

    *(Related comments)*

    810: this evaluation doesn't really tell me anything. Is this a real Java program (I think no, it's just a snippet you artificially copied out of a real library)? Is this practical at all on programs of even moderate size? What are compilation times like? Any of these questions would be more interesting than what you have here.

    969: no kidding it produces many clones. A good evaluation would have included a measurement of how many.

**Reply:**

## Response to other comments

### Reviewer #703A

1. L.134: You say here that "simple name mangling" cannot solve the problem. But isn't your translation doing just that? IIUC, it renames classes to allow multiple version to coexist. Plus, it performs global type inference to resolve uses of unmangled names to the right target.

**Reply:** The issue of simple name mangling is further elaborated in the paper in Section 6.3. We did not, unfortunately, provided a concrete example in the paper.

We can suppose a class `C` requiring two incompatible versions of `D`, where one is renamed through name mangling. However, this would render it impossible to pass the renamed `D` to locations that require `D`. The situation can become more unpredictable when renamed dependencies are involved in transitive dependencies. On the other hand, given two versions of D ver.1 and 2, the translation into D_ver_1 and D_ver_2 still retain the common identity of D between the two versions. Hence, even if they are renamed, they are still treated as D.

2. L.357: Can multiple paths coexist?

**Reply:** From L.349 the paper did mention that multiple *path*s exist for a class definition. However, the compilation later on fixes it to a specific path.

3. Fig. 3: What is the set "CT(C)", and why is this condition needed in this rule?

**Reply:** `n` $\in dom$(CT(`C`)) in Fig.3 basically describes the same condition as `C!n extends D#N {...}`. We will remove this condition in the revised version of the paper.

### Reviewer #703B

### Reviewer #703C