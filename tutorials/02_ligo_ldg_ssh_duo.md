# Part 2: Accessing the LIGO Data Grid (LDG) clusters with SSH and Duo MFA

*Author: Suyog Garg, Dated: 2025/11/18*

---

## 1. Big picture

Before we touch any LIGO-specific system, it helps to have the right mental picture of terminologies.

- **Local machine**: your laptop or desktop, where you edit code, write papers, and run light tests.  
- **Remote cluster**: powerful computing resources in a data centre, accessed through the network by Secure Shell (SSH).  
- **Version control server**: Git hosting such as GitHub or `git.ligo.org`, which stores your code and sometimes also holds your SSH keys.

For LIGO and KAGRA work we add two more pieces:

- **IGWN identity**: your collaboration identity (LIGO.ORG or KAGRA account) used to authenticate.  
- **LIGO Data Grid (LDG)**: a distributed set of computing centres (CIT, LHO, LLO, Nemo, Gwave, IUCAA, etc.) that share the same authentication and software environment.

Our aim in this tutorial is to:

1. Learn basic SSH habits.  
2. Generate and manage SSH keys.  
3. Register keys with GitHub and with the LDG.  
4. Log in to the CIT cluster using Duo multi-factor authentication, both as a LIGO member and as KAGRA-only.  
5. Run Python code on the cluster.  
6. See how the same ideas apply to a local GPU cluster.

---

## 2. SSH basics

### 2.1 What is SSH?

SSH (Secure Shell) is a protocol and tool for opening an encrypted terminal session on a remote machine. Conceptually you type commands on your laptop, but they actually run on the remote host.

The basic syntax is:

```bash
ssh username@hostname
```

Examples:

```bash
# Log in to a generic university server
ssh alice@login.myuniversity.edu

# Log in to a LIGO cluster via the LDG portal
ssh albert.einstein@ssh.ligo.org
```

On first connection you will be asked to accept the server’s host key. Type `yes` once if you are connecting to the correct system.

### 2.2 Installing an SSH client

On most Unix-like systems an SSH client is already installed.

- **Linux**: `ssh` is usually available. Check with `ssh -V`.  
- **macOS**: the Terminal application has `ssh` preinstalled.  
- **Windows**:  
  - Option A: install “Git for Windows” and use “Git Bash”.  
  - Option B: use Windows Subsystem for Linux (WSL) and work inside a Linux shell.  
  - Option C: use PowerShell, which includes `ssh` on recent Windows versions.

For the rest of the tutorial we assume you have a shell where running `ssh` works.

---

## 3. Generating and managing SSH keys

### 3.1 Why SSH keys

You can log in with a password, but for long-term access and for automated tools we use an SSH key pair:

- **public key**: safe to share; installed on servers or uploaded to web portals.  
- **private key**: stays on your machine; never email it or commit it to Git.

The server verifies that you control the private key that matches the public key you uploaded.

### 3.2 Creating a key pair

On your local machine, run:

```bash
ssh-keygen -t ed25519
```

You will see prompts similar to:

```text
Enter file in which to save the key (/home/you/.ssh/id_ed25519):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
```

Recommendations for LDG use:

- Accept the default file path (press Enter).  
- Choose a strong, unique passphrase. **Do not** leave it empty.  
- Do not reuse the same passphrase as for any other account.

After this you will have:

- Private key: `~/.ssh/id_ed25519`  
- Public key: `~/.ssh/id_ed25519.pub`

The `.pub` file is what you will upload to web portals.

### 3.3 Using `ssh-agent` so you do not retype the passphrase

To avoid typing the passphrase every time:

```bash
# Start an agent (on Linux / macOS)
eval "$(ssh-agent -s)"

# Add your key
ssh-add ~/.ssh/id_ed25519
```

You will enter the passphrase once per login session and `ssh-agent` will remember it until you log out.

On many Linux desktops and on macOS, `ssh-agent` can be managed automatically by the system keychain. You can simply run `ssh-add` once and allow the system to remember the key.

### 3.4 Inspect your public key

To view and copy your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

The output is a single long line starting with `ssh-ed25519`. Copy this line when a website asks you to “paste your SSH public key”.

---

## 4. Adding your key to GitHub and git.ligo.org

We normally keep two separate concerns in mind:

- **GitHub**: personal, public or open-source projects.  
- **git.ligo.org**: collaboration code and keys that are checked by LIGO infrastructure.

### 4.1 Adding the key to GitHub (personal)

1. Go to your GitHub profile in a web browser.  
2. Open **Settings → SSH and GPG keys → New SSH key**.  
3. Give the key a helpful title (for example, “Laptop ed25519”).  
4. Paste the contents of `id_ed25519.pub`.  
5. Save.

You can now access GitHub repositories via SSH, for example:

```bash
git clone git@github.com:username/repo.git
```

### 4.2 Adding the key to git.ligo.org and to the LDG

For LDG and CIT login you must upload your public key to the collaboration infrastructure, not just to GitHub.

There are two cases.

#### Case A: you have a LIGO.ORG account

1. Request an LDG account at the LDG portal.  
2. After approval, manage your SSH keys at the LDG “Manage SSH keys” page.  
3. Paste your public key where requested.  
4. Wait for confirmation that the key has been accepted.

Once configured, this key is used both for code access on `git.ligo.org` and for direct SSH login to LDG hosts.

#### Case B: you are KAGRA-only

This is the situation for many KAGRA members:

1. Open the IGWN registry link specific to LDG access.  
2. Submit the petition form with your details and desired LDG username.  
3. In the registry interface, look for an **Authenticators** section on the right.  
4. Under the LDG key tab, add your SSH public key. You can add more keys later by using the “Manage” button for that LDG authenticator.  
5. Your request is then manually approved by LDG administrators. When approved, a home directory will be created for you on the cluster and you will receive a confirmation email.  
6. If nothing happens for several days, open a computing help desk ticket and mention your petition number. You can also politely ask on the appropriate Mattermost computing channel.

From this point onward your KAGRA identity plus the uploaded key gives you direct access to LDG login hosts.

---

## 5. Connecting to generic remote servers

Before we specialise to LIGO, let us practice with a generic server. Suppose you have:

- username: `alice`  
- host: `login.myuniversity.edu`

### 5.1 First SSH login

```bash
ssh alice@login.myuniversity.edu
```

If your key is installed on the server, you will be asked for your key passphrase. Otherwise you may be asked for your account password on the remote system.

Once logged in, try a few basic commands:

```bash
whoami      # shows your username on the remote machine
hostname    # shows the machine name
pwd         # print working directory
ls          # list files
```

Use `exit` or press `Ctrl + D` to close the SSH session.

### 5.2 Simple file transfer with `scp`

To upload a file to the remote machine:

```bash
# local → remote
scp myscript.py alice@login.myuniversity.edu:/home/alice/
```

To download a file back:

```bash
# remote → local
scp alice@login.myuniversity.edu:/home/alice/output.txt .
```

For large directories it is often nicer to use `rsync`, but `scp` is enough for a first tutorial.

### 5.3 Running Python on the remote machine

On the remote shell:

```bash
python3 -c "print('Hello from the remote cluster')"
```

Or run a script:

```bash
python3 myscript.py
```

If Python is not installed or the version is too old, ask your local admins which module or Conda environment to load.

### 5.4 Port forwarding for Jupyter

You can tunnel a remote Jupyter notebook to your local browser. On your laptop:

```bash
ssh -L 8889:localhost:8889 alice@login.myuniversity.edu
```

On the remote machine, start Jupyter without opening a browser:

```bash
jupyter notebook --no-browser --port=8889
```

Then open `http://localhost:8889` in your local browser and paste the Jupyter token URL.

---

## 6. Overview of the LIGO Data Grid (LDG)

The LIGO Data Grid is a collection of computing centres that share a common authentication and software stack. You see a consistent environment, whether you log in at CIT, LHO, LLO, Nemo, IUCAA, or other sites.

Key facts:

- Access is controlled centrally through the LDG and IGWN portals, using your LIGO.ORG or KAGRA identity.  
- Once your SSH key is registered, you can log in to different sites with the same credentials.  
- Many sites also support HTCondor-based access points for large workflows.

You already saw in section 4 how to request an LDG account and upload keys.

---

## 7. CIT (Caltech) LDAS cluster

The CIT site is one of the most heavily used LDG centres. It provides several kinds of hosts:

- `ldas-grid.ligo.caltech.edu`: general login host for the CIT LDG.  
- `citlogin0.ligo.caltech.edu` and `citlogin1` to `citlogin5`: general login and per-home-area login hosts.  
- `ldas-pcdev2.ligo.caltech.edu`, `ldas-pcdev3.ligo.caltech.edu`, `ldas-pcdev11` to `ldas-pcdev14`: development and GPU hosts, including A100 nodes.  
- Special purpose hosts such as `cbc.ligo.caltech.edu`, `spiir.ligo.caltech.edu`, `mly.ldas.cit`, and others for specific pipelines.

The important point for a new user is that you normally log in to a login node first, then hop or submit jobs to GPU or specialised nodes.

There are several routes in.

### 7.1 Option A: `ssh.ligo.org` portal

If you have a LIGO.ORG account, one simple way to reach any LDG site is through the SSH portal:

```bash
ssh your.name@ssh.ligo.org
```

You will see a menu like:

```text
::: LIGO Data Grid Login Menu :::
Select from these LDG sites:
 1. CDF - Cardiff
 2. CIT - Caltech
 3. LHO - Hanford
 4. LLO - Livingston
 ...
 Z. Specify alternative user account
Enter selection from the list above:
```

Choose the number for CIT, then follow any further prompts. You will end up logged in on a CIT login host.

This route is nice when you are unsure which host to use, or if you want a simple menu-based way to choose between CIT, LHO, LLO, etc.

### 7.2 Option B: direct SSH with an SSH key

Once your SSH key is registered with LDG, you can log in directly:

```bash
ssh your.name@ldas-grid.ligo.caltech.edu
```

or

```bash
ssh your.name@citlogin0.ligo.caltech.edu
```

For some hosts you may be prompted for your LDG password, for others the SSH key is enough. The exact policy depends on the host configuration.

### 7.3 Duo multi-factor authentication and SSH proxy

Recently, some CIT hosts are protected by Duo-based multi-factor authentication. For those hosts the documentation lists `MFA:Y` for built-in multi-factor, or `MFA:P` for login via an SSH proxy.

The current recommended pattern for the GPU hosts is to use `ssh.igwn.org` as a ProxyJump host. For example:

```bash
ssh -J your.name@ssh.igwn.org your.name@ldas-pcdev12.ligo.caltech.edu
```

What happens:

1. SSH connects to `ssh.igwn.org` as an intermediate host. This triggers Duo multi-factor.  
2. Duo sends a push notification or asks for a passcode on your phone.  
3. After you approve the request, SSH automatically “jumps” to the final host `ldas-pcdev12.ligo.caltech.edu`.

You may need to repeat the Duo approval each time you start a new SSH session, depending on the site policy.

#### Duo enrollment

Before this works you must have Duo configured for your Caltech or IGWN account:

1. Install the Duo Mobile application on your phone.  
2. Use the Duo enrollment or device management portal recommended by your local IT to link your phone to your account.  
3. Test Duo with another protected service (for example access to Caltech web systems) before trying the cluster.

Each institute will have its own detailed screenshots, so always follow the official Duo documentation.

### 7.4 Making life easier with `~/.ssh/config`

You can avoid typing long commands with a local SSH configuration file.

Create or edit `~/.ssh/config` on your laptop:

```bash
nano ~/.ssh/config
```

Add entries like:

```text
Host igwn-ssh
    HostName ssh.igwn.org
    User your.name

Host cit-grid
    HostName ldas-grid.ligo.caltech.edu
    User your.name
    ProxyJump igwn-ssh

Host cit-pcdev12
    HostName ldas-pcdev12.ligo.caltech.edu
    User your.name
    ProxyJump igwn-ssh
```

Now you can simply run:

```bash
ssh cit-grid
```

or

```bash
ssh cit-pcdev12
```

Your local SSH client will handle usernames and ProxyJump through `ssh.igwn.org` automatically.

### 7.5 KAGRA-only notes

If you are KAGRA-only:

- Your LDG access request goes through the IGWN registry petition form, not the LIGO.ORG portal.  
- Make sure you paste the same `id_ed25519.pub` key that you generated locally into the LDG authenticator in the registry.  
- After you receive confirmation and a home directory is created, direct SSH and ProxyJump commands from this section should work, just with your own IGWN username.

If approval is slow, open a help desk ticket and mention your petition, and include a brief, polite description of the problem you see when you try to log in.

---

## 8. Other LDG sites, including MIT-linked work

Once your LDG account and keys are set up, the same methods work for other LDG sites.

### 8.1 Using `ssh.ligo.org` to choose a site

From your laptop:

```bash
ssh your.name@ssh.ligo.org
```

Choose a site from the menu:

- CIT: Caltech LDAS.  
- LHO: Hanford LDAS.  
- LLO: Livingston LDAS.  
- Nemo (UWM), Gwave (PSU), IUCAA, and others.

You can work on the site that is closest to your collaboration or to your supervisors. From the user point of view, software such as `lalsuite`, IGWN Conda environments, and HTCondor access points are provided in a very similar way at different centres.

### 8.2 MIT-related work

MIT LIGO computing is integrated into the LDG and IGWN environment, rather than being a completely separate portal. In practice this means:

- You authenticate using the same IGWN or LIGO.ORG identity.  
- You access data and software through LDG sites such as CIT and LHO / LLO.  
- For machine learning and method development you may also be granted access to specific hosts (for example `mly.ldas.cit`) through the same LDG credentials.

From your perspective as a student, the main pattern is the same: log in through `ssh.ligo.org` or a direct SSH host, then use the software environments there.

---

## 9. Running Python on CIT

Once you can log in, let us run some simple Python commands.

### 9.1 Interactive session on a login node

Log in to `ldas-grid.ligo.caltech.edu` or another login host, then:

```bash
python3 -c "import sys; print(sys.version)"
```

This confirms you are using the site Python.

For one-off tests, you can run:

```bash
python3
```

and use the interactive prompt. For anything heavier, put your code in a script and consider running it on a development or GPU node.

### 9.2 Using Conda or modules

CIT exposes several software stacks. Typical patterns:

```bash
# Example: load an IGWN Conda environment
source /cvmfs/ligo.osgstorage.org/conda/etc/profile.d/conda.sh
conda activate igwn-py38

# Or use environment modules, depending on the site configuration
module avail
module load igwn-py38
```

Check the CIT documentation for the current recommended environment names. Sites may have `igwn-py39`, `igwn-py310`, or similar.

### 9.3 Basic workflow example

On your laptop:

```bash
scp my_search_script.py cit-grid:~/projects/
ssh cit-grid
```

On the remote host:

```bash
cd ~/projects
conda activate igwn-py38     # or the relevant environment
python3 my_search_script.py
```

You can extend this to more advanced workflows, for example:

- Submitting HTCondor jobs from a submission node.  
- Using IGWN container images.  
- Running code that streams alerts from Kafka for multimessenger work.

For this introductory tutorial we stay with interactive jobs.

---

## 10. Porting the workflow to a local GPU cluster

Many institutes now have their own GPU cluster. The good news is that most of what you learned here transfers directly.

Checklist for a local cluster:

1. **Authentication**: local Unix account or institute single sign-on.  
2. **SSH access**: get the hostname and port from your local admins. Set up SSH keys as in section 3 and add entries to `~/.ssh/config`.  
3. **File system layout**: there will be a home directory and perhaps a project or scratch space. Learn the quotas and clean-up policy.  
4. **Software**:  
   - Python and Conda.  
   - Modules for CUDA, compilers, and libraries.  
   - Any site-specific containers or Singularity images.  
5. **Job scheduling**:  
   - Many local clusters use Slurm instead of HTCondor.  
   - The idea is similar: you write a small job script that loads modules and launches your Python application, then submit it to a queue.

Your students can practice on the LDG during the session, then map the same concepts to the local cluster.

A very simple pattern that works both on LDG and on local clusters is:

```bash
# On your laptop
git clone git@github.com:yourname/yourproject.git
cd yourproject

# Push to GitHub / git.ligo.org
git add .
git commit -m "First working version"
git push

# On the cluster
git clone git@github.com:yourname/yourproject.git
cd yourproject
conda env create -f environment.yml
conda activate your-env
python3 main.py
```

This keeps your workflow reproducible and version controlled everywhere.

---

## 11. Further references

You can share these links with students for deeper reading:

- IGWN Computing Guide main page.  
- LIGO Data Grid overview and access methods.  
- CIT LDAS site documentation with host lists and environment details.  
- SSH key generation tutorial for the LIGO Data Grid.  
- Duo multi-factor authentication documentation from Caltech or your home institute.  
- “Getting Started with LIGO” notes such as Eric Thrane’s web page.  
- My own blog posts:  
  - “Accessing Remote Computing Clusters using Open-SSH”.  
  - “How to use LIGO Data Grid CIT as KAGRA-only Member”.

---