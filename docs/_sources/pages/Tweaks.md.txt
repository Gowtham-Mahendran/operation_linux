# Tweaks

## De-encrypt a pdf

1. Download `xpdf-utils' using `sudo apt-get install xpdf-utils`

```
gowtham@deb-gowarc  ~  sudo apt-get install xpdf-utils
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'poppler-utils' instead of 'xpdf-utils'
poppler-utils is already the newest version (22.12.0-2+deb12u1).
poppler-utils set to manually installed.
The following packages were automatically installed and are no longer required:
  libslirp0 pigz slirp4netns
Use 'sudo apt autoremove' to remove them.
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
```

2. Debian is telling you that `xpdf-utils` has been replaced by `poppler-utils`, so when you try to install `xpdf-utils`, it just installs (or confirms you already have) `poppler-utils`.

3. Go to the directory. To decrypt a PDF and create a postscript file

```
pdftops -upw YOURPASSWORD-HERE input.pdf
```

This will create `input.ps` file. 

4. To convert `.ps` file (postscript) back to a `PDF`:
```
ps2pdf input.ps
```

PDF will be created without a encrypt password.

## Git

To check which user the repo is linked to,
```
git config --get user.name
git config --get user.email
```
To check all the details,
```
git config --list
```

To set the repo to a user and mail,
```
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

Omit `--global` to set the identity only in this repository.

Git doesn't allow changes by password. It will accept only Personalized Access Tokens (PAT). So, the remote url should have PAT included. To change this,

Profile > Settings > Developer settings > PAT > Fine-grained tokens

**Grant these permissions:**

* Read access to metadata
* Read and Write access to code

```
git remote set-url origin https://<username>:<PAT>@github.com/<username>/<repo_name>.git
```
