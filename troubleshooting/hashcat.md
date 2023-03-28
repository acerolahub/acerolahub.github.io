# hashcat

Hashcat usually uses GPU to work. Well it's faster. However you can force it to use the CPU as well. In order for it to work on your AMD CPU, you need to install Intel CPU Runtime for OpenCL Applications.

Hashcat uses OpenCL is that it can take advantage of the parallel processing capabilities of modern GPUs (Graphics Processing Units) to perform password cracking much faster than using only the CPU.

No devices found/left.

clGetPlatformIDs(): CL\_PLATFORM\_NOT\_FOUND\_KHR

### AMD and Hashcat on Linux 

You can find Intel CPU Runtime for OpenCL in your distribution's package repository.

On **Kali Linux (Debian, Linux Mint, Ubuntu)** to install, run the command:



<details>

<summary>dd</summary>



</details>

<table data-view="cards"><thead><tr><th></th><th></th><th></th></tr></thead><tbody><tr><td></td><td></td><td>cdd</td></tr><tr><td></td><td>dd</td><td></td></tr><tr><td></td><td>dd</td><td></td></tr></tbody></table>

| 1 | `sudo` `apt install` `firmware-misc-nonfree intel-opencl-icd` |
| - | ------------------------------------------------------------- |

On **Arch Linux (BlackArch)** to install, run the command:

| 1 | `sudo` `pacman -S linux-firmware intel-compute-runtime pocl` |
| - | ------------------------------------------------------------ |

{% embed url="https://miloserdov.org/?p=6507" %}

{% embed url="https://miloserdov.org/?p=4726" %}
