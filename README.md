# eSim 2.5 on Ubuntu 25.04: Screening Task 4

**Subham Sahu, FOSSEE Winter Internship 2026**

The eSim 2.5 installer did not run on Ubuntu 25.04. I found 11 issues, fixed each one in its own commit, and tested the result on a fresh Ubuntu 25.04 system. After the fixes eSim installs, starts, and simulates the RC example with both the eSim plotter and the Ngspice plots.

## Branches

| Branch | What it holds |
| --- | --- |
| [`installers`](https://github.com/subham-sahu06/eSim/tree/installers) | Installer fixes on top of FOSSEE/eSim `installers`, one commit each, and the notes file [`Ubuntu/UBUNTU-25.04-FIXES.md`](https://github.com/subham-sahu06/eSim/blob/installers/Ubuntu/UBUNTU-25.04-FIXES.md) |
| [`ubuntu-25.04-fixes`](https://github.com/subham-sahu06/eSim/tree/ubuntu-25.04-fixes) | The eSim 2.5 release zip as downloaded, then one commit per fix, then the test logs in [`test-logs/ubuntu-25.04/`](https://github.com/subham-sahu06/eSim/tree/ubuntu-25.04-fixes/test-logs/ubuntu-25.04) |

The `installers` branch has only the installer scripts, so the fixes for `nghdl.zip` and `src/` are on `ubuntu-25.04-fixes`.

## Issues found and fixed

| # | Problem | Fix | Commit |
| --- | --- | --- | --- |
| 1 | The installer has no case for 25.04 and stops with "Unsupported Ubuntu version: 25.04 ()" | Add the 25.04 case and `install-eSim-25.04.sh` | [e8dc2e6](https://github.com/subham-sahu06/eSim/commit/e8dc2e6) |
| 2 | `FULL_VERSION` is empty on 25.04, because `lsb_release -d` prints "Ubuntu 25.04" with no third number | Make the third number optional in the pattern | [254d8f2](https://github.com/subham-sahu06/eSim/commit/254d8f2) |
| 3 | The KiCad PPA build for 25.04 needs libgit2-1.8, which 25.04 does not have | Install KiCad 8.0.8 from the Ubuntu archive | [9d2e26f](https://github.com/subham-sahu06/eSim/commit/9d2e26f) |
| 4 | The nghdl installer has issues 1 and 2 too | Same fixes inside `nghdl.zip` | [859fd54](https://github.com/subham-sahu06/eSim/commit/859fd54) |
| 5 | `exit 0` inside `installKicad` ends the whole installer when KiCad 8 is already installed | Use `return 0` | [a7cda98](https://github.com/subham-sahu06/eSim/commit/a7cda98) |
| 6 | `libcanberra-gtk-module` (GTK2) has no installation candidate on 25.04 | Install only `libcanberra-gtk3-module` | [0079645](https://github.com/subham-sahu06/eSim/commit/0079645) |
| 7 | GHDL 4.1.0 accepts LLVM up to 18.1, but 25.04 has LLVM 20 | Build GHDL with `llvm-18` | [6e7d99a](https://github.com/subham-sahu06/eSim/commit/6e7d99a) |
| 8 | "Ngspice plots" passes the binary `.raw` file to ngspice as input | Pass the netlist, and the `.raw` file with `-r` | [80ad808](https://github.com/subham-sahu06/eSim/commit/80ad808) |
| 9 | `apt install python3-virtualenv` stops at "Continue? [Y/n]" | Add `-y` | [f1546f1](https://github.com/subham-sahu06/eSim/commit/f1546f1) |
| 10 | The welcome page is empty on Linux | Look for `welcome.html` two folders up | [9dbcc8a](https://github.com/subham-sahu06/eSim/commit/9dbcc8a) |
| 11 | `Ubuntu/install-eSim.sh` on the `installers` branch has a syntax error after a merge | Restore the `else` branch and remove the leftover lines | [4819756](https://github.com/subham-sahu06/eSim/commit/4819756) |

On the `installers` branch, fixes 2, 3, 5 and 9 are [9ce4c02](https://github.com/subham-sahu06/eSim/commit/9ce4c02), [3e12cc5](https://github.com/subham-sahu06/eSim/commit/3e12cc5), [4c24b95](https://github.com/subham-sahu06/eSim/commit/4c24b95) and [481390d](https://github.com/subham-sahu06/eSim/commit/481390d).

## Method

1. Run `./install-eSim.sh --install` and save the output with `tee`.
2. Read the first error and find its cause in the script, in `apt-cache policy`, or in the eSim source.
3. Make the smallest fix, check it with `bash -n`, and commit it alone.
4. Run the installer again, until it finishes and eSim can simulate an example.

Each step's log is in [`test-logs/ubuntu-25.04/`](https://github.com/subham-sahu06/eSim/tree/ubuntu-25.04-fixes/test-logs/ubuntu-25.04). The changes inside `nghdl.zip` are also shown as a plain diff in [`nghdl-zip-changes.diff`](https://github.com/subham-sahu06/eSim/blob/ubuntu-25.04-fixes/test-logs/ubuntu-25.04/nghdl-zip-changes.diff).

## Test on a fresh Ubuntu 25.04 system

```sh
git clone --single-branch -b ubuntu-25.04-fixes https://github.com/subham-sahu06/eSim.git
cd eSim
./install-eSim.sh --install
esim
```

Answer `n` to the proxy question, then open `Examples/RC`, click Simulate, and choose Yes for Ngspice plots.

Result: the install finished in about 15 minutes with no `[Y/n]` prompt, the welcome page showed, and the RC example simulated with working Ngspice plots.

---

*The original README of this branch follows.*

![GitHub release (latest by date)](https://img.shields.io/github/v/release/fossee/esim?color=blueviolet)
![GitHub](https://img.shields.io/github/license/fossee/esim?color=blue)
![Python](https://img.shields.io/badge/python-v3.6+-blue.svg)
[![PEP8](https://img.shields.io/badge/code%20style-pep8-orange.svg)](https://www.python.org/dev/peps/pep-0008/)
![Travis (.org)](https://img.shields.io/travis/fossee/esim)
[![Documentation Status](https://readthedocs.org/projects/esim/badge/?version=latest)](https://esim.readthedocs.io/en/latest/?badge=latest)
[![GitHub forks](https://img.shields.io/github/forks/fossee/esim)](https://github.com/fossee/esim/network)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat)](https://github.com/fossee/esim)
![GitHub contributors](https://img.shields.io/github/contributors/fossee/esim)

## eSim

[eSim](https://esim.fossee.in/) is an open source EDA tool for circuit design, simulation, analysis and PCB design, developed by [FOSSEE Team](https://www.fossee.in/) at [IIT Bombay](https://www.iitb.ac.in/).
It is an integrated tool build using open source softwares such as KiCad, Ngspice and GHDL.

## Releases and Installation
eSim is released for the following distributions (operating systems):
* Ubuntu 18.04, 20.04 LTS versions.
* Microsoft Windows 8, 10 and 11.

To use eSim on your machine having above distributions, please refer to link [here](https://esim.fossee.in/downloads) for installation and other guidelines.

> Note for other distributions: You can refer [`installers`](https://github.com/fossee/eSim/tree/installers) branch for documentation on packaging (for above mentioned distributions) to build installers for your operating system in a similar way. For providing your build, please check the `Contribution` section mentioned below.

## Features
* An open-source EDA tool.
* Perform Circuit Design.
* Perform Simulation.
* Perform Layout Design.
* Model and Subcircuit builder.
* Support for Mixed-Signal Simulations including Microcontrollers.
* eSim has been successfully ported to low cost FOSSEE [laptop](https://laptop.fossee.in/)

## Open-Source Softwares Used
* [Python](https://www.python.org/)
* [KiCad](https://www.kicad.org/)
* [NGHDL](https://github.com/fossee/nghdl/)
* [Makerchip](https://www.makerchip.com/)
* [SkyWater SKY130 PDK](https://skywater-pdk.rtfd.io/)

## eSim Manual
To know everything about eSim, how it works and it's feature please download the manual from [here](https://static.fossee.in/esim/manuals/eSim_Manual_2.5.pdf)

## Contact
For any queries regarding eSim please write us on at this [email address](mailto:contact-esim@fossee.in).

## Contribution
Please refer [here](https://github.com/FOSSEE/eSim/blob/master/CONTRIBUTION.md) for further details.

## License
It is developed by FOSSEE Team at IIT Bombay and is released under GNU GPL License.
