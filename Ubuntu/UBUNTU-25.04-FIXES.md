# eSim 2.5 on Ubuntu 25.04: issues and fixes

Screening Task 4, Subham Sahu.

The eSim 2.5 installer did not run on Ubuntu 25.04. I tested it on a fresh
Ubuntu 25.04 VM (kernel 6.14.0-37, Python 3.13.3), fixed each problem in its own
commit, and ran the install again after every fix. After the fixes eSim installs,
starts, and simulates the RC example with both the eSim plotter and the Ngspice
plots.

## Where the fixes are

The fixes are in two branches of this fork. This branch has only the installer
files, so the fixes for `nghdl.zip` and `src/` are in a second branch.

| Branch | What it holds |
| --- | --- |
| `installers` (this branch) | Fixes for `Ubuntu/install-eSim.sh` and `Ubuntu/install-eSim-scripts/install-eSim-25.04.sh` |
| [`ubuntu-25.04-fixes`](https://github.com/subham-sahu06/eSim/tree/ubuntu-25.04-fixes) | The eSim 2.5 release zip as downloaded, then all fixes, then the test logs in `test-logs/ubuntu-25.04/` |

## Issues

| # | Problem | Fix | Branch |
| --- | --- | --- | --- |
| 1 | The release installer has no case for 25.04 and prints "Unsupported Ubuntu version: 25.04 ()" | Add the 25.04 case and `install-eSim-25.04.sh` (this branch already had them) | ubuntu-25.04-fixes |
| 2 | `FULL_VERSION` is empty on 25.04, because `lsb_release -d` prints "Ubuntu 25.04" with no third number | Pattern `\d+\.\d+(\.\d+)?` | both |
| 3 | The KiCad PPA build for 25.04 (8.0.9-0~ubuntu25.04.1) needs libgit2-1.8, which 25.04 does not have | Do not add the PPA on 25.04, install KiCad 8.0.8 from the Ubuntu archive | both |
| 4 | The nghdl installer in `nghdl.zip` has issues 1 and 2 too | Same fixes in `nghdl.zip` | ubuntu-25.04-fixes |
| 5 | `exit 0` inside `installKicad` ends the whole installer when KiCad 8 is already installed | `return 0` | both |
| 6 | `libcanberra-gtk-module` (GTK2) has no installation candidate on 25.04 | Install only `libcanberra-gtk3-module` | ubuntu-25.04-fixes |
| 7 | GHDL 4.1.0 accepts LLVM up to 18.1, but 25.04 has LLVM 20 ("Unhandled version llvm 20.1.2") | Install `llvm-18` and build with `--with-llvm-config=/usr/bin/llvm-config-18` | ubuntu-25.04-fixes |
| 8 | "Ngspice plots" runs `ngspice -p` on the binary `.raw` file, so ngspice fails with "UTF-8 syntax error" | Pass the netlist as input and the `.raw` file with `-r` (`src/ngspiceSimulation/NgspiceWidget.py`) | ubuntu-25.04-fixes |
| 9 | `sudo apt install python3-virtualenv` stops at "Continue? [Y/n]" | Add `-y` | both |
| 10 | The welcome page is empty on Linux, because `Welcome.py` looks for `welcome.html` in `src/browser` | Look two folders up when not running as a Windows .exe | ubuntu-25.04-fixes |
| 11 | `Ubuntu/install-eSim.sh` on this branch has a syntax error left by a merge (`syntax error near unexpected token '}'`), so it does not run on any Ubuntu version | Restore the `else` branch and remove the leftover lines | installers |

The nghdl changes inside `nghdl.zip` are also shown as a plain diff in
`test-logs/ubuntu-25.04/nghdl-zip-changes.diff` on the `ubuntu-25.04-fixes` branch.

## Test on a fresh Ubuntu 25.04 system

```sh
git clone --single-branch -b ubuntu-25.04-fixes https://github.com/subham-sahu06/eSim.git
cd eSim
./install-eSim.sh --install
esim
```

Answer `n` to the proxy question. Then open `Examples/RC`, click Simulate, and
choose Yes for Ngspice plots.

Result on the fresh VM: the install finished in about 15 minutes with no
`[Y/n]` prompt, the welcome page showed, and the RC example simulated with
working Ngspice plots.

## Not fixed

- On the first start on a fresh system, after OK in the workspace dialog, the
  eSim windows stayed unmapped (`xwininfo` shows IsUnMapped). Later starts open
  normally. I have not found the cause yet.
- The installer prints "rm: cannot remove '/usr/bin/ngspice'" on a fresh system.
  It is harmless.
