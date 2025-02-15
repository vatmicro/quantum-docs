---
author: bradben
description: Learn about the Quantum Development Kit (QDK), the Q# programming language, and how you can create quantum programs.
ms.author: brbenefield
ms.date: 11/15/2021
ms.service: azure-quantum
ms.subservice: qdk
ms.topic: overview
no-loc: ['Q#', '$$v']
title: What are the Q# programming language & QDK?
uid: microsoft.quantum.overview.q-sharp
---

# What are the Q# programming language and the Quantum Development Kit?

Q# is Microsoft’s **open-source** programming language for developing and running quantum algorithms. It’s part of the Quantum Development Kit (QDK), an SDK which offers a set of tools that will assist you in the quantum software development process. The Quantum Development Kit provides:

- Python packages to submit Qiskit and Cirq applications to the Azure Quantum service  
- The Q# programming language and libraries 
- The IQ# kernel for running Q# on Jupyter Notebooks 
- Azure CLI extension to manage the Azure Quantum service and submit Q# applications 
- APIs for using Python and .NET languages (C#, F#, and VB.NET) with Q# 
- Extensions for Visual Studio Code and Visual Studio 

With the Quantum Development Kit, you can build programs that run on quantum hardware or formulate problems that run on quantum-inspired solvers in [Azure Quantum](xref:microsoft.quantum.azure-quantum-overview), an open cloud ecosystem with a diverse set of quantum solutions and technologies. The QDK offers support for Q#, [Qiskit](xref:microsoft.quantum.quickstarts.computing.qiskit), and [Cirq](xref:microsoft.quantum.quickstarts.computing.cirq) for quantum computing, so if you are already working in other development languages you can also run your circuits on Azure Quantum. 

> [!Tip]
> **Free trial.** If you don’t have an Azure subscription, you can [create an Azure free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) (check out free Azure accounts [for students](https://azure.microsoft.com/free/students/)). With Azure you can create, deploy, and manage applications across multiple clouds, on-premises, and at the edge. You will get 200 USD Azure credit to use in other Azure services. 

You can write, run, and test programs using the [online Jupyter notebooks](xref:microsoft.quantum.get-started.notebooks) available in your Azure Quantum workspace, and from your [local computer](xref:microsoft.quantum.install-qdk.overview.standalone). QDK supports interoperability with classical languages such as Python, C# and F#. Therefore, Q# programs can run as standalone programs or be called from other host program. 

As a programming language, Q# draws familiar elements from Python, C#, and F#, and supports a basic procedural model for writing programs with loops, if/then statements, and common data types. It also introduces new quantum-specific data structures and operations. The QDK allows integration of quantum and classical computation. In particular, classical flow control is based on quantum measurement outcomes. This is required to support quantum patterns such as repeat-until-success and adaptive phase estimation.


> [!Tip]
> Get started with the Q# language:
> - Run Q# in Azure portal (no installation required) 
>   - [Get started with Q# and an Azure Quantum notebook](xref:microsoft.quantum.get-started.notebooks)
> 
> - Install and run QDK locally
>   - [Set up a Q# and Python development environment](xref:microsoft.quantum.install-qdk.overview.python)
>   - [Set up a standalone Q# development environment](xref:microsoft.quantum.install-qdk.overview.standalone)
>
> - [Q# API reference documentation](xref:microsoft.quantum.apiref-intro)


## The quantum programming language Q\#

Why a quantum programming language? In short, because you want to write algorithms, not circuits.

A quantum program can be seen as a particular set of classical subroutines which, when called, perform a computation by interacting with a quantum system; a program written in Q# does not directly model the quantum state, but rather describes how a classical control computer interacts with qubits. This allows you to be entirely agnostic about what a quantum state even *is* on each target machine, which might have different interpretations depending on the machine. You can write your code once and, with little to no change, run it against multiple targets of the same family, allowing you to focus your programming at the algorithm level.

To learn more about the QDK features and the general pieces that fit within a Q# program, see [the Q# quantum programming language user guide](xref:microsoft.quantum.user-guide-qdk.overview).

### Integration with quantum and classical computation

Q# is a standalone language offering a high level of abstraction. There is no notion of a quantum state or a circuit; instead, Q# implements programs in terms of statements and expressions, much like classical programming languages. Distinct quantum capabilities (such as support for functors and control-flow constructs) facilitate expressing, for example, phase estimation and quantum chemistry algorithms.

For example, the following Q# program constructs a Hamiltonian describing the Hydrogen molecule, and obtains estimates of its energy levels by simulating the quantum phase estimation algorithm.

```qsharp
namespace Microsoft.Quantum.Chemistry.Samples.Hydrogen {
    open Microsoft.Quantum.Intrinsic;
    open Microsoft.Quantum.Canon;
    open Microsoft.Quantum.Chemistry.JordanWigner;
    open Microsoft.Quantum.Simulation;
    open Microsoft.Quantum.Characterization;
    open Microsoft.Quantum.Convert;
    open Microsoft.Quantum.Math;

    operation GetEnergyByTrotterization (qSharpData : JordanWignerEncodingData, nBitsPrecision : Int, trotterStepSize : Double, trotterOrder : Int) : (Double, Double) {

        // The data describing the Hamiltonian for all these steps is contained in
        // `qSharpData`
        let (nSpinOrbitals, fermionTermData, statePrepData, energyOffset) = qSharpData!;

        // Using a Product formula, also known as `Trotterization` to
        // simulate the Hamiltonian.
        let (nQubits, (rescaleFactor, oracle)) = TrotterStepOracle(qSharpData, trotterStepSize, trotterOrder);

        // The operation that creates the trial state is defined below.
        // By default, greedy filling of spin-orbitals is used.
        let statePrep = PrepareTrialState(statePrepData, _);

        // Using the Robust Phase Estimation algorithm of Kimmel, Low and Yoder.
        let phaseEstAlgorithm = RobustPhaseEstimation(nBitsPrecision, _, _);

        // This runs the quantum algorithm and returns a phase estimate.
        let estPhase = EstimateEnergy(nQubits, statePrep, oracle, phaseEstAlgorithm);

        // Obtaining the energy estimate by rescaling the phase estimate with the trotterStepSize. We also add the constant energy offset
        // to the estimated energy.
        let estEnergy = estPhase * rescaleFactor + energyOffset;

        // This returns both the estimated phase, and the estimated energy.
        return (estPhase, estEnergy);
    }
}
```

This operation makes use of the [Q# libraries](xref:microsoft.quantum.libraries.overview), in particular the [Quantum Chemistry Library](xref:microsoft.quantum.libraries.overview-chemistry.concepts.overview), and will return a tuple of the estimated phase and energy of the ground state of the molecule. 

The Q# language supports the integration of rich classical and quantum computing. This allows clean expression of adaptive algorithms such as the [random walk phase estimation operation](xref:Microsoft.Quantum.Research.Characterization.RandomWalkPhaseEstimation) that are difficult to express directly in the circuit model of a fixed sequence of quantum gates.

Q# supports general classical flow control during the execution of an algorithm. In particular, classical flow control is based on quantum measurement outcomes, which makes it much easier to write things that depend on intermediate measurements. For instance, the loop required for probabilistic algorithms such as Grover search can easily be expressed in Q#, rather than having to return to the classical driver to test whether the result satisfies the oracle and rerunning if not. 

### Qubits as opaque references

In Q#, qubits are a resource that are requested from the runtime when needed and returned when no longer in use. This is similar to the way that classical languages deal with heap memory.

In Q# qubits are modeled as opaque data types that represent a reference to a specific two-state quantum system, whether physical or logical (error-corrected), on which quantum operations may be performed. This is an operational view of qubits - that is, qubits are defined by what you can do to them. 

The mapping from a qubit variable in a program to an actual logical or physical qubit is decided by the runtime, and that mapping may be deferred until after the topology and other details of the target device is known. The runtime is responsible for determining a mapping that allows the algorithm to execute, including any qubit state transfer and remapping required during execution.

The representation used in Q# has the interesting implication that all of the actual quantum computing is done by side effect. There is no way to directly interact with the quantum state of the computer; it has no software representation at all. Instead, you perform operations on qubit entities that have the side effect of modifying the quantum state. Effectively, the quantum state of the computer is an opaque global variable that is inaccessible except through a small set of accessor primitives (measurements) — and even these accessors have side effects on the quantum state, and so are really “mutators with results” rather than true accessors.

### Respect the laws of physics

Quantum programs should be required to respect the laws of physics. For example, copying the state of a qubit or directly accessing the qubit state are not possible in Q#. 

Therefore, Q# has no ability to introspect into the state of a qubit or other properties of quantum mechanics directly, which guarantees that a Q# program can be physically executed on any quantum computer. Instead, a Q# program has the ability to call operations and functions, such as <xref:Microsoft.Quantum.Diagnostics.DumpOperation>, to extract classical information from a qubit, that allow validation and state examination to facilitate debugging with a simulator. For more information, see [testing and debugging](xref:microsoft.quantum.user-guide-qdk.overview.testingdebugging).


### Hardware agnostic

Q# is hardware agnostic, meaning that it provides the means to express and leverage powerful quantum computing concepts independently of how hardware evolves in the future. To be useable across a wide range of applications, Q# allows you to build reusable components and layers of abstractions. To achieve performance with growing quantum hardware size, the Q# quantum programming language ensures the scalability of both applications and development effort. Even though the full complexity of such computations requires further hardware development, Q# programs can be targeted to run on various [quantum hardware backends](xref:microsoft.quantum.reference.qc-target-list) in Azure Quantum.

### Efficient execution

The Q# language is focused on expressing information to optimize the execution. The goal is to ensure an efficient execution of quantum components, independent of the context within which they are invoked. Q# allows the developer to communicate their knowledge about a computation so that the compiler can make an informed decision regarding how to translate it into instructions, leveraging information about the end-to-end application that is not available to the developer.

## What can I do with the Quantum Development Kit?

The Quantum Development Kit is a full-featured development kit for Q# that you can use with common tools and languages to develop quantum applications that you can run in various environments. A Q# program can compile into a standalone application, or be called by a host program that is written either in Python or a .NET language.

When you compile and run the program, it creates an instance of the quantum simulator and passes the Q# code to it. The simulator uses the Q# code to create qubits (simulations of quantum particles) and apply transformations to modify their state. The results of the quantum operations in the simulator are then returned to the program. Isolating the Q# code in the simulator ensures that the algorithms follow the laws of quantum physics and can run correctly on quantum computers.

The following diagram shows the stages through which a quantum program goes from idea to complete implementation on Azure Quantum, and the tools offered by the QDK for each stage.

![qdk workflow](~/media/quantum-development-kit-flow-diagram.svg)

> [!Note]
> You use the same Q# code for all steps of the workflow. In the short term you might have to tweak some portions of the code to account for the current hardware limitations. But in the long run you’ll be able to switch between various simulators and hardware providers without any code modifications.

### Write your quantum program

If you want to start practicing and writing your Q# programs without installing additional software, you can use the [hosted Jupyter Notebooks](xref:microsoft.quantum.get-started.notebooks) available in your Azure Quantum workspace in the Azure portal. The sample gallery contains a collection of annotated notebook samples - select the sample you want to explore and run it on cloud-based simulators or real quantum computers.

If you prefer a local development environment, you can [create your Q# program](xref:microsoft.quantum.install-qdk.overview.standalone) using Jupyter Notebooks with the IQ# kernel, or the QDK extensions for Visual Studio Code and Visual Studio 2022, and then run them against quantum hardware or local simulators. 

Whichever environment you prefer, you can follow the Q# tutorials and start writing quantum programs to explore quantum concepts such as [superposition](xref:microsoft.quantum.tutorial-qdk.random-number), [entanglement](xref:microsoft.quantum.tutorial-qdk.entanglement), [Grover's quantum algorithm](xref:microsoft.quantum.tutorial-qdk.grovers), and other quantum phenomena.

If you don't have an Azure account, and you want to try Q# without installing the QDK locally, you can use [Binder to run the Q# samples](xref:microsoft.quantum.install-qdk.overview.binder) in Jupyter Notebooks online. 

### Explore domain-specific libraries

The [Q# libraries](xref:microsoft.quantum.libraries.overview) will help you keep your code high level, enabling you to run complex quantum operations without having to design low-level operation sequences. New Q# projects automatically include the Q# [standard library](xref:microsoft.quantum.libraries.overview.standard.intro), which provides a set of essential and very useful functions and operations that can be used when writing quantum programs in Q#.

In addition to the standard library, the QDK includes a [quantum chemistry library](xref:microsoft.quantum.libraries.overview-chemistry.concepts.overview) for simulating quantum dynamics and electronic structure problems on quantum computers, a [quantum machine learning library](xref:microsoft.quantum.libraries.overview#quantum-machine-learning-library) which provides an implementation of the sequential classifiers that take advantage of quantum computing to run hybrid quantum/classical machine learning experiments, and a [quantum numeric library](xref:microsoft.quantum.libraries-numerics.usage) which provides support for a wide range of numerics functionality.


### Run programs in simulators

Once you’ve written your program, the QDK offers a set of [quantum simulators](xref:microsoft.quantum.machines.overview) – classical programs that simulate the behavior of a quantum system, so that you can run a small instance of your program and see what it does without actual hardware access. You can run your quantum programs on a [full-state quantum simulator](xref:microsoft.quantum.machines.overview.full-state-simulator), a limited-scope [Toffoli simulator](xref:microsoft.quantum.machines.overview.toffoli-simulator), a [sparse simulator](xref:microsoft.quantum.machines.overview.sparse-simulator) for systems with larger number of qubits, and even use the [noise simulator](xref:microsoft.quantum.machines.overview.noise-simulator) for simulating the behavior of Q# programs under the influence of noisy environments. 

Before running on quantum hardware, you’ll need to figure out whether your program can run on existing hardware, and how many resources it will consume. You can use the [resource estimator](xref:microsoft.quantum.machines.overview.resources-estimator) to tell you how many qubits and quantum gates you need, and how long your program will take, and the [trace simulator](xref:microsoft.quantum.machines.overview.qc-trace-simulator.intro) to do an advanced analysis of resources consumption and  debug classical code that is part of your quantum program.

See the full list of [quantum simulators](xref:microsoft.quantum.machines.overview).

### Submit jobs to the Azure Quantum service

[Azure Quantum](xref:microsoft.quantum.azure-quantum-overview) is the cloud quantum computing service of Azure, with a diverse set of quantum solutions and technologies. Azure Quantum ensures an open, flexible, and future-proofed path to quantum computing that allows you to run your program on quantum hardware. You can run your Qiskit, Cirq, and Q# programs on multiple quantum systems.

[!INCLUDE [Azure Quantum credits banner](includes/azure-quantum-credits.md)]

Once you’ve created an Azure Quantum workspace, you can submit your Q# programs (also known as jobs) to Azure Quantum through your preferred development enviroment, both locally and online. For more information, see [how to submit Q# jobs](xref:microsoft.quantum.submit-jobs). You can also run and submit quantum circuits written in [Qiskit](xref:microsoft.quantum.quickstarts.computing.qiskit) or [Cirq](xref:microsoft.quantum.quickstarts.computing.cirq). 

The following diagram shows the basic workflow after you submit your job:

![azure quantum job flow](~/media/azure-quantum-flow-diagram-providers.svg)

Azure Quantum offers some of the most compelling and diverse quantum resources available today from industry leaders. With Azure Quantum and the QDK you can write [quantum computing](xref:microsoft.quantum.overview.understanding) and [quantum-inspired optimization](xref:microsoft.quantum.optimization.concepts.overview.introduction) programs, and submit them to Azure Quantum to run on [quantum hardware](xref:microsoft.quantum.reference.qc-target-list) and [optimization solvers](xref:microsoft.quantum.reference.qio-target-list). 


## How can I use the Quantum Development Kit?

Everything you need to write and run Q# programs, including the Q# compiler, the Q# libraries, and the quantum simulators, is pre-deployed in the hosted Jupyter Notebooks in the Azure portal. The QDK can also be installed and run from your local computer, so you can use your preferred IDE and language locally and submit jobs to Azure Quantum, or work with local simulators only.

The Quantum Development Kit includes extensions for [Visual Studio 2022](https://marketplace.visualstudio.com/items?itemName=quantum.DevKit64) and [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=quantum.quantum-devkit-vscode), and integration for [Jupyter Notebooks with IQ#](xref:microsoft.quantum.install-qdk.overview.standalone). You can also use the QDK for quantum computing with [Binder environment](xref:microsoft.quantum.install-qdk.overview.binder) and with Docker image. 

The Quantum Development Kit supports interoperability with [Python](xref:microsoft.quantum.install-qdk.overview.python) and other [.NET languages](xref:microsoft.quantum.install-qdk.overview.cs), as well as integration with [Qiskit](xref:microsoft.quantum.quickstarts.computing.qiskit) and [Cirq](xref:microsoft.quantum.quickstarts.computing.cirq), so quantum developers that are already working in other development languages can also run their circuits on Azure Quantum.

For more information, see how to [configure your quantum development environment](xref:microsoft.quantum.install-qdk.overview).

- Running a Q# program in a Jupyter Notebook in the [Azure Quantum](xref:microsoft.quantum.azure-quantum-overview) portal is the quickest way to get started (an [Azure account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) is needed).

- You can develop Q# programs as standalone applications in a [local development environment](xref:microsoft.quantum.install-qdk.overview.standalone). 

- If you are familiar with Python, you can use it as a [host programming platform](xref:microsoft.quantum.install-qdk.overview.python) to get started. Python enjoys widespread use not only among developers, but also scientists, researchers and teachers.

- If you already have experience with [C#, F#, or VB.NET](xref:microsoft.quantum.install-qdk.overview.cs) and are familiar with the Visual Studio development environment, there are just a few extensions you need to add to Visual Studio to prepare it for Q#.  



## Next Steps

If you want to learn more, the [Quantum Katas](xref:microsoft.quantum.tutorial-qdk.katas) provide a good introduction to [quantum computing concepts](xref:microsoft.quantum.tutorial-qdk.katas#introduction-to-quantum-computing-concepts) such as common quantum operations and how to manipulate qubits.

- [QuickStarts](xref:microsoft.quantum.install-qdk.overview)
- [Understanding quantum computing](xref:microsoft.quantum.overview.understanding)
- [The Q# User Guide](xref:microsoft.quantum.user-guide-qdk.overview)
- [Linear algebra for quantum computing](xref:microsoft.quantum.overview.algebra)
