---
author: SoniaLopezBravo
description: Learn how to run your Q# programs on the Microsoft Quantum Development Kit full state simulator.
ms.author: sonialopez
ms.date: 10/11/2022
ms.service: azure-quantum
ms.subservice: qsharp-guide
ms.topic: conceptual
no-loc: ['Q#', '$$v', Quantum Development Kit, target, targets]
title: Full state quantum simulator 
uid: microsoft.quantum.machines.overview.full-state-simulator
---

# Quantum Development Kit (QDK) full state simulator

[!INCLUDE [Modern QDK banner](~/includes/new-qdk-support.md)]

The QDK provides a full state simulator that simulates a quantum machine on your local computer. You can use the full state simulator to run and debug quantum algorithms written in Q#, utilizing up to 30 qubits. The functionality of the full state simulator is similar to the quantum simulator used in the [LIQ$Ui|\rangle$](http://stationq.github.io/Liquid/) platform from Microsoft Research.

## Invoking and running the full state simulator

You expose the full state simulator via the `QuantumSimulator` class. For more information, see [Ways to run a Q# program](xref:microsoft.quantum.user-guide-qdk.overview.host-programs).

### Invoking the simulator from C\#

Create an instance of the `QuantumSimulator` class and then pass it to the `Run` method
of a quantum operation, along with any additional parameters.

```csharp
    using (var sim = new QuantumSimulator())
    {
        var res = myOperation.Run(sim).Result;
        ///...
    }
```

Because the `QuantumSimulator` class implements the <xref:System.IDisposable> interface, you must call the `Dispose` method once you don't need the instance of the simulator anymore. The best way to do it is to wrap the simulator declaration and operations within a [using](/dotnet/csharp/language-reference/keywords/using-statement) statement, which automatically calls the `Dispose` method.

### Invoking the simulator from Python

Use the [simulate()](/python/qsharp-core/qsharp.loader.qsharpcallable) method from the Q# Python library with the imported Q# operation:

```python
qubit_result = myOperation.simulate()
```

### Invoking the simulator from the command line

When running a Q# program from the command line, the full state simulator is the default target machine. Optionally, you can use the **--simulator** (or **-s** shortcut) parameter to specify the desired target machine. Both of the following commands run a program using the full state simulator.

```dotnetcli
dotnet run
dotnet run -s QuantumSimulator
```

### Invoking the simulator from Jupyter Notebooks

Use the IQ# magic command [%simulate](xref:microsoft.quantum.iqsharp.magic-ref.simulate) to run the Q# operation.

```qsharp
%simulate myOperation
```

## Seeding the simulator

By default, the full state simulator uses a random number generator to simulate quantum randomness. For testing purposes, it's sometimes useful to have deterministic results. In a C# program, you can accomplish this by providing a seed for the random number generator in the `QuantumSimulator` constructor via the `randomNumberGeneratorSeed` parameter.

```csharp
    using (var sim = new QuantumSimulator(randomNumberGeneratorSeed: 42))
    {
        var res = myOperationTest.Run(sim).Result;
        ///...
    }
```

## Simulator options

The behavior of the full state simulator can be adjusted via the following parameters to the C# constructor:

|Parameter|Description|
|------|-------|
|`throwOnReleasingQubitsNotInZeroState`| The simulator can warn you if qubits have not been returned to the `zero` state before release by throwing an exception. The default is `true`.|
|`randomNumberGeneratorSeed`| Obtain deterministic behavior by [seeding the simulator](#seeding-the-simulator).|
|`disableBorrowing`| If you don't want to use [borrowed qubits](xref:microsoft.quantum.qsharp.quantummemorymanagement#borrow-statement) for this simulation, you can disable this feature by setting this parameter to `true`. Borrowed qubits will instead be replaced with regular clean qubits. The default is `false`.|

> [!NOTE]
> Resetting or measuring qubits before release is required by the Q# spec - not doing so may lead to computational errors. 

The code below shows a possible configuration of the parameters.

```csharp
    var sim = new QuantumSimulator (
        throwOnReleasingQubitsNotInZeroState: false,
        randomNumberGeneratorSeed: 42,
        disableBorrowing: true
    )
```

## Configuring threads

The full state simulator uses [OpenMP](http://www.openmp.org/) to parallelize the linear algebra required. By default, OpenMP uses all available hardware threads, which means that programs with small numbers of qubits often run slowly because the coordination that is required dwarfs the actual work. You can fix this by setting the environment variable `OMP_NUM_THREADS` to a small number. As a rule of thumb, configure one thread for up to four qubits, and then one additional thread per qubit. You might need to adjust the variable depending on your algorithm.

## See also

- [Quantum sparse simulator](xref:microsoft.quantum.machines.overview.sparse-simulator)
- [Quantum Toffoli simulator](xref:microsoft.quantum.machines.overview.toffoli-simulator)
- [Quantum trace simulator](xref:microsoft.quantum.machines.overview.qc-trace-simulator.intro)
- [Quantum noise simulator](xref:microsoft.quantum.machines.overview.noise-simulator)
