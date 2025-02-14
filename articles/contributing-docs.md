---
author: bradben
description: Learn how to contribute conceptual or API content to the Azure Quantum documentation set.
ms.author: brbenefield
ms.date: 10/28/2022
ms.service: azure-quantum
ms.subservice: qdk
ms.topic: contributor-guide
no-loc: ['Q#', '$$v', Quantum Development Kit, target, targets]
title: Contributing documentation
uid: microsoft.quantum.contributing-qdk.overview.docs
---

# Improving documentation

The documentation for Azure Quantum takes on several different forms, such that information is readily available to quantum developers.

Following the principles of [Docs as Code](https://www.writethedocs.org/guide/docs-as-code/), all Azure Quantum documentation (in fact, all Azure documentation) is formatted as code and is managed using Git in the same way as the source code that is used to build the Quantum Development Kit.
For the most part, the code backing documentation consists of various forms of [Markdown](https://daringfireball.net/projects/markdown/), a language for writing out richly formatted text in a plain text format that's easy to use at the command line, in IDEs, and with source control.
We similarly adopt the [MathJax](https://www.mathjax.org/) library to allow for formatting mathematics in documentation using the LaTeX language, as detailed further below.

That said, each form of documentation does vary somewhat in the details:

- The **conceptual documentation** consists of the set of articles that are published to [Microsoft Learn](xref:microsoft.quantum.azure-quantum-overview) and cover getting started with Azure Quantum, tutorials to learn Q#, and managing your quantum workspaces, along with deep dives into quantum computing history, the Azure Quantum libraries, and the Q# language. These articles are written in [DocFX-Flavored Markdown (DFM)](https://dotnet.github.io/docfx/spec/docfx_flavored_markdown.html), a Markdown variant used for creating rich documentation sets.
- The **API reference** is a set of topics for each Q# function, operation, and user-defined type. In addition to the Q# libraries, the reference includes APIs for the .NET, Python, IQ# and REST libraries, and ARM templates. These topics document the inputs and operations to each callable, along with examples and links to more information, and are accessed from the conceptual documentation table of contents. The API reference is automatically generated from small DFM documents in Q# source code as a part of each release.
- The **README<!---->.md** files included with each sample and kata describe how to use that sample or kata is used, what it covers, and how it relates to the rest of Azure Quantum. These files are written using [GitHub Flavored Markdown (GFM)](https://github.github.com/gfm/), a more lightweight alternative to DFM that's popular for attaching documentation directly to code repositories.

## Contributing to the conceptual documentation

To contribute an improvement to the conceptual or README documentation, then, starts with a pull request in the relevant GitHub repository. For more information about the Azure Quantum repositories, see [Contributing to Azure Quantum](xref:microsoft.quantum.contributing-qdk.overview#where-do-contributions-go). For more information about submitting a pull request, see [Opening pull requests](xref:microsoft.quantum.contributing-qdk.overview.pulls).

Here are a few things to keep in mind as you improve documentation:

- Readers come to the Quantum Development Kit documentation from a very wide range of backgrounds. Everyone from high school students looking to learn something new and exciting through to tenured faculty performing quantum computing research should be able to get something out of reading the documentation. To the extent that's possible, please don't assume extensive knowledge on the part of your readers, as they may just be starting out. It's most helpful if you can provide clear and accessible descriptions, or can provide links to other resources for more information.
- Documentation sets aren't laid out like books or papers, in that readers will arrive in what might seem like the "middle." For example, search engines might not suggest the index, or they might have been sent a link by a friend trying to help them out. Try to help your reader by always providing a clear context, along with links where appropriate.
- Some readers will find abstract statements and definitions most helpful, while other readers work best by extrapolating from concrete examples. Providing both the general case and specific examples can help both readers get the most out of quantum programming.
- Especially if you also wrote the code being documented, things may be obvious to you that are not at all obvious to your reader. There's no one unique best way to program, so no matter how clever or experienced a reader might be, they can't guess at what design patterns you found the most helpful to express your ideas in code. Being clear about how a reader can expect to make use of your code can help provide that context.
- Many members of the quantum programming community are academic researchers, and are recognized mainly through citations for their contributions to the community. In addition to helping readers find additional materials, making sure to properly cite academic outputs such as papers, talks, blog posts, and software tools can help academic contributors to keep doing their best work to improve the community.
- The quantum programming community is a broad and wonderfully diverse community. The use of gendered pronouns in third-person examples (for example: "if a user ..., they will ...") can work to exclude rather than include. Being cognizant of people's names in citations and links, and of the correct inclusion of non-ASCII characters can serve the diversity of the community by showing respect to its members. Similarly, many words in the English language are often used in a hateful manner, such that their use in technical documentation can cause harm both to individual readers and to the community at large.

### Referencing sample code from conceptual articles

If you want to include code from the [samples repository](https://github.com/Microsoft/Quantum), you can do so using a special DocFX-Flavored Markdown command:

```markdown
:::code language="qsharp" source="~/quantum/samples/algorithms/chsh-game/Game.qs" range="4-8":::
```

This command will import lines 4 to 8 of the [`Game.qs` file from the `chsh-game` sample](https://github.com/microsoft/Quantum/blob/main/samples/algorithms/chsh-game/Game.qs), marking them as Q# code for the purpose of syntax highlighting.
Using this command, you can avoid duplicating code between conceptual articles and the samples repository, so that sample code in documentation is always as up to date as possible.

### Contributing image files

**IMPORTANT**: To have the images rendering properly in dark mode you must avoid transparencies.

- For .jpg files. you don't need to do anything as the .jpg format doesn't support transparent elements.
- For .png files, you must add a white background or change the value of the alpha channel to **100**. The easiest way to do this in Windows is to open the file in **Paint** and save it, overwriting the original file.
- For .svg files you must add a white rectangle in the lowest layer. You can do this with **Inkscape**:
  1. Open the .svg file.
  1. Select the square maker tool and draw a white rectangle on top of the original figure.
  1. Select the tool **Select and transform objects** by clicking in the dark arrow or pressing **F1**.
  1. While having the rectangle selected, click the toolbar element **Lower selection to bottom (End)**.
  1. Adjust the rectangle with your mouse or the arrow keys.

## Contributing to the API references

To contribute an improvement to the API references, it's most helpful to open a pull request directly on the code being documented.
Each function, operation, or user-defined type supports a documentation comment (denoted with `///` instead of `//`).
When each release of the Quantum Development Kit is compiled, these comments are used to generate the API reference in [Q# API reference](/qsharp/api/), including details about the inputs to and outputs from each callable, the assumptions each callable makes, and examples of how to use them.

> [!IMPORTANT]
> Please make sure to not manually edit the generated API documentation, as these files are overwritten with each new release.
> Your contribution to the community is valued, and you want to make sure that your changes continue to help users release after release.

For example, consider the function `ControlledOnBitString<'T> (bits : Bool[], oracle : ('T => Unit is Adj + Ctl)) : ((Qubit[], 'T) => Unit is Adj + Ctl)`.
A documentation comment should help a user learn how to interpret `bits` and `oracle` and what the function is for.
Each of these different pieces of information can be provided to the Q# compiler by a specially named Markdown section in the documentation comment.
For the example of `ControlledOnBitString`, you might write something like the following:

```qsharp
 /// # Summary
 /// Returns a unitary operation that applies an oracle on the target register if the 
 /// control register state corresponds to a specified bit mask.
 ///
 /// # Description
 /// The output of this function is an operation that can be represented by a
 /// unitary transformation $U$ such that
 /// \begin{align}
 ///     U \ket{b_0 b_1 \cdots b_{n - 1}} \ket{\psi} = \ket{b_0 b_1 \cdots b_{n-1}} \otimes
 ///     \begin{cases}
 ///         V \ket{\psi} & \textrm{if} (b_0 b_1 \cdots b_{n - 1}) = \texttt{bits} \\\\
 ///         \ket{\psi} & \textrm{otherwise}
 ///     \end{cases},
 /// \end{align}
 /// where $V$ is a unitary transformation that represents the action of the
 /// `oracle` operation.
 ///
 /// # Input
 /// ## bits
 /// The bit string to control the given unitary operation on.
 /// ## oracle
 /// The unitary operation to be applied on the target register.
 ///
 /// # Output
 /// A unitary operation that applies `oracle` on the target register if the control 
 /// register state corresponds to the bit mask `bits`.
 ///
 /// # Remarks
 /// The length of `bits` and `controlRegister` must be equal.
 ///
 /// Given a Boolean array `bits` and a unitary operation `oracle`, the output of this function
 /// is an operation that performs the following steps:
 /// * apply an `X` operation to each qubit of the control register that corresponds to `false` 
 /// element of the `bits`;
 /// * apply `Controlled oracle` to the control and target registers;
 /// * apply an `X` operation to each qubit of the control register that corresponds to `false` 
 /// element of the `bits` again to return the control register to the original state.
 ///
 /// The output of the `Controlled` functor is a special case of `ControlledOnBitString` where `bits` is equal to `[true, ..., true]`.
 ///
 /// # Example
 /// The following code snippets are equivalent:
 /// ```qsharp
 /// ControlledOnBitString(bits, oracle)(controlRegister, targetRegister);
 /// ```
 /// and
 /// ```qsharp
 /// within {
 ///     ApplyPauliFromBitString(PauliX, false, bits, controlRegister);
 /// } apply {
 ///     Controlled oracle(controlRegister, targetRegister);
 /// }
 /// ```
 ///
 /// The following code prepares a state $\frac{1}{2}(\ket{00} - \ket{01} + \ket{10} + \ket{11})$:
 /// ```qsharp
 /// use register = Qubit[2];
 /// ApplyToEach(H, register);
 /// ControlledOnBitString([false], Z)(register[0..0], register[1]);
 /// ```
 function ControlledOnBitString<'T> (bits : Bool[], oracle : ('T => Unit is Adj + Ctl)) : ((Qubit[], 'T) => Unit is Adj + Ctl)
 {
     return ControlledOnBitStringImpl(bits, oracle, _, _);
 }
```

You can see the rendered version of the code above in the [API documentation for the `ControlledOnBitString` function](xref:Microsoft.Quantum.Canon.ControlledOnBitString).

In addition to the general practice of documentation writing, in writing API documentation comments it helps to keep a few things in mind:

- The format of each documentation comment has to match what the Q# compiler expects for your documentation to appear correctly. Some sections, such as `/// # Remarks` allow for freeform content, while sections such as `/// # See Also` section are more restrictive.
- A reader may read your API documentation on the main API reference site, on the summary for each namespace, or even from within their IDE through the use of hover information. Making sure that `/// # Summary` isn't longer than about a sentence helps your reader quickly sort out whether they need to read further or look elsewhere, and can help in quickly scanning through namespace summaries.
- Your documentation may well wind up being much longer than the code itself, but that's OK! Even a small piece of code can have unexpected effects to users that don't know the context in which that code exists. By providing concrete examples and clear explanations, you can help users make the best use of the code that's available to them.

<!-- ## LaTeX Formatting ##

**TODO** -->
