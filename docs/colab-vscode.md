# Run local notebooks on a Colab GPU in VS Code

Keep the `.ipynb` file in this project. VS Code edits and saves it locally;
the selected Colab kernel executes its cells on Google's servers.

## Connect

1. Install the official [Google Colab extension](https://marketplace.visualstudio.com/items?itemName=google.colab)
   and [Microsoft Jupyter extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter).
   This project recommends both. You can also run:

   ```sh
   code --install-extension google.colab --install-extension ms-toolsai.jupyter
   ```

2. Reload VS Code if the extension installation requests it, then open
   `Week_02/Lab/Week_02_gpus_with_pytorch.ipynb`.
3. Click the kernel name at the top right, then **Select Another Kernel… → Colab → New Colab Server**.
4. Complete Google authentication in the browser when prompted, then return to VS Code.
5. Choose **GPU** and an available GPU type, such as **T4**, in the server creation prompts.
   Select the Python kernel on that server. **Auto Connect** uses a default server;
   use **New Colab Server** when you need to choose hardware explicitly.
6. Run the first code cell. It prints CUDA availability and the GPU name, checks
   the environment, and runs a small calculation on `cuda:0`.

Kernel selection is stored by VS Code for each notebook; an `extensions.json`
recommendation or a notebook's `kernelspec` cannot authenticate or provision a
Colab server. Select Colab separately for other notebooks and after reconnecting
if VS Code asks. The project's `.venv` interpreter setting remains available for
local Python tooling; it does not override an explicitly selected Colab kernel.

## Verify before training

Run this as a notebook cell (the newlines are required):

```python
import torch
print(torch.cuda.is_available())
print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else "No GPU")
```

Expect `True` and an NVIDIA GPU name. Confirm the kernel picker identifies your
Colab server, rather than `.venv` or a local Python environment. If it prints
`False`, provision a GPU server and select its kernel before proceeding.

In training notebooks, explicitly place the model and each input batch on
`torch.device("cuda")`. The GPU lab already creates or transfers its GPU tensors
using `device='cuda'`; its CPU timing comparisons run on the **Colab server's CPU**.
Selecting a remote kernel does not automatically place every tensor on its GPU.
Use notebook cells for execution: the ordinary VS Code terminal and **Run Python
File / Run Code** still execute locally. There is no CPU or Mac MPS fallback in
the GPU lab's setup cell.

## Files, dependencies, and reconnecting

Local project files are not automatically available to the remote kernel.
Right-click required files/folders in Explorer and choose **Upload to Colab**;
view uploaded files under `/content` in the Colab sidebar's Contents view.
Install extra packages using `%pip install ...` in a connected notebook cell.
Save outputs/checkpoints from the remote server before removing it: server files
and installed packages are temporary, while saved local notebook outputs persist.

GPU access depends on account limits and availability. If unavailable, wait and
retry rather than selecting a local kernel. To change hardware, provision another
server with **New Colab Server** and select its kernel. Rerun setup cells after
switching; in-memory variables do not transfer. Use **Colab: Remove Server** when
finished with a server. For connection errors, inspect **View → Output → Colab**.

## Still seeing `.venv` or `No module named 'google'`?

The extension installation does not switch an already-open notebook's kernel.
Click **`.venv (Python …)` at the notebook's top right**, then **Select Another
Kernel…**, then **Colab**. Use this kernel picker to create/select a GPU server;
the Colab toolbar logo may open the Colab website while signed out.

If Colab is missing from the provider list, open Extensions (`Cmd+Shift+X`),
confirm **Google Colab** and **Jupyter** are enabled for this workspace, then run
**Developer: Reload Window** from the Command Palette (`Cmd+Shift+P`) and retry.
Changing **Python: Select Interpreter** only chooses a local Python interpreter.
Installing `google` or `google-colab` into `.venv` cannot provision a remote GPU.

The first cell now prints **Kernel Python** and **Kernel OS**. `Darwin` or a path
inside this project's `.venv` identifies local Mac execution. After selecting
Colab, rerun the first cell and expect `Linux`, `True`, an NVIDIA GPU name, and a
CUDA calculation on `cuda:0`.

References: [Google's VS Code extension announcement](https://developers.googleblog.com/google-colab-is-coming-to-vs-code/),
[official extension user guide](https://github.com/googlecolab/colab-vscode/wiki/User-Guide),
[Colab resource limits](https://research.google.com/colaboratory/faq.html#resource-limits).
