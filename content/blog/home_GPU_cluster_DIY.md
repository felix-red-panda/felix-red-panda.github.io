+++
title = "Building a GPU Cluster at home"
# title = "Home GPU Cluster DIY"
date = "2024-01-12T16:43:42+01:00"

# description = "An optional description for SEO. If not provided, an automatically created summary will be used."

tags = []
+++

*this post is still WIP*

incomplete list of things to consider when building a GPU server for home:

- PCIe
    - get enough PCIe bandwidth (both mainboard and CPU) (consumer CPUs typically only come with 16 lanes of PCIe in total, 1 GPU has 16 lanes, PCIe lanes can be shared by multiple GPUs but this decreases memory throughput significantly)
    - keep the generation of PCIe in mind
    - keep the configuration of the PCIe lanes and their allocation in the Motherboard bios in mind
electricity:
    - if you're in the US/Canada/Japan, keep in mind that you can't draw more than ~1600W from a single circuit continuously: (16A current limit for a typical US circuit * 120V mains voltage * 80% max continuous load = 1536 Watts). In most of the rest of the world this limit is ~2x higher (with ~230V grid voltage) and so in practice very hard to exceed, so don't worry too much about it (you can easily find out your grid local grid voltage with a google search)
    - keep local electricity prices in mind (running such a GPU rig 24/7 can be very expensive)
- buy a high quality power supply
    - buying a good quality power supply (instead of a no name one) is very important because power supplies are safety relevant. High quality power supplied tend to have more more reliable features that can be crucial to prevent e.g. a fire when the computer malfunctions (e.g. power supplies come with overload protection to prevent overheating (that means a good quality power supply will shut itself and all it's load down instead of overheating and potentially causing a fire)
    - buy from a reputable manufacturer (Thermaltake, Silverstone, Corsair, Cooler Master, Seasonic, EVGA, be quiet!)
    - prefer long warranty periods from a reputable manufacturer (this can be read as a sign of confidence the manufacturer has in it's choice of components in the power supply), power supplies with 10 year warranty aren't uncommon
    - at time of writing e.g. Thermaltake ToughPower GF3 1650W ATX 3.0 is a reasonably priced option with 10 years of warranty
    - power supplies tend to have an efficiency rating of 80plus (white, bronze, gold, platinum): don't bother with that, it's not a great certifation process, focus on buying a high quality unit (it doesn't matter in practice whether it's 80plus gold or 80 plus platinum)
    - if you're in North American/Japan: if you have more than 3 GPUs you'll probably need 2 power supplies that you run from 2 different electrical circuits

- keep cooling requirements in mind: if you're dumping >1kw continously (if you have 3 or more RTX 4090/3090 GPUs) in the air you need a plan what to do with the heat, especially if you're in a single room (maybe opening a window is enough? But plan for that)
- GPU choice
    - consider 4060Ti cards with 16Gb (especially for LLM inference, because a lot of VRAM for relatively cheap) otherwise RTX 3090, RTX 4090 or A6000 cards (be aware there are different generations of the A6000 cards)
    - different cards use different amounts of power (and need different power supplies)
    - different cards have different memory speeds (more memory speed is better)
- consider used (but not old) server hardware (you might be able to get a decent combo of RAM, CPU+Mainboard)
    - keep in mind server hardware can be pretty loud and use more electricty when idle
- memory
    - keep in mind the difference of ECC memory (for server hardware; ECC RAM and Reg ECC RAM are typically not intercompatible, your system typically will only support one kind and not the other) and non ECC (for consumer and prosumer hardware)
    - find out the number of memory channels your CPU has and buy at least as many RAM sticks as the number of memory channels (depending on your CPU 2, 4 or 8 memory channels)
- storage
    - buy a HDD to keep models around you're not using frequently (HDDs are much cheaper per unit of storage than SSDs)
    - especially if you have multiple NVMe drives: keep in mind that those are using PCIe lanes too
    - buy NVMe SSDs for much faster model loading and storing (those cost about the same as SATA SSDs but are much faster, prefer PCIe gen 4 SSDs over PCIe gen 3 SSDs)
    - NVMe SSDs have much better access latencies than HDDs or SATA SDDs -> depending on how the dataset is stored on disk that can be an advantage
    - Consider creating regular backups to an external HDD to recover in case of hardware defects or user error (user error one of the leading reasons for data loss). On linux the btrfs file system provides a easy way to create incremental backups with the btrfs snapshots feature. Borg backup is a file system independent alternative to that
    - storage speed is nice to have but in a lot cases slower storage will be more than sufficient as the throughput in MB/s of training achieved by home GPUs is tiny. Reading and writing neural net checkpoints is by far the most storage bandwidth intensive task you're likely to encounter
- software
    - consider something like Arch Linux or Manjaro Linux to have an easy time with the CUDA setup
    - use ssh + VS Code ssh access for local network access to the server
    - use sshfs to map working directories from the server into a local folder for convenient working access
    - format the disks with the btrfs filesystem to make it easy to create backups with the btrfs snapshot functionality
- cooling/ventilation
    - don't bother with water cooling (it's usually pretty expensive and introduces a bunch of potential failure points)
    - choose an open air (mining rig style) computer case to maximize ventilation: to increase ventilation. keep in mind the cooler the hardware is the longer it tends to last
    - power limit your GPUs to 80%-90% of their stock power limit `sudo nvidia-smi -i 0 -pl 300` -pl is the power limit in watts (here 300 watts) and -i is the number of the card the command is targetting (here card number 0)
    - this reduces the heat output, reduces power consumption and increases the statistical lifetime of your GPUs
    - consider reducing the max clock of your CPU `sudo cpupower frequency-set --max <clock>` to reduce heat and power conusmption

- don't buy RGB stuff (most of the time it's just a significant price increase and it uses more power. Turning RGB LEDs off with Linux utils can be non-trivial as I was forced to find out)
- https://github.com/hitorilabs/navi for more practical tips
- slightly outdated guide on more considerations which GPUs to buy https://timdettmers.com/2023/01/30/which-gpu-for-deep-learning/

*Send feedback to me on twitter [@felix_red_panda](https://twitter.com/felix_red_panda) my DMs are open. I'd love to get a photo of your GPU rig if this post had any part in helping you design and build it :)*