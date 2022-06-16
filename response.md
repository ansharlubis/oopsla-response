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

    *(Related comments)* Why OOP and why class-wise versions? If the same principle is applied to imperative or functional programming languages, what would happen? Would it be more challenging?

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

    L.530: "This implies that the current calculus does not yet allow different instances of class `C!n` to inherit different versions of `D`." -- Is that referring to the inheritance flexibility I complained about above?

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

    L.656: A constraint of the form `X=C!n` does not seem to allow any path variables. How can it achieve the same expressiveness?

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

**Reply:** The concern for behavioral change is apt. Currently, the multiple versions approach can only account for type compatibility, and not behavioral compatibility.

A feature of the prototype that was not explained in the paper is version selection for import declarations. In addition to using version selection on expression level when necessary, we argue this will allow users to avoid unexpected behavior. Futhermore, when multiple solutions exist, the program defaults to the latest version.

3. The evaluation should involve actual programmers and see whether they think it is a good idea.

    *(Related comments)*

    810: this evaluation doesn't really tell me anything. Is this a real Java program (I think no, it's just a snippet you artificially copied out of a real library)? Is this practical at all on programs of even moderate size? What are compilation times like? Any of these questions would be more interesting than what you have here.

    969: no kidding it produces many clones. A good evaluation would have included a measurement of how many.

**Reply:** To show the feasibility of the language, we plan to implement medium-sized application using BatakJava.

## Response to other comments

### Reviewer #703A

1. L.134: You say here that "simple name mangling" cannot solve the problem. But isn't your translation doing just that? IIUC, it renames classes to allow multiple version to coexist. Plus, it performs global type inference to resolve uses of unmangled names to the right target.

**Reply:** The issue of simple name mangling is further elaborated in the paper in Section 6.3. We did not, unfortunately, provided a concrete example in the paper.

We can suppose a class `C` requiring two incompatible versions of `D`, where one is renamed through name mangling. However, this would render it impossible to pass the renamed `D` to locations that require `D`. The situation can become more unpredictable when renamed dependencies are involved in transitive dependencies. On the other hand, given two versions of D ver.1 and 2, the translation into D_ver_1 and D_ver_2 still retain the common identity of D between the two versions. Hence, even if they are renamed, they are still treated as D.

2. L.333: This says that "p, q range over paths" without having mentioned paths before, which was confusing. They are only introduced several paragraphs later.

**Reply:** Fix the writing

3. L.357: Can multiple paths coexist?

**Reply:** From L.349 the paper did mention that multiple *path*s exist for a class definition. However, the compilation later on fixes it to a specific path.

4. Fig. 3: What is the set "CT(C)", and why is this condition needed in this rule?

**Reply:** `n` $\in dom$(CT(`C`)) in Fig.3 basically describes the same condition as `C!n extends D#N {...}`. We will remove this condition in the revised version of the paper.

5. Fig. 7: I think there are several issues with these typing rules.
- Contain uses of index variables that escape the scope of their quantifier

**Reply:** We will fix the writing of the index variables in the revision.

- Not all rules produce types of the same shape (`X` and `C`)

**Reply:** We will change the rules so all are typed with a type variable `X#N`.

- T-New seems to require a unique superclass D

**Reply:** It does not require a unique superclass `D`. The superclass `D` depends on the `p`$_j$ chosen. With different `p`$_j$, then `C` can possibly have `n`$_j$ with a different superclass.

- T-Field is missing a closing parenthesis

**Reply:** Add the closing parenthesis.

6. L.582: You may want to say that sigma is a type substitution, not affecting term variables.

**Reply:** We will replace the phrasing appopriately.

7. L.625: I assume this implies that the process requires a whole program transformation?

**Reply:** No, the semantics of the runtime transforms only the main expression and its subsequent reductions.

### Reviewer #703B

1. Is it straightforward to transform Java to BatakJava?



### Reviewer #703C

1. 31: "whose"'s antecedent is unclear

**Reply:** We will change the sentence accordingly.

2. 34: delete "of"

**Reply:** We will change the sentence accordingly.

3. 248: if there is a key idea in this paper, this is it - but it appears very late.

**Reply:**  We argue that it is not a key idea, but allowing a single method definition to represent multiple different signatures is certainly one of the consequences of having an instance representing multiple versions simultaneously.

4. Fig. 2: the use of `C!n` and `C#n` to mean different classes is very confusing, especially because you also have `D#N`, which is also a (different) class. Why do you use C some of the time to mean a class, but D other times? I found this entire discussion of syntax deeply unintuitive, even though the thing you're presenting is really quite simple.

**Reply:** To the best of my understanding, the use of different letters in the same rule (example: `C!n` extends `D#N`) represents different class names, making it explicit that `C` and `D` are different class names. Because class `C` cannot extend itself, we use different namings in the rule.

The confusion between C!n and C#N may be caused by the use of the same letter `n` and `N`. They are given different symbols because n represents a single version number and N represents a list of versions.

5. 309: to some extent, it seems like a coincidence that this is compatible. What if `Rectangle!2` had changed the name of its x getter from `x()` to `getX()`? I think that would have caused a type error, but that seems...difficult...to explain to a programmer.

**Reply:** If the getter `x()` in `Rectangle!2` are all replaced with `getX()`, it is true that it would have caused a type error. Such situation indicates that the equal method is not compatible with the previous version, hence it would not work in a multi-version environment, which unfortunately does not illustrate the capability of the language.
    
It would not necessarily be difficult to explain to a programmer. The error happens simply because the equal method in `Rectangle!2` requires the receiver and the argument to have `getX()`, which is not defined in Rectangle!1. This results in type error because Square does not have that method.

6. 326: you never discuss how difficult solving these constraints are at any point. I assume this reduces to SMT or something similar, so what happens when the solver gets stuck/times out? Is that raised to the user as a compile error? How is the programmer supposed to fix that?
Answer: The application we have implemented so far has not encountered time out during solving. The size of the implementation reached 1000 lines of code. We plan to add compilation time measure in the revision. While the problem in scaling it to real-world program will also be included in our future work.

**Reply:** 

7. 329: please put the prose in 3.1 in the same order as Fig 2, here and elsewhere where you describe the contents of a figure
Answer: We move the explanation for paths to a separate subsection before syntax in the revision.

**Reply:** We add a subsection for *paths* before explaining the syntax.

8. 341: CT doesn't appear to be part of the syntax, so why is it mentioned here at all?

**Reply:** The condition `n` $\in$ $dom$(CT(`C`)) describes the same condition as `class C!n extends D#N {...}`. We will remove it in the revision.

9. 345: you have not defined "path variable" yet

**Reply:**  The explanation for path is moved before the syntax rules.

10. 346: avoid telling the reader what "needs" to happen. Just say "note..."

**Reply:** Remove the unnecessary phrases.

11. 349: this is still in the subsection labeled "syntax", but I think it's part of the semantics of the type system? Unclear structure here.

**Reply:** Moved the explanation for paths to a separate subsection before syntax.

12. Fig. 6: the second rule from the top says that a type is a subtype of any valid instantiation of its superclass, right? But, shouldn't the type only be a subtype of the concrete superclass and its compatible-with-the-actual-class versions? The rule is unclear and the text does not help.

13. 464: I think this implies that no type variable can be a subtype of another type variable. That significantly reduces the expressiveness of the language, no?

**Reply:** Within the limited syntax that FBJ has, there is no subtype check between two type variables. Subtype check is used to check whether arguments’ types are subtypes of method and constructor parameters (declared as `C#N`), whether a cast (declared as `C#N`) is a supertype of an expression, and whether a method body is a subtype of the method return type (declared as `C#N`).
Additionally, a subtype check between two type variables has no meaning as both type variables can be any type within the program.

14. 471: do something to highlight the differences for the reader, then

**Reply:** For simplicity, we will revise the rules so that all the expressions are typed with a type variable X#N, hence avoiding the confusion of having a constraint in the form of `{C=C}`.

15. 489: here and throughout, do not use the letter O as a variable: it looks too much like 0. Use Q for the thing after P.

**Reply:** Change it

16. Fig.8: this takes up a lot of space but looks extremely standard. You could cut some of this to add more eval at the end.

**Reply:** Pack them into two columns.

17. 612: this soundness theorem I guess makes some sense, but it doesn't tell me what I want to know, which is that your system guarantees that some kind of mistake is impossible (but, your system doesn't do this, so it's not clear what you mean by "soundness")

**Reply:** 

18. 689: it is not intuitive why you need to generate constraints on primitives at all, since they can't have versions. I think the reason, as you mention later in the text, is that constraints on method parameters and return types might require them to be certain kinds of primitives.

**Reply:** Method invocations with different return primitive types require constraints to primitive types. On the other hand, it’s true that primitive type annotations do not require any constraints.

Note: The implementation does not need to generate constraints on primitive type annotations.

19. 743: you describe in text what I think should be another constraint ("Square!1's superclass and X2' have to be the same")

**Reply:** The constraint `{X1=C and X2’=C}` is equivalent to `{X1=C and X2’=X1}`. We do not need `{X2’=X1}` as long as we always give both variables the same type in the constraint.

20. 751: be explicit that this means the constraint solver must find all solutions, not just one that satisfies the constraints. I'm sure this has performance implications for your tool.

**Reply:** It is true that finding the whole set of solutions with a large number of versions can cause the solver to exceed the memory limit to find all the solutions. The current feature provided by the prototype is limiting the versions of imported versions.

A future approach is by specifying the priority constraint for each type variable. The default priority can be set to the latest version or a certain range of version that the users choose.

21. 804: if you tried to do this with a real Java program, you'd probably quickly start running into the bytecode class size limit. You should mention that that means this will probably never be practical without some way to handle that, due to the size blowup. (forward ref to FW is okay)

**Reply:** Yes, the number of constraints and solutions grow exponentially in regard to the program size, which would be impractical with large programs. The prototype and the formalization here serve as a base from which we plan to optimize the inference and compilation to make it more feasible, something that we will mention later on in future work.

In reality, programmers do not require the whole history of a library in their programs. Therefore, as long as we can properly specify the range of versions that programmers may require, we can limit how much code is generated.

22. 886: No attempts here to explain why what these other things have done is different than what you have done. This reads like a list of other papers you know, not a comparison between your work and related work.

**Reply:** We will follow the suggestion to elaborate on the difference more between our work and previous work.

23. 920: why does being in the language matter? in my experience, Maven shadowing in Java works just fine.

**Reply:** Maven shadowing can cause issues to downstream users when common transitive dependencies are packaged together in the jar. Suppose the downstream `C` requires `D` and `E`. `D` is also shaded and packaged together with `E`. Problem can happen when `C` attempts to pass `D` from its direct dependency to `E` because `D` is already renamed in `E`.

By having version in the language, different versions of `D` (required by `E` and required by `C`) can coexist and be distinguished as a different implementation of `D`.