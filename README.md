# RustHound

<p align="center">
  <a href="https://crates.io/crates/rusthound"><img alt="Crates.io" src="https://img.shields.io/crates/v/rusthound?style=for-the-badge"></a>
  <img alt="GitHub" src="https://img.shields.io/github/license/OPENCYBER-FR/RustHound?style=for-the-badge">
  <img alt="Linux supported" src="https://img.shields.io/badge/Supported%20OS-Linux-orange?style=for-the-badge">
  <img alt="Windows supported" src="https://img.shields.io/badge/Supported%20OS-Windows-green?style=for-the-badge">
  <img alt="MacOS supported" src="https://img.shields.io/badge/Supported%20OS-MacOS-blue?style=for-the-badge">
  <a href="https://twitter.com/intent/follow?screen_name=OPENCYBER_FR" title="Follow"><img alt="Twitter Follow" src="https://img.shields.io/twitter/follow/OPENCYBER_FR?label=OPENCYBER_FR&style=for-the-badge"></a>
  <a href="https://twitter.com/intent/follow?screen_name=g0h4n_0" title="Follow"><img src="https://img.shields.io/twitter/follow/g0h4n_0?label=g0h4n&style=for-the-badge"></a>
  <br>
</p>

<p align="center">
<img width="30%" src="img/rusthound_logo_v3.png">
</p>

# Summary

- [Limitation](#limitations)
- [Description](#description)
- [How to compile it?](#how-to-compile-it)
  - [Using Makefile](#using-makefile)
  - [Using Dockerfile](#using-dockerfile)
  - [Using Cargo](#using-cargo)
  - [Linux x86_64 static version](#manually-for-linux-x86_64-static-version)
  - [Windows static version from Linux](#manually-for-windows-static-version-from-linux)
  - [MacOS static version from Linux](#manually-for-macos-static-version-from-linux)
- [How to build documentation?](#how-to-build-documentation)
- [Usage](#usage)
- [Demo](#demo)
  - [Simple usage](#simple-usage)
  - [Module FQDN resolver](#module-fqdn-resolver)
  - [Module ADCS collector](#module-adcs-collector)
- [Statistics](#rocket-statistics)
- [Roadmap](#-roadmap)
- [Links](#link-links)

# Limitations

Not all SharpHound features are implemented yet but some are existing in RustHound and do not in SharpHound or BloodHound-Python. Please refer to the [roadmap](#-roadmap) for more information.

# Description

RustHound is a **cross-platform** BloodHound collector tool, written in Rust. (Linux,Windows,MacOS)

No anti-virus detection and **cross-compiled**.

RustHound generate users,groups,computers,ous,gpos,containers,domains json files to analyze it with BloodHound application.

> 💡 If you can use SharpHound.exe, use it.
> Rusthound is a backup solution if SharpHound.exe is detected by AV or if SharpHound.exe isn't executable from the system where you have access to.


# How to compile it?

## Using Makefile

You can use **make** command to install Rusthound or to compile it for Linux or Windows.

```bash
make install
rusthount -h
```

More command in the **Makefile**:

```bash
make help
  usage: make install
  usage: make uninstall
  usage: make debug
  usage: make release
  usage: make windows
  usage: make linux_musl
```

## Using Dockerfile

Use RustHound with docker to make sure to have all dependencies.

```bash
docker build -t rusthound .
docker run rusthound -h
```

## Using Cargo

You need to install rust on your system (Windows/Linux/MacOS).

[https://www.rust-lang.org/fr/tools/install](https://www.rust-lang.org/fr/tools/install)

RustHound support Kerberos/GSSAPI but this means that it needs Clang and its development libraries, as well as the Kerberos development libraries. On Debian/Ubuntu, that means **clang-N**, **libclang-N-dev** and **libkrb5-dev**.

For example:
```bash
#Debian/Ubuntu
sudo apt-get -y update && sudo apt-get -y install gcc libclang-dev clang libclang-dev libgssapi-krb5-2 libkrb5-dev libsasl2-modules-gssapi-mit musl-tools gcc-mingw-w64-x86-64
```

Here is how to compile the "release" and "debug" versions from "cargo" command.

```bash
git clone https://github.com/OPENCYBER-FR/RustHound
cd RustHound
cargo build --release
#or debug version
cargo b
```

The result can be found in "target/release" or in "target/debug" folder.

Below you can find the compilation methodology for each of the OS from Linux.
If you need another compilation system, please consult the list in this link : [https://doc.rust-lang.org/nightly/rustc/platform-support.html](https://doc.rust-lang.org/nightly/rustc/platform-support.html)


## Manually for Linux x86_64 static version
```bash
#Install rustup and cargo in Linux
curl https://sh.rustup.rs -sSf | sh

#Add Linux deps
rustup install stable-x86_64-unknown-linux-gnu
rustup target add x86_64-unknown-linux-gnu

#Static compilation for Linux
git clone https://github.com/OPENCYBER-FR/RustHound
cd RustHound
CFLAGS="-lrt";LDFLAGS="-lrt";RUSTFLAGS='-C target-feature=+crt-static';cargo build --release --target x86_64-unknown-linux-gnu
```

The result can be found in "target/x86_64-unknown-linux-gnu/release" folder.


## Manually for Windows static version from Linux
```bash
#Install rustup and cargo in Linux
curl https://sh.rustup.rs -sSf | sh

#Add Windows deps
rustup install stable-x86_64-pc-windows-gnu
rustup target add x86_64-pc-windows-gnu

#Static compilation for Windows
git clone https://github.com/OPENCYBER-FR/RustHound
cd RustHound
RUSTFLAGS="-C target-feature=+crt-static" cargo build --release --target x86_64-pc-windows-gnu
```

The result can be found in "target/x86_64-pc-windows-gnu/release" folder.

## Manually for MacOS static version from Linux

Amazing documentation: [https://wapl.es/rust/2019/02/17/rust-cross-compile-linux-to-macos.html](https://wapl.es/rust/2019/02/17/rust-cross-compile-linux-to-macos.html)

```bash
#Install rustup and cargo in Linux
curl https://sh.rustup.rs -sSf | sh

#Add MacOS tool chain
cd /usr/local/bin
sudo git clone https://github.com/tpoechtrager/osxcross
cd osxcross
sudo wget -nc https://s3.dockerproject.org/darwin/v2/MacOSX10.10.sdk.tar.xz
sudo mv MacOSX10.10.sdk.tar.xz tarballs/
sudo UNATTENDED=yes OSX_VERSION_MIN=10.7 ./build.sh
sudo chmod 770 /usr/local/bin/osxcross/ -R
export PATH="/usr/local/bin/osxcross/target/bin:$PATH" \

#Cargo needs to be told to use the correct linker for the x86_64-apple-darwin target, so add the following to your project’s .cargo/config file:
vim ~/.cargo/config
  [target.x86_64-apple-darwin]
  linker = "x86_64-apple-darwin14-clang"
  ar = "x86_64-apple-darwin14-ar"

#Static compilation for MacOS
git clone https://github.com/OPENCYBER-FR/RustHound
cd RustHound
#Uncomment line 32 and comment line 34 in Cargo.toml
RUSTFLAGS="-C target-feature=+crt-static" cargo build --release --target x86_64-apple-darwin
```

The result can be found in "target/x86_64-apple-darwin/release" folder.

# How to build the documentation?

```bash
git clone https://github.com/OPENCYBER-FR/RustHound
cd RustHound
cargo doc --open --no-deps
```

# Usage

```bash
Usage: rusthound [OPTIONS] --domain <domain>

Options:
  -v...          Set the level of verbosity
  -h, --help     Print help information
  -V, --version  Print version information

REQUIRED VALUES:
  -d, --domain <domain>  Domain name like: DOMAIN.LOCAL

OPTIONAL VALUES:
  -u, --ldapusername <ldapusername>  LDAP username, like: user@domain.local
  -p, --ldappassword <ldappassword>  LDAP password
  -f, --ldapfqdn <ldapfqdn>          Domain Controler FQDN like: DC01.DOMAIN.LOCAL or just DC01
  -i, --ldapip <ldapip>              Domain Controller IP address like: 192.168.1.10
  -P, --ldapport <ldapport>          LDAP port [default: 389]
  -n, --name-server <name-server>    Alternative IP address name server to use for DNS queries
  -o, --output <output>              Output directory where you would like to save JSON files [default: ./]

OPTIONAL FLAGS:
      --ldaps           Force LDAPS using for request like: ldaps://DOMAIN.LOCAL/
      --dns-tcp         Use TCP instead of UDP for DNS queries
      --dc-only         Collects data only from the domain controller. Will not try to retrieve CA security/configuration or check for Web Enrollment
      --old-bloodhound  For ADCS only. Output result as BloodHound data for the original BloodHound version from @BloodHoundAD without PKI support
  -z, --zip             Compress the JSON files into a zip archive

OPTIONAL MODULES:
      --fqdn-resolver  Use fqdn-resolver module to get computers IP address
      --adcs           Use ADCS module to enumerate Certificate Templates, Certificate Authorities and other configurations.
                       (For the custom-built BloodHound version from @ly4k with PKI support)
```

# Demo

Examples are done on the [GOADv2](https://github.com/Orange-Cyberdefense/GOAD) implemented by [mayfly](https://twitter.com/M4yFly):

## Simple usage

```bash
# Linux with username:password
rusthound -d north.sevenkingdoms.local -u 'jeor.mormont@north.sevenkingdoms.local' -p '_L0ngCl@w_' -o /tmp/demo -z

# Linux with username:password and ldapip
rusthound -d north.sevenkingdoms.local -i 192.168.56.11 -u 'jeor.mormont@north.sevenkingdoms.local' -p '_L0ngCl@w_' -o /tmp/demo -z

# Linux with username:password and ldaps
rusthound -d north.sevenkingdoms.local --ldaps -u 'jeor.mormont@north.sevenkingdoms.local' -p '_L0ngCl@w_' -o /tmp/demo -z 
# Linux with username:password and ldaps and custom port
rusthound -d north.sevenkingdoms.local --ldaps -P 3636 -u 'jeor.mormont@north.sevenkingdoms.local' -p '_L0ngCl@w_' -o /tmp/demo -z 

# Tips to redirect and append both standard output and standard error to a file > /tmp/rh_output 2>&1
rusthound -d north.sevenkingdoms.local --ldaps -u 'jeor.mormont@north.sevenkingdoms.local' -p '_L0ngCl@w_' -o /tmp/demo --fqdn-resolver > /tmp/rh_output 2>&1

# Windows with GSSAPI session
rusthound.exe -d sevenkingdoms.local --ldapfqdn kingslanding
# Windows simple bind connection username:password (don't use simple quote or double quote with cmd.exe)
rusthound.exe -d sevenkingdoms.local -u jeor.mormont@north.sevenkingdoms.local -p _L0ngCl@w_ -o output -z
```
<p align="center">
<img width="100%" src="img/demo.gif">
</p>

## Module FQDN resolver

```bash
# Linux with username:password and FQDN resolver module
rusthound -d essos.local -u 'daenerys.targaryen@essos.local' -p 'BurnThemAll!' -o /tmp/demo --fqdn-resolver -z
# Linux with username:password and ldaps and FQDN resolver module and TCP DNS request and custom name server
rusthound -d essos.local --ldaps -u 'daenerys.targaryen@essos.local' -p 'BurnThemAll!' -o /tmp/demo --fqdn-resolver --tcp-dns --name-server 192.168.56.12 -z

# Windows with GSSAPI session and FQDN resolver module
rusthound.exe -d essos.local -f meereen -o output --fqdn-resolver -z
# Windows simple bind connection username:password and FQDN resolver module and TCP DNS request and custom name server (don't use simple quote or double quote with cmd.exe)
rusthound.exe -d essos.local -u daenerys.targaryen@essos.local -p BurnThemAll! -o output -z --fqdn-resolver --tcp-dns --name-server 192.168.56.12 
```
<p align="center">
<img width="100%" src="img/demo_windows_fqdn_resolver.gif">
</p>


## Module ADCS collector

Example is done with the [@ly4k BloodHound version](https://github.com/ly4k/BloodHound).

```bash
# Linux with username:password and ADCS module for @ly4k BloodHound version
rusthound -d essos.local -u 'daenerys.targaryen@essos.local' -p 'BurnThemAll!' -o /tmp/adcs --adcs -z
# Linux with username:password and ADCS module and dconly flag (will don't check webenrollment)
rusthound -d essos.local -u 'daenerys.targaryen@essos.local' -p 'BurnThemAll!' -o /tmp/adcs --adcs --dc-only -z

# Linux with username:password and ADCS module using "--old-bloodhound" argument for official @BloodHoundAd version
rusthound -d essos.local -u 'daenerys.targaryen@essos.local' -p 'BurnThemAll!' -o /tmp/adcs --adcs --old-bloodhound -z

# Windows with GSSAPI session and ADCS module
rusthound.exe -d essos.local -f meereen -o output -z --adcs
# Windows with GSSAPI session and ADCS module and TCP DNS request and custom name server
rusthound.exe -d essos.local --ldapfqdn meereen -o output -z --adcs --tcp-dns --name-server 192.168.56.12
# Windows simple bind connection username:password (don't use simple quote or double quote with cmd.exe)
rusthound.exe -d essos.local -u daenerys.targaryen@essos.local -p BurnThemAll! -o output -z --adcs --dc-only
```
<p align="center">
<img width="100%" src="img/demo_windows_adcs_collector.gif">
</p>


You can find the custom queries used in the demo, in the resource folder.

Use the following command to install it:

```bash
cp resources/customqueries.json ~/.config/bloodhound/customqueries.json
```

# :rocket: Statistics

In order to make statistics on a DC with more LDAP objects, we run the [BadBlood](https://github.com/davidprowe/BadBlood) project on the domain controller ESSOS.local from [GOAD](https://github.com/Orange-Cyberdefense/GOAD). The DC has now around 3500 objects. An execution average time has been done and here are the output:

| Tool              | Environment         | Objects | Time     | Command line     |
| -------------------------- | ----------------- | ---------- | -------  | -------  |
| SharpHound.exe        | Windows <img width="20px"  src="https://github.com/OPENCYBER-FR/RustHound/blob/main/img/windows.png"/> | ~3500   | ~51.605s | Measure-Command { sharphound.exe -d essos.local --ldapusername 'khal.drogo' --ldappassword 'horse' --domaincontroller '192.168.56.12' -c All } |
| BloodHound.py | Linux <img width="20px" src="https://github.com/OPENCYBER-FR/RustHound/blob/main/img/linux.png"/>    | ~3500   | ~9.657s  | time python3 bloodhound.py -u khal.drogo -p horse -d essos.local -ns 192.168.56.12 --zip -c all |
| RustHound.exe         | Windows <img width="20px"  src="https://github.com/OPENCYBER-FR/RustHound/blob/main/img/windows.png"/> | ~3500   | **~5.315s**  | Measure-Command { rusthound.exe -d essos.local -u khal.drogo@essos.local -p horse -z } | 
| RustHound         | Linux <img width="20px"  src="https://github.com/OPENCYBER-FR/RustHound/blob/main/img/linux.png"/>    | ~3500   | **~3.166s**  | time rusthound -d essos.local -u khal.drogo@essos.local -p horse -z  |

#  🚥 Roadmap

## Authentification
  - [x] ldap (389)
  - [x] ldaps (636)
  - [x] `BIND`
  - [ ] `NTLM`
  - [x] `GSSAPI` for Windows ok but not tested for Linux

## Outputs
  - [x] users.json
  - [x] groups.json
  - [x] computers.json
  - [x] ous.json
  - [x] gpos.json
  - [x] containers.json
  - [x] domains.json
  - [x] cas.json
  - [x] templates.json
  - [x] args and function to zip json files **--zip**

## Modules

- [x] Retreive LAPS password if your user can read them **automatic**
- [x] Resolve FQDN computers found to IP address **--fqdn-resolver**
- [x] Retrieve certificates for ESC exploitation with [Certipy](https://github.com/ly4k/Certipy) **--adcs**
- [ ] Kerberos attack module (ASREPROASTING,KERBEROASTING) **--attack-kerberos**
- [ ] Retrieve datas from trusted domains  **--follow-trust** (Currently working on it, got beta version of this module)


## Bloodhound v4.2

- Parsing Features
  - Users & Computers
    - [ ] `HasSIDHistory`
  - Users
    - [ ] `Properties` : `sfupassword`

- **DCERPC (dependencies)**
  - Computers
    - [ ] `Sessions`
  - OUs & Domains
    - [ ] `LocalAdmins`
    - [ ] `RemoteDesktopUsers`
    - [ ] `DcomUsers`
    - [ ] `PSRemoteUsers`
  - CAs
    - [ ] `User Specified SAN`
    - [ ] `Request Disposition`

# :link: Links

- Blog post: [https://www.opencyber.com/rusthound-data-collector-for-bloodhound-written-in-rust/](https://www.opencyber.com/rusthound-data-collector-for-bloodhound-written-in-rust/)
- BloodHound.py: [https://github.com/fox-it/BloodHound.py](https://github.com/fox-it/BloodHound.py)
- SharpHound:  [https://github.com/BloodHoundAD/SharpHound](https://github.com/BloodHoundAD/SharpHound)
- BloodHound: [https://github.com/BloodHoundAD/BloodHound](https://github.com/BloodHoundAD/BloodHound)
- BloodHound docs: [https://bloodhound.readthedocs.io/en/latest/index.html](https://bloodhound.readthedocs.io/en/latest/index.html)
- GOAD: [https://github.com/Orange-Cyberdefense/GOAD](https://github.com/Orange-Cyberdefense/GOAD)
- ly4k BloodHound version: [https://github.com/ly4k/BloodHound](https://github.com/ly4k/BloodHound)
- Certipy: [https://github.com/ly4k/Certipy](https://github.com/ly4k/Certipy)