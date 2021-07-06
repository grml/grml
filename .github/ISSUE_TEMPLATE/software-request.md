---
name: Software / Feature request
about: Suggest an application or a feature

---

**Is your software / feature request related to a problem? Please describe.**
A clear and concise description of what the problem can be solved with that requested software. Ex. I'm always frustrated when [...]

**Describe the solution you'd like**
A clear and concise description of what you want to happen.

**Describe alternatives you've considered**
A clear and concise description of any alternative solutions or features you've considered.

**Disk usage of the software**
Space on the CD images is limited. Did you try to install it on the latest Daily Grml Snapshot. How much disk space will be used after installation (including all dependencies)?

```shell
# apt update
# apt install --assume-no $PACKAGE
[...]
After this operation, 10.1 MB of additional disk space will be used.

# apt-cache show $PACKAGE | grep Installed-Size
Installed-Size: 338
```

**The Grml version and flavour you checked that didn't have the feature you are asking for**
<!-- Please check if the package is already included in the latest Daily Grml Snapshot: https://grml.org/daily/ -->

```shell
# grml-version
...
```
