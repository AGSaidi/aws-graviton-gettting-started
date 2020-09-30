# DPDK, SPDK, ISA-L supports Graviton2

Graviton2 is optimized for data path functions like networking and storage.  Users of [DPDK](https://github.com/dpdk/dpdk) and [SPDK](https://github.com/spdk/spdk) can download and compile natively on Graviton2 following the normal installation guidelines from the respective repositories linked above. 

**NOTE**: *Though DPDK precompiled packages are available from Ubuntu but we recommend building them from source.*

SPDK relies often on [ISA-L](https://github.com/intel/isa-l) which is already optimized for Arm64 and the CPU cores in Graviton2.



## Compile DPDK from source

[DPDK official guidelines](https://doc.dpdk.org/guides/linux_gsg/build_dpdk.html) requires using *meson* and *ninja* to build from source code.

A native compilation of DPDK on top of Graviton2 will generate optimized code that take advantage of the CRC and Crypto instructions in Graviton2 cpu cores.

**NOTE**: Some of the installations steps call "python" which may not be valid command in modern linux distribution,  you may need to install *python-is-python3* to resolve this.

### Older DPDK version with makefile-based build

If a developer is using the makefile-based build (vs the newer *meson*), the following [patch](https://www.mail-archive.com/dev@dpdk.org/msg179445.html) will enable Graviton2 optimized built.


## Performance consideration

### Optimal RTE settings

In some older releases, some default parameters are not optimal and developers should check the values:
* RTE_MAX_LCORE, should at least be 64
* RTE_CACHE_LINE_SIZE=64

### Number of LCores used could be misconfigured

Some application, written with x86 architecture in mind, is setting the active dpdk threads or lcores to 1/2 number of vCPU to run single thread per physical core on x86.  However, in Graviton, every vCPU is a real CPU, and developer can use more threads or lcores than same size instance in x86 servers.   For example, a c5.16xl has 64vCPU or 32 physical core,  but some DPDK application would only run on 32 to guarantee one thread per physical core.   In c6g.16xl, developer can use 64 physical cores.


