title: Installing python and setting up venv

## 🐍 Python Installation and Virtual Environment Setup on Windows

---

### 🔧 Installing Python on Windows

To begin using Python in a controlled and reproducible way on Windows, it is essential to install the language properly and set up the environment so that it works seamlessly from the command line.

The installation process starts by downloading the official installer from [python.org](https://www.python.org/downloads/windows/). One should select the latest **stable release** compatible with their system (usually the 64-bit installer). Upon launching the installer, it's critical to **check the option "Add Python to PATH"** before proceeding. This ensures that the Python interpreter can be invoked from any terminal window. Then, click on **"Customize installation"** and proceed with default settings, or specify the desired installation directory. After installation, click on **"Disable path length limit"** if prompted, as this allows the use of deep directory structures in projects.

---

### 🧪 Verifying Installation and Checking Version

Once installed, Python can be verified by opening the **Command Prompt** and typing:

```bash
python --version
```

This should output something like:

```
Python 3.12.2
```

If the command is not recognized, then either Python was not added to the system's `PATH`, or the system environment variables were misconfigured.

---

### ⚙️ Correcting PATH Manually (if needed)

To fix the `PATH`, navigate to **System Properties** → **Environment Variables**. Under either *User Variables* or *System Variables*, find the entry named `Path` and click **Edit**. Then **add the paths** corresponding to the Python installation and its Scripts directory. These are typically:

```
C:\Users\<yourname>\AppData\Local\Programs\Python\Python3x\
C:\Users\<yourname>\AppData\Local\Programs\Python\Python3x\Scripts\
```

Once added, click OK on all dialogs and restart the terminal. Running `python --version` again should now show the correct output.

---

## 🧰 What is `venv`?

`venv` stands for **Virtual Environment**, and it is a built-in Python module introduced in version 3.3. It provides a mechanism to create a lightweight, self-contained, and isolated Python environment within a directory. This environment includes its own Python interpreter and a copy of the standard `pip` installer, allowing users to install packages locally within the environment rather than globally on the system.

This isolation is crucial for modern software development. In practice, many Python projects depend on specific versions of third-party libraries. Installing all dependencies globally can quickly lead to **version conflicts** between projects. For instance, one project might require `Django 3.2` while another demands `Django 4.0`. By using `venv`, each project can maintain its own independent set of packages, preventing such collisions and ensuring reproducibility.

Furthermore, when using virtual environments, developers can safely experiment, upgrade, or downgrade packages without affecting system-wide installations or other projects. The environment also acts as a **reproducible context**: if one exports the list of dependencies via `pip freeze > requirements.txt`, then any other machine can reconstruct the same environment using that file and `pip install -r requirements.txt`.

---

### 📦 Installing and Activating `venv`

To create a virtual environment, navigate to your project directory and run:

```bash
python -m venv venv
```

This creates a folder named `venv/` containing the environment. To activate it on Windows:

```bash
venv\Scripts\activate
```

Once activated, the prompt will change to show `(venv)`, indicating that the shell is now using the virtual environment. All subsequent `pip install` commands will affect only this environment.

---

### 📜 Managing Dependencies with `requirements.txt`

After installing necessary packages within the environment, you can export them:

```bash
pip freeze > requirements.txt
```

This `requirements.txt` file serves as a manifest of all installed dependencies and their exact versions. On any other system, the same environment can be recreated by running:

```bash
pip install -r requirements.txt
```

This guarantees consistency across development, testing, and deployment environments.

