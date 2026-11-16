# Ubuntu

System tweaks and workarounds.

## CAC on Linux (UBUNTU-001)

Notes and helper files for using a DoD Common Access Card (CAC) with Google Chrome on Ubuntu 24.04.

Chrome reaches the card through `p11-kit-proxy.so`. The proxy starts OpenSC in a separate child process, so a fault in OpenSC costs a card session instead of crashing the browser. With OpenSC 0.25.0~rc1 loaded directly into Chrome, the browser's heap was corrupted and Chrome crashed during CAC use. Two small user-level p11-kit module files, one NSS module entry and the DoD certificate chain in Chrome's NSS database make up the whole setup.

Status: working in Chrome on Ubuntu 24.04.5 with an Alcor Micro AU9540 reader. Edge has not been tried. Firefox is left as it was and uses its own profile.

### Documentation

- [Recreate from scratch](.docs/cac-on-linux-recreate.md) - step-by-step instructions, with verification and rollback for each step, for another machine or a fresh install
- [Change log](.docs/ubuntu-001-change-log.md) - what was changed on the machine this was done on, why, and what is and is not verified
- [Reference setup](.docs/CAC%20on%20Linux%20Reference%20Setup%20for%20Aaron.md) - the original document this work follows, from a machine where it already worked

### Files

- [opensc-remote.module](.docs/opensc-remote.module) and [opensc-pkcs11.module](.docs/opensc-pkcs11.module) - the p11-kit module files, installed in `~/.config/pkcs11/modules/`
- [pkcs11-register.desktop](.docs/pkcs11-register.desktop) - per-user autostart override that stops `pkcs11-register` re-adding OpenSC to Chrome
- [import-dod-intermediates.sh](.docs/import-dod-intermediates.sh) - dry-run-by-default script that imports the DoD intermediate certificates into Chrome's NSS database

### Safety notes

- A CAC locks after three wrong PIN tries. Read the retry counter before any PIN attempt, and never retry a PIN that looks rejected.
- Some command output contains your name and EDIPI (for example the token label from `pkcs11-tool --list-slots`). Redact it before pasting anywhere.
- Close every Chrome before changing the NSS database, and back up `~/.pki/nssdb` first.
