---
author: SoniaLopezBravo
description: This document provides a list of the available quantum computing providers on Azure Quantum.
ms.date: 04/21/2022
ms.author: sonialopez
ms.service: azure-quantum
ms.subservice: core
ms.topic: reference
title: List of quantum computing targets on Azure Quantum
uid: microsoft.quantum.reference.qc-target-list
---

# Quantum computing providers on Azure Quantum

Azure Quantum offers various quantum solutions, such as different quantum hardware devices and quantum simulators that you can use to run your quantum computing programs. This topic has the latest capabilities available through Azure Quantum.

| Provider | Description |
|---|---|
|<img src="~/media/logo-ionq2.png" alt="logo of IonQ" title="logo of IonQ" width="200" height="200"/>|IonQ's trapped-ion gate-based quantum computers are universal and dynamically reconfigurable in software to use up to 11 qubits. All qubits are fully connected, meaning you can run a two-qubit gate between any pair. The implementation of quantum gate operations is done by manipulating Ytterbium ions with laser pulses. IonQ provides a GPU-accelerated quantum simulator supporting up to 29 qubits, using the same set of gates IonQ provides on its quantum hardware. For more information, see the [IonQ provider page](xref:microsoft.quantum.providers.ionq).|
|<img src="~/media/logo-quantinuum.svg" alt="logo of Quantinuum" title="logo of Quantinuum" width="200" height="200"/>| Quantinuum's trapped-ion quantum computers have high-fidelity, fully-connected qubits, and qubit reuse. Quantum operations are laser-based gates with low error rates, and have the ability to perform mid-circuit measurements. The System Model H1 generation of hardware, Powered by Honeywell, uses a Quantum Charge-Coupled Device (QCCD) architecture. Quantinuum provides an emulation tool, the System Model H1 Emulator, which contains detailed physical models and noise models of the actual quantum hardware. For more information, see the [Quantinuum provider page](xref:microsoft.quantum.providers.quantinuum). |

Quantum hardware devices are still an emerging technology. These devices have some limitations and requirements for quantum programs that run on them. For more information, see the [target profile types in Azure Quantum](xref:microsoft.quantum.target-profiles). 

## Coming soon to Azure Quantum

Azure Quantum is a platform for innovation. As the quantum hardware partners across the Azure Quantum ecosystem keep growing, you can explore these upcoming quantum hardware solutions.

| Provider | Description  |
|---|---|
|<img src="~/media/logo-pasqal.png" alt="logo of Pasqal" title="logo of Pasqal" width="200" height="200"/>|Pasqal's neutral atom-based quantum processors operating at room temperature have long coherence times and impressive qubit connectivity. You can pre-register today for Azure Quantum’s [private preview](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxm1OO5DJVRBs-fh9Rmd-nRURVRKVUJDM05WV1hDRlU2OFFZUlhUN1Q4SCQlQCN0PWcu) of Pasqal.|
|<img src="~/media/logo-rigetti.png" alt="logo of Rigetti" title="logo of Rigetti" width="200" height="200"/> | Rigetti's gate-based superconducting processors utilize Quantum Intermediate Representation (QIR) to enable low latency and parallel execution. You can pre-register today for Azure Quantum’s [private preview](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxm1OO5DJVRBs-fh9Rmd-nRURVRKVUJDM05WV1hDRlU2OFFZUlhUN1Q4SCQlQCN0PWcu) of Rigetti.|
|<img src="~/media/logo-qci.png" alt="logo of Quantum Circuits" title="logo of Quantum Circuits" width="200" height="200"/>| Quantum Circuits’ full-stack superconducting circuits have real-time feedback that enables error-correcting, encoding-agnostic entangling gates. You can pre-register today for Azure Quantum’s [private preview](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxm1OO5DJVRBs-fh9Rmd-nRURVRKVUJDM05WV1hDRlU2OFFZUlhUN1Q4SCQlQCN0PWcu) of QCI. |



