# SageMaker Jupyter Notebook Virtual Environment Troubleshooting

## Problem Identified
The SageMaker Jupyter notebook was using a different pip than the terminal, causing package list discrepancies:
• **Terminal**: Using rag-cookbook environment pip ✓
• **Notebook**: Using base conda pip ✗

## Root Cause
The notebook kernel was using the correct Python executable but had incorrect PATH configuration, pointing to base conda's pip instead of the virtual environment's pip.

## Diagnostic Commands

### Terminal Verification
bash
which python
which pip
pip --version


### Notebook Verification
python
import sys
import subprocess

print("Python:", sys.executable)
print("Pip:", subprocess.run(['which', 'pip'], capture_output=True, text=True).stdout.strip())
print("Pip version:", subprocess.run(['pip', '--version'], capture_output=True, text=True).stdout)


## Solution Applied

### Method 1: Use Python-specific pip (Recommended)
python
import sys
import subprocess

# Always use pip from the current Python environment
result = subprocess.run([sys.executable, '-m', 'pip', 'list'], capture_output=True, text=True)
print(result.stdout)


### Method 2: Fix PATH in notebook
python
import os
import sys

# Add environment's bin directory to PATH
env_bin = os.path.dirname(sys.executable)
current_path = os.environ['PATH']
if env_bin not in current_path:
    os.environ['PATH'] = f"{env_bin}:{current_path}"


## How to Revert

### Revert PATH changes (if Method 2 was used)
python
import os

# Reset PATH to original (restart kernel is easier)
# Or manually remove the added path
original_path = os.environ['PATH'].split(':', 1)[1]  # Remove first entry
os.environ['PATH'] = original_path


### Complete Reset
• Restart the Jupyter kernel: Kernel → Restart
• This will reset all environment variables to default state

## Prevention for Future
Always use sys.executable -m pip in notebooks instead of direct pip commands to ensure you're using the correct pip for the current Python environment.
