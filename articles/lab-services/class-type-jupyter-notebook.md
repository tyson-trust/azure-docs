---
title: Set up a data science lab with Python and Jupyter Notebooks
titleSuffix: Azure Lab Services
description: Learn how to set up a lab VM in Azure Lab Services to teach data science using Python and Jupyter Notebooks. 
services: lab-services
ms.service: lab-services
ms.custom: devx-track-python
author: ntrogh
ms.author: nicktrog
ms.topic: how-to
ms.date: 02/17/2023
---

# Set up a lab to teach data science with Python and Jupyter Notebooks

[!INCLUDE [preview note](./includes/lab-services-new-update-focused-article.md)]

This article outlines how to set up a [template virtual machine (VM)](./classroom-labs-concepts.md#template-virtual-machine) in Azure Lab Services with the tools for teaching students to use Jupyter Notebooks. You also learn how to lab users can connect to notebooks on their virtual machines.

[Jupyter Notebooks](https://jupyter-notebook.readthedocs.io/) is an open-source project that enables you to easily combine rich text and executable Python source code on a single canvas, known as a notebook. Running a notebook results in a linear record of inputs and outputs. Those outputs can include text, tables of information, scatter plots, and more.

## Lab configuration

[!INCLUDE [must have subscription](./includes/lab-services-class-type-subscription.md)]

### Lab plan settings

[!INCLUDE [must have lab plan](./includes/lab-services-class-type-lab-plan.md)]

This lab uses one of the Data Science Virtual Machine Azure Marketplace images as the base VM image. You first need to enable these images in your lab plan. This lets lab creators then select the image as a base image for their lab.

1. Follow these steps to [enable these Azure Marketplace images available to lab creators](specify-marketplace-images.md). Select one of the following Azure Marketplace images, depending on your OS requirements:

    - **Data Science Virtual Machine – Windows Server 2019**
    - **Data Science Virtual Machine – Ubuntu 18.04**
    
1. Alternately, create a custom VM image:

    The Data Science VMs images in the Azure Marketplace are already configured with Jupyter Notebooks. These images, however, also include many other development and modeling tools for data science. If you don't want those extra tools and want a lightweight setup with only Jupyter notebooks, create a custom VM image. For an example, [Installing JupyterHub on Azure](http://tljh.jupyter.org/en/latest/install/azure.html). 

    After you create the custom image, upload the image to a compute gallery to use it with Azure Lab Services. Learn more about [using compute gallery in Azure Lab Services](how-to-attach-detach-shared-image-gallery.md).

### Lab settings

1. Create a lab for your lab plan:

    [!INCLUDE [create lab](./includes/lab-services-class-type-lab.md)] Specify the following lab settings:

    | Lab settings | Value |
    | ------------ | ------------------ |
    | Virtual machine size | Select **Small** or **Medium** for a basic setup accessing Jupyter Notebooks. Select **Small GPU (Compute)** for compute-intensive and network-intensive applications used in Artificial Intelligence and Deep Learning classes. |
    | Virtual machine image | Choose **[Data Science Virtual Machine – Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)** or **[Data Science Virtual Machine – Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps?search=Data%20science%20Virtual%20machine&page=1&filters=microsoft%3Blinux)** depending on your OS needs. |
    | Template virtual machine settings | Select **Use virtual machine without customization.**.

1. When you create a lab with the **Small GPU (Compute)** size, follow these steps to [install GPU drivers](./how-to-setup-lab-gpu.md#ensure-that-the-appropriate-gpu-drivers-are-installed).

    These process installs recent NVIDIA drivers and the Compute Unified Device Architecture (CUDA) toolkit, which you need to enable high-performance computing with the GPU. For more information, see [Set up a lab with GPU virtual machines](./how-to-setup-lab-gpu.md).

## Template machine configuration

[!INCLUDE [configure template vm](./includes/lab-services-class-type-template-vm.md)]

The Data Science VM images come with many of data science frameworks and tools required for this type of class. For example, the images include:

- [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io/): A web application that allows data scientists to take raw data, run computations, and see the results all in the same environment. It runs locally in the template VM.  
- [Visual Studio Code](https://code.visualstudio.com/): An integrated development environment (IDE) that provides a rich interactive experience when writing and testing a notebook. For more information, see [Working with Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

The **Data Science Virtual Machine – Ubuntu** image is already provisioned with X2GO server to enable lab users to use a graphical desktop experience.

### Enabling tools to use GPUs

If you're using the **Small GPU (Compute)** size, we recommend that you verify that the Data Science frameworks and libraries are properly set up to use GPUs. You might need to install a different version of the NVIDIA drivers and CUDA toolkit. To configure the GPUs, you should consult the framework's or library's documentation.

For example, to validate that TensorFlow uses the GPU, connect to the template VM and run the following Python-TensorFlow code in Jupyter Notebooks:

```python
import tensorflow as tf
from tensorflow.python.client import device_lib

print(device_lib.list_local_devices())
```

If the output from the above code looks like the following, TensorFlow isn't using the GPU:

```python
[name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
locality {
}
incarnation: 15833696144144374634
]
```

Continuing with the above example, see [TensorFlow GPU Support](https://www.tensorflow.org/install/gpu) for guidance.  TensorFlow guidance covers:

- Required version of the [NVIDIA drivers](https://www.nvidia.com/drivers)
- Required version of the [CUDA Toolkit](https://developer.nvidia.com/cuda-toolkit-archive).
- Instructions to install [NVIDIA CUDA Deep Neural Network library (cudDNN)](https://developer.nvidia.com/cudnn).

After you've followed TensorFlow's steps to configure the GPU, when you rerun the test code, you should see output similar to the following output.

```python
[name: "/device:CPU:0"
device_type: "CPU"
memory_limit: 268435456
locality {
}
incarnation: 15833696144144374634
, name: "/device:GPU:0"
device_type: "GPU"
memory_limit: 11154792128
locality {
  bus_id: 1
  links {
  }
}
incarnation: 2659412736190423786
physical_device_desc: "device: 0, name: NVIDIA Tesla K80, pci bus id: 0001:00:00.0, compute capability: 3.7"
]
```

### Provide notebooks for the class

The next task is to provide lab users with notebooks that you want them to use. You can save notebooks locally on the template VM so each lab user has their own copy.

If you want to use sample notebooks from Azure Machine Learning, see [how to configure an environment with Jupyter Notebooks](/azure/machine-learning/how-to-configure-environment#jupyter-notebooks).

### Publish the template machine

You make the lab VM available for lab users, you have to [publish the template](how-to-create-manage-template.md#publish-the-template-vm). The lab VM has all the local tools and notebooks that you configured previously.

## Connect to Jupyter Notebooks

The following sections show different ways for lab users to connect to Jupyter Notebooks on the lab VM.

### Use Jupyter Notebooks on the lab VM

Lab users can connect from their local machine to the lab VM and then use Jupyter Notebooks inside the lab VM.

If you use a Windows-based lab VM, lab users can connect to their lab VMs through remote desktop (RDP). For more information, see how to [connect to a Windows lab VM](connect-virtual-machine.md#connect-to-a-windows-lab-vm).

If you use a Linux-based lab VM, lab users can connect to their lab VMs through SSH or by using X2Go. For more information, see how to [connect to a Linux lab VM](connect-virtual-machine.md#connect-to-a-linux-lab-vm).

### SSH tunnel to Jupyter server on the VM

For Linux-based labs, you can also connect directly from your local computer to the Jupyter server inside the lab VM. The SSH protocol enables port forwarding between the local computer and a remote server (in our case, the user's lab VM). An application that is running on a certain port on the server is **tunneled** to the mapping port on the local computer. 

Follow these steps to configure an SSH tunnel between a user's local machine and the Jupyter server on the lab VM:

1. Go to the [Azure Lab Services website](https://labs.azure.com)

1. Verify that the Linux-based [lab VM is running](how-to-use-lab.md#start-or-stop-the-vm).

1. Select the **Connect** icon > **Connect via SSH** to get the SSH connection command.

    The SSH connection command looks like the following:

    ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    Learn more about [how to connect to a Linux VM](connect-virtual-machine.md#connect-to-a-linux-lab-vm-using-ssh).

1. On your local computer, launch a terminal or command prompt, and copy the SSH connection string to it. Then, add `-L 8888:localhost:8888` to the command string, which creates the **tunnel** between the ports. 

    The final command should look as follows:

    ```shell
    ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus.cloudapp.azure.com
     ```

1. Press **ENTER** to run the command.
1. When prompted, provide the lab VM password to connect to the lab VM.
1. When you’re connected to the VM, start the Jupyter server using this command:

    ```bash
    jupyter notebook
    ```

    The command outputs a URL for the Jupyter server in the terminal. The URL should look like:

    ```output
    http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
    ```

1. Paste this URL into a browser on your local computer to connect and work on your Jupyter Notebook.

    > [!NOTE]
    > Visual Studio Code also enables a great [Jupyter Notebook editing experience](https://code.visualstudio.com/docs/python/jupyter-support). You can follow the instructions on [how to connect to a remote Jupyter server](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) and use the same URL from the previous step to connect from VS Code instead of from the browser.

## Cost estimate

This section provides a cost estimate for running this class for 25 lab users. There are 20 hours of scheduled class time. Also, each user gets 10 hours quota for homework or assignments outside scheduled class time. The VM size we chose was small GPU (compute), which is 139 lab units. If you want to use the Small (20 lab units) or Medium size (42 lab units), you can replace the lab unit part in the equation below with the correct number.  

Here's an example of a possible cost estimate for this class:
25 lab users \* (20 scheduled hours + 10 quota hours) \* 139 lab units \* 0.01 USD per hour = 1042.5 USD

>[!IMPORTANT]
>This cost estimate is for example purposes only. For current details on pricing, see [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## Conclusion

In this article, you learned how to create a lab for a Jupyter Notebooks class and how user can connect to their notebooks on the lab VM. You can use a similar setup for other machine learning classes.

## Next steps

[!INCLUDE [next steps for class types](./includes/lab-services-class-type-next-steps.md)]
