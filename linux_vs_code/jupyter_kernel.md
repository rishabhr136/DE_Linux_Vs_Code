How to setups devops kernel in my jupyter notebook
Yes. If by “DevOps kernel” you mean you want a Python environment called DevOps to appear in the Jupyter Notebook Select Kernel list, you can create/register it.

Option 1 — If DevOps is already a Python environment
First open Terminal / Command Prompt and check your environments:

conda env list

If you see something like:

devops
activate it:

conda activate devops

Then install the Jupyter kernel package:

python -m pip install ipykernel

Register it:

python -m ipykernel install --user --name devops --display-name "DevOps"

Now go back to your Jupyter Notebook:

Kernel → Change Kernel → DevOps

Option 2 — Create a new DevOps environment
If devops doesn't exist, use:

conda create -n devops python=3.10 -y

Then:

conda activate devops

Install the kernel:

python -m pip install ipykernel

Register it:

python -m ipykernel install --user --name devops --display-name "DevOps"

Then refresh/reopen Jupyter and select:

Select Kernel → DevOps

