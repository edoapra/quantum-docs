---
author: bradben
description: Learn how to submit Qiskit quantum circuits to the Azure Quantum service using an online notebook.
ms.author: brbenefield
ms.date: 12/01/2022
ms.service: azure-quantum
ms.subservice: qdk
ms.topic: quickstart
no-loc: ['Python', '$$v', target, targets]
title: 'Quickstart: Create Qiskit circuits with online notebooks'
zone_pivot_groups: quantum-computing-platforms-rigetti
uid: microsoft.quantum.quickstarts.computing.qiskit.portal
--- 

# Quickstart: Submit a circuit with Qiskit using an Azure Quantum notebook

Learn how to use the Azure Quantum service to submit a Qiskit quantum circuit to an IonQ, Quantinuum, or Rigetti quantum computing target. This example uses an Azure Quantum notebook and the built-in *azure-quantum* Python package - no installation or configuration is required. For more information, see [Quantum circuits](xref:microsoft.quantum.concepts.circuits).

## Prerequisites

- An Azure account with an active subscription. If you don’t have an Azure account, register for free and sign up for a [pay-as-you-go subscription](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go).
- An Azure Quantum workspace. For more information, see [Create an Azure Quantum workspace](xref:microsoft.quantum.how-to.workspace).

## Create a new Notebook in your workspace

1. Log in to the [Azure portal](https://portal.azure.com/) and select the workspace you created in the previous step.
1. In the left blade, select **Notebooks**.
1. Click **My Notebooks** and click **Add New**.
1. In **Kernel Type**, select **IPython**.
1. Type a name for the file, for example *Qiskit.ipynb*, and click **Create file**. 

When your new Notebook opens, it automatically creates the code for the first cell, based on your subscription and workspace information.

```py
from azure.quantum import Workspace
workspace = Workspace (
    subscription_id = <your subscription ID>, 
    resource_group = <your resource group>,   
    name = <your workspace name>,          
    location = <your location>        
    )
```

> [!NOTE]
> Unless otherwise noted, you should run each cell in order as you create it to avoid any compilation issues. 

Click the triangular "play" icon to the left of the cell to run the code. 

## Load the required imports

First, you'll need to import some additional modules. 

Click **+ Code** to add a new cell, then add and run the following code:

```python
from qiskit import QuantumCircuit
from qiskit.visualization import plot_histogram
from qiskit.tools.monitor import job_monitor
from azure.quantum.qiskit import AzureQuantumProvider
```

## Connect to the Azure Quantum service

Next, use an `AzureQuantumProvider` constructor to create a `provider` object that connects to your Azure Quantum workspace.  Add a new cell, but don't run it yet, with the following code:

```python
provider = AzureQuantumProvider(
  resource_id="",
  location=""
)
```

Before running this cell, your program needs the resource ID and the
location of your Azure Quantum workspace: 

1. Click **Save** to save your notebook.
1. Click **Overview** to view the workspace properties.
1. Hover over the **Resource ID** field and click the **Copy to clipboard** icon. 
1. Click **Notebooks** and open your Qiskit notebook. 
1. Paste the resource ID into the value for *resource_id*, and then add the location string from the first cell to *location*.
1. Run the cell.

 :::image type="content" source="media/azure-quantum-resource-id.png" alt-text="Screenshot of the overview pane showing how to retrieve the resource ID and location from an Azure Quantum workspace.":::

## Define a simple circuit

In a new cell, create a `circuit` object. This example is a simple quantum random bit generator. Add the following code to define and display the circuit:

```python
# Create a Quantum Circuit acting on the q register
circuit = QuantumCircuit(3, 3)
circuit.name = "Qiskit Sample - 3-qubit GHZ circuit"
circuit.h(0)
circuit.cx(0, 1)
circuit.cx(1, 2)
circuit.measure([0, 1, 2], [0, 1, 2])

# Print out the circuit
circuit.draw()
```

```html
     ┌───┐          ┌─┐      
q_0: ┤ H ├──■───────┤M├──────
     └───┘┌─┴─┐     └╥┘┌─┐   
q_1: ─────┤ X ├──■───╫─┤M├───
          └───┘┌─┴─┐ ║ └╥┘┌─┐
q_2: ──────────┤ X ├─╫──╫─┤M├
               └───┘ ║  ║ └╥┘
c: 3/════════════════╩══╩══╩═
                     0  1  2 
```

## List all targets

[!INCLUDE [Quantinuum target name update](includes/quantinuum-name-change.md)]

You can now display all of the quantum computing targets, or backends, that are
available in your workspace. Add a new cell and run the following line:

```python
print("This workspace's targets:")
for backend in provider.backends():
    print("- " + backend.name())
```

```output
This workspace's targets:
- ionq.qpu
- ionq.simulator
- ionq.qpu.aria-1
- microsoft.estimator
- quantinuum.qpu.h1-1
- quantinuum.sim.h1-1sc
- quantinuum.qpu.h1-2
- quantinuum.sim.h1-2sc
- quantinuum.sim.h1-1e
- quantinuum.sim.h1-2e
- rigetti.sim.qvm
- rigetti.qpu.aspen-m-2
- rigetti.qpu.aspen-m-3
```

::: zone pivot="platform-ionq"

[!INCLUDE [ionq-procedure](includes/quickstart-qiskit-include-ionq-portal.md)]

::: zone-end

::: zone pivot="platform-quantinuum"

[!INCLUDE [quantinuum-procedure](includes/quickstart-qiskit-include-quantinuum-portal.md)]

::: zone-end

::: zone pivot="platform-rigetti"

[!INCLUDE [rigetti-procedure](includes/quickstart-qiskit-include-rigetti-portal.md)]

::: zone-end

> [!IMPORTANT]
> Submitting multiple circuits on a single job is currently not supported. As a workaround you can call the `backend.run` method to submit each circuit asynchronously, then fetch the results of each job. For example:
>
> ```python
> jobs = []
> for circuit in circuits:
>     jobs.append(backend.run(circuit, shots=N))
> 
> results = []
> for job in jobs:
>     results.append(job.result())
>```

## Next steps

Looking for more samples to run? Check out the [samples directory](https://github.com/microsoft/Quantum/tree/main/samples/azure-quantum) for Azure Quantum.

Lastly, if you would like to learn more about writing Q# programs please see the [Q# programming language user guide](xref:microsoft.quantum.user-guide-qdk.overview).
