# How to install Amazon Q CLI in Sagemaker Studion Notebook Terminal

TL;DR : 
Step 1 : Follow the steps mentioned in this part of documentation - [Installing Amazon Q CLI using zip file](https://docs.aws.amazon.com/amazonq/latest/qdeveloper-ug/command-line-installing-ssh-setup-autocomplete.html)
Step 2 : Once you are at last step of Step 1 you need to run this command './q/install.sh' and you need to select appropriate response , I went through pro and I need to provide the Start URL which you will get in your AWS Amazon Q Developer Console - Go to Amazon Q Developer - Go to Amazon Q Developer console
I have already have user so I loggedin to authorize the approval, after that I followed step 3.
Step 3 : 

The Amazon Q CLI executable `q` exists in two locations on your system:

- `/home/sagemaker-user/.local/bin/q`
- `/home/sagemaker-user/q/bin/q`

***

## Why `q` command is not found?

The likely reason is that **`/home/sagemaker-user/.local/bin`** (and possibly `/home/sagemaker-user/q/bin`) is **not included in your shell's PATH environment variable**, so the shell cannot locate the `q` command.

***

## How to fix:

### 1. Add the directory containing `q` to your PATH

Run this command to temporarily add the common `~/.local/bin` to your PATH:

```bash
export PATH=$HOME/.local/bin:$PATH
```

You can verify by:

```bash
which q
q --version
```

***

### 2. To make the PATH change permanent, add it to your shell config

For Bash shell, append this line to `~/.bashrc` or `~/.bash_profile`:

```bash
echo 'export PATH=$HOME/.local/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

***

### 3. Alternatively, use the `q` in place directly without changing PATH

```bash
~/q/bin/q --version
~/q/bin/q chat
```

***

## Summary

| Issue                       | Solution                                    |
|-----------------------------|---------------------------------------------|
| `q` executable not in PATH  | Add `~/.local/bin` or `~/q/bin` to PATH     |
| Temporary test              | `export PATH=$HOME/.local/bin:$PATH`        |
| Permanent fix               | Add export line to `~/.bashrc`               |
| Run from path directly      | `~/q/bin/q` followed by commands             |

This will make `q` commands available in the CLI for Amazon Q CLI usage.[1]





Here is a detailed step-by-step guide to **install, verify, and enable Amazon Q CLI** in **Amazon SageMaker Studio Jupyter Notebook** (Amazon Linux 2 environment):

***

## Step 1: Open a Terminal in SageMaker Studio

- From the JupyterLab interface in SageMaker Studio, open a new **Terminal** tab.
- All commands below should be run in this terminal.

***

## Step 2: Install Required Dependencies

Amazon Q CLI requires some Linux packages. Run:

```bash
sudo yum update -y
sudo yum install -y unzip curl jq libfuse fuse
```

***

## Step 3: Download the Amazon Q CLI Linux Package

Download the latest Linux binary package from the Amazon Q CLI GitHub releases (replace with latest URL if changed):

```bash
curl -L -o q.zip https://github.com/aws/amazon-q-developer-cli/releases/latest/download/amazon-q-linux.zip
```

***

## Step 4: Verify Package Integrity (Optional but Recommended)

1. **Create public key file** (in a file named `amazon-q-pubkey.asc`) with the provided Amazon Q public key block (as per AWS docs).

2. **Import the public key:**

```bash
gpg --import amazon-q-pubkey.asc
```

3. **Download the signature file for the zip package:**

Example for Linux x86-64:

```bash
curl --proto '=https' --tlsv1.2 -sSf "https://desktop-release.q.us-east-1.amazonaws.com/latest/q-x86_64-linux.zip.sig" -o "q.zip.sig"
```

4. **Verify the signature:**

```bash
gpg --verify q.zip.sig q.zip
```

Look for “Good signature from Amazon Q command line Team”.

***

## Step 5: Extract the Package

```bash
unzip q.zip -d q
```

This creates a folder named `q/` containing the CLI binary and scripts.

***

## Step 6: Install Amazon Q CLI

Run the install script inside the extracted folder:

```bash
cd q
chmod +x install.sh
./install.sh
```

The script will ask some interactive questions like whether to modify your shell config. Approve to have the `q` command added to your PATH.

***

## Step 7: Reload Shell Environment

To activate the path changes:

```bash
source ~/.bashrc
```

***

## Step 8: Verify Installation

- Check the version:

```bash
q --version
```

- Run diagnostic to check installation integrity:

```bash
q doctor
```

***

## Step 9: Use Amazon Q CLI

You can now run commands such as:

```bash
q chat
q help
```

***

## Step 10: Using Amazon Q CLI inside Jupyter Notebook cells

To run Q CLI commands from notebook code cells, prefix the command with an exclamation mark `!`:

```python
!q --version
!q doctor
!q chat "Hello from Jupyter"
```

***

## Summary Table

| Step                  | Command / Action                                                           |
|-----------------------|----------------------------------------------------------------------------|
| Open Terminal         | From SageMaker Studio JupyterLab interface                                |
| Install deps          | `sudo yum install -y unzip curl jq libfuse fuse`                         |
| Download package      | `curl -L -o q.zip https://github.com/aws/amazon-q-developer-cli/releases/latest/download/amazon-q-linux.zip` |
| Verify package (opt)  | Import key, download sig, verify with `gpg --verify q.zip.sig q.zip`      |
| Extract package       | `unzip q.zip -d q`                                                        |
| Run installer         | `cd q && chmod +x install.sh && ./install.sh`                            |
| Reload shell          | `source ~/.bashrc`                                                        |
| Verify install        | `q --version` and `q doctor`                                              |
| Run commands          | `q chat`, `q help`                                                        |
| Use in notebook cells | Prefix CLI commands with `!`                                              |

***

This method ensures a robust, verified, and persistent installation of Amazon Q CLI inside SageMaker Studio Jupyter environment, allowing seamless CLI use both in terminal and notebooks.

If any issues occur, ensure the PATH includes your user local binaries and that dependencies are installed properly.
