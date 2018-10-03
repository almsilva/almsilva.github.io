---
layout: post
title: "How to install the LLVM8 and Clang on Ubuntu."
---

# How to install the LLVM8 and Clang on Ubuntu.

This is a straight forward description about how to install the LLVM 8 and CLang (+tools) on Ubuntu.

## Preparing the sources.

The [LLVM project](https://llvm.org/) hosts its own [synaptic repository](https://apt.llvm.org/) where we can 
find everything that we need. So, the first is to retrieve and register the archive signature key:

```bash
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add -
```

Now we can add the follow source entries to our ```/etc/apt/sources.list```. Because I am installing it on an
old Ubuntu Trusty (14.04) Vagrant image, I will use the sources described in the appropriated section:

```
# LLVM Nightly Packages
deb http://apt.llvm.org/trusty/ llvm-toolchain-trusty main
deb-src http://apt.llvm.org/trusty/ llvm-toolchain-trusty main

deb http://apt.llvm.org/trusty/ llvm-toolchain-trusty-6.0 main
deb-src http://apt.llvm.org/trusty/ llvm-toolchain-trusty-6.0 main

deb http://apt.llvm.org/trusty/ llvm-toolchain-trusty-7 main
deb-src http://apt.llvm.org/trusty/ llvm-toolchain-trusty-7 main

deb http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu trusty main
```

Finally, we can update our synpatic local indexes:

```bash
sudo apt-get update
```

## Installing LLVM.

Off course we could perform the following steps into a single line. However, for the clarity and explanation
sake of this text, I decided to split it into few sections. So, first, we will install the LLVM  environment
and dependencies.

```bash
sudo apt-get install libllvm-8-ocaml-dev libllvm8 llvm-8 llvm-8-doc
```

## Installing Clang and Tools.

With LLVM runtime environment in place, we can install Clang and related tools:

```bash
sudo apt-get install clang-8 clang-8-doc clang-tidy-8 clang-tools-8 libclang-common-8-dev clang-format-8
```

## Installing LLDB (debugger) and LLD (linker).

As final steps, we can install the LLDB and the LLD using the following command:

```bash
sudo apt-get install lldb-8 lld-8
```

## Testing if everything was correctly installed.

At this point, we have 3 main set of components to test:

* Debugger (LLDB) and linker (LLD).
* Clang compiler and tools.
* LLVM environment and libraries.

Lets start with the LLDB and the LLD. Executing the command ```bash lldb-8 --version```, we should receive
the following output:

```bash
lldb version 8.0.0
```

Simillar output is exepected when we perform the command ```bash ld.lld-8 --version```:

```bash
LLD 8.0.0 (compatible with GNU linkers)
```

The LLVM environment and the Clang can be tested together trying to compile a simple hello world with the
following command:

```bash
clang++-8 main.cpp -o main
```

It must generate a binary executable named ```main``` in the current folder.

## Final touches.

As you can notice, the commands (lldb-8, ld.lld-8 and clang++-8) used above are quite ugly and out of the
standards, including the version in its names. We need to update the symlinks created by default to point to
the installed versions.

```bash
sudo update-alternatives --install /usr/bin/clang clang /usr/bin/clang-8 100
sudo update-alternatives --install /usr/bin/clang++ clang++ /usr/bin/clang++-8 100
sudo update-alternatives --install /usr/bin/clang-cl clang-cl /usr/bin/clang-cl-8 100
sudo update-alternatives --install /usr/bin/clang-tidy clang-tidy /usr/bin/clang-tidy-8 100
sudo update-alternatives --install /usr/bin/clang-format clang-format /usr/bin/clang-format-8 100

sudo update-alternatives --install /usr/bin/ld ld /usr/bin/ld.lld-8 100
sudo update-alternatives --install /usr/bin/lldb lldb /usr/bin/lldb-8 100
```

The above list is not exhaustive and comprehend only the most commonly used commands. The process is the same
for any other command that we use in the future.

Now, we can set our environment variables identifing the compilers to be used by default. Add the following
lines at the end of your ```~/.bashrc``` file:

```bash
export CXX=/usr/bin/clang++
export CC=/usr/bin/clang
export LINT=/usr/bin/clang-tidy
```
Done! Now we have a ready to used minimal LLVM based development environment.

