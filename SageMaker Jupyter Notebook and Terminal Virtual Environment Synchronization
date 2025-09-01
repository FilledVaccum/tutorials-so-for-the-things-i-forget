# SageMaker Jupyter Notebook and Terminal Virtual Environment Synchronization

## Overview

This tutorial addresses the common issue where a SageMaker Jupyter notebook and terminal appear to use the same virtual environment but actually have different package managers (pip) or environments. This can lead to package installation discrepancies and confusion about which environment is being used.

## The Problem

When working in SageMaker, you might encounter these issues:
- Jupyter notebook and terminal show different package lists despite using the same virtual environment
- Installing packages in terminal doesn't reflect in the notebook
- Different Python executables or pip versions between notebook and terminal

## Understanding Jupyter Kernels vs Terminal Environments

### Key Concepts:
- **Terminal Environment**: Directly uses the activated virtual environment's Python interpreter and tools
- **Jupyter Kernel**: A separate execution environment that needs to be explicitly linked to your virtual environment
- **ipykernel**: The Python package that enables Jupyter to use different Python environments as kernels

## Step-by-Step Solution

### Step 1: Create and Activate Virtual Environment

In the terminal:
```bash
# Create new conda environment
conda create -n your_env_name python=3.x

# Activate the environment
conda activate your_env_name
```

### Step 2: Install and Register Jupyter Kernel

```bash
# Install ipykernel in your virtual environment
pip install ipykernel

# Register your environment as a Jupyter kernel
python -m ipykernel install --user --name=your_env_name --display-name "Python (your_env_name)"
```

**Note**: This command can be run from any directory as long as the correct Python environment is activated.

### Step 3: Select Correct Kernel in Jupyter

1. In your SageMaker Jupyter interface
2. Go to **Kernel → Change kernel**
3. Select **"Python (your_env_name)"** from the dropdown
4. Restart the notebook if needed

### Step 4: Verification Commands

#### Terminal Verification:
```bash
which python
which pip
pip --version
echo $CONDA_DEFAULT_ENV
echo $CONDA_PREFIX
```

#### Notebook Verification:
```python
import sys
import os
import subprocess

print("Python executable:", sys.executable)
print("Python prefix:", sys.prefix)
print("Conda environment:", os.environ.get('CONDA_DEFAULT_ENV'))
print("Conda prefix:", os.environ.get('CONDA_PREFIX'))

# Check pip location
result = subprocess.run(['which', 'pip'], capture_output=True, text=True)
print("Pip location:", result.stdout.strip())

# Check pip version
result = subprocess.run(['pip', '--version'], capture_output=True, text=True)
print("Pip version:", result.stdout.strip())
```

## Common Issues and Solutions

### Issue 1: CONDA_DEFAULT_ENV Shows "base" in Notebook

**Symptoms:**
```python
# In notebook
print(os.environ.get('CONDA_DEFAULT_ENV'))  # Output: "base"
```

**Explanation:**
This is normal behavior. Jupyter kernels don't inherit shell environment variables. The important check is `sys.executable` which should point to your virtual environment.

**Verification:**
```python
import sys
print(sys.executable)  # Should show: /path/to/your_env/bin/python
```

### Issue 2: Different Pip Locations

**Problem:**
- Terminal: `/home/sagemaker-user/.conda/envs/your_env/bin/pip`
- Notebook: `/opt/conda/bin/pip`

**Solution 1 (Recommended): Use Environment-Specific Pip**
```python
import sys
import subprocess

# Always use pip from current Python environment
result = subprocess.run([sys.executable, '-m', 'pip', 'list'], 
                       capture_output=True, text=True)
print(result.stdout)
```

**Solution 2: Fix PATH in Notebook**
```python
import os
import sys

# Add environment's bin directory to PATH
env_bin = os.path.dirname(sys.executable)
current_path = os.environ['PATH']
if env_bin not in current_path:
    os.environ['PATH'] = f"{env_bin}:{current_path}"

# Verify fix
result = subprocess.run(['which', 'pip'], capture_output=True, text=True)
print("Fixed pip location:", result.stdout.strip())
```

### Issue 3: Different Package Lists

**Diagnostic:**
```bash
# Terminal
pip list | wc -l

# Notebook
!pip list | wc -l
```

**Solution:**
Use the environment-specific pip method shown above.

## Advanced Verification Techniques

### Package Installation Test
```bash
# In terminal
pip install requests-oauthlib
```

```python
# In notebook
try:
    import requests_oauthlib
    print("✓ Package found - same environment!")
except ImportError:
    print("✗ Package not found - different environments")
```

### Environment Consistency Check
```python
import sys
import subprocess
import os

def check_environment_consistency():
    """Check if notebook and terminal environments match"""
    
    print("=== Environment Consistency Check ===")
    
    # Python executable
    print(f"Python executable: {sys.executable}")
    
    # Python prefix
    print(f"Python prefix: {sys.prefix}")
    
    # Pip location using sys.executable
    result = subprocess.run([sys.executable, '-m', 'pip', '--version'], 
                           capture_output=True, text=True)
    print(f"Pip info: {result.stdout.strip()}")
    
    # Environment variables
    print(f"CONDA_PREFIX: {os.environ.get('CONDA_PREFIX', 'Not set')}")
    print(f"VIRTUAL_ENV: {os.environ.get('VIRTUAL_ENV', 'Not set')}")
    
    # PATH check
    path_entries = os.environ['PATH'].split(':')[:3]
    print("First 3 PATH entries:")
    for i, entry in enumerate(path_entries, 1):
        print(f"  {i}. {entry}")

check_environment_consistency()
```

## Best Practices

1. **Always use `sys.executable -m pip`** in notebooks instead of direct `!pip` commands
2. **Verify kernel selection** after creating new notebooks
3. **Install packages consistently** - if you install in terminal, use the same method in notebook
4. **Restart kernel** after major environment changes
5. **Use absolute paths** when in doubt about which executable is being used

## Troubleshooting Commands

### Reset Environment
```python
# Restart kernel: Kernel → Restart (recommended)
# Or reset PATH manually
import os
os.environ.pop('CUSTOM_PATH_ADDED', None)  # Remove custom variables
```

### List Available Kernels
```bash
jupyter kernelspec list
```

### Remove Kernel
```bash
jupyter kernelspec remove kernel_name
```

### Check Kernel Configuration
```bash
cat ~/.local/share/jupyter/kernels/your_env_name/kernel.json
```

## Revert Changes

### Method 1: Restart Kernel (Easiest)
- In Jupyter: **Kernel → Restart**
- This resets all environment variables to default state

### Method 2: Manual PATH Reset
```python
import os

# If you modified PATH, reset it
# (Note: This is session-specific and will reset on kernel restart anyway)
original_path = "/opt/conda/bin:/usr/local/bin:/usr/bin:/bin"  # Default SageMaker PATH
os.environ['PATH'] = original_path
```

### Method 3: Remove Custom Kernel
```bash
# List kernels
jupyter kernelspec list

# Remove specific kernel
jupyter kernelspec remove your_env_name
```

## Summary

The key to ensuring SageMaker Jupyter notebooks and terminal use the same virtual environment is:

1. **Create** the virtual environment in terminal
2. **Install ipykernel** in that environment
3. **Register** the environment as a Jupyter kernel
4. **Select** the correct kernel in Jupyter interface
5. **Verify** using `sys.executable` and environment-specific pip commands
6. **Always use** `sys.executable -m pip` in notebooks for consistency

By following these steps, you ensure true environment synchronization between your terminal and Jupyter notebook sessions in SageMaker.
