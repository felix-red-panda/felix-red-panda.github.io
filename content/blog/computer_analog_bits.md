+++
title = "Computers have analog bits*"
date = "2024-03-06T18:34:57+01:00"

#
# description is optional
#
# description = "An optional description for SEO. If not provided, an automatically created summary will be used."

# tags = ['electrical engineering', 'error correction']
+++

<!-- # Computers are analog systems -->
<!-- # Computers have analog bits -->

Abstractions are leaky. That's commonly accepted wisdom in software engineering circles. There's even a "Law of Leaky Abstractions", commonly phrased as "All non-trivial abstractions are (to some degree) leaky".

This law also applies to the hardware computer systems consist of in general.

To illustrate, consider a common NAND flash storage: In the past decades a lot of progress in computing was enabled by cramming ever more storage on tiny chip dies with billions or even trillions of individual transistors. Initially one flash storage cell stored one bit, so each cell had two possible states: fully charged or fully discharged: 1 or 0. This is called Single-level Flash Cell (SLC).

But storing one bit per cell is expensive (you need a dedicated tiny bit of hardware to store a single bit!), so engineers figured they'd store 2 bits per flash cell. This tech is called Multi-level Flash Cell (MLC) and it made it possible to store two bits per cell. This is achieved by having the flash cell now hold one state out of 4 possible states. You can think of those possible states as the flash cell holding an electric charge between 0 (meaning both bits are 0) or 1 (=both bits are 1) just as before but now it can also hold two partially charged states in between: Think of them as 33% or 66% of fully charged, for the states of one bit 0 and the other one and vice versa. Now you can store two bits in one cell [^1].

MLC tech became popular in SSD storage around 2010 because of it enabling double the amount the storage at the same NAND flash price. In the past ~10 years flash manufacturers increasingly migrated from MLC storage to TLC storage. Meaning each flash cell stores three bits, so each flash cell needs to be able to store one of 8 different charge states between 0 and 1. QLC NAND for storing 4 bits per cell in 16 possible states also exist.

Now keep in mind these flash cells are really tiny and hold only minuscule amounts of electric charge. The charge is so tiny that it's expected for it to reduce over time when your computer isn't plugged in for a few months or even much faster when you're storing your computer at a high temperature.
When you then make the SSD controller read your data from NAND flash, the SSD needs to measure a tiny amount of electricity and needs to evaluate from each read value and each relatively small deviation from the amount of charge. If the amount of charge is measured wrong it could mean 3 bits of your data can't be retrieved anymore. In practice there are sophisticated error correction algorithms that manage to fix most of the read errors automatically so this often only presents to the user as temporarily slow drive. SSDs are for the most part capable to fix themselves by automatically re-writing the data when they notice the data has degraded.

The SSD controller has to measure a fundamentally analog signal emitted by an analog amount of charge that was trapped in the flash cells at write time and calculate back what amounts of charge could have created them.

Digital representation is a convention how to treat the analog world, in many cases that leads to problems. Usually those problems were foreseen by the programmers of the firmware and are fixed silently but in more bleeding edge scenarios these problems make compute jobs crash ([blog post by Yi Tay](https://www.yitay.net/blog/training-great-llms-entirely-from-ground-zero-in-the-wilderness) that tells a story of how not all compute clusters are the same even when built from the same chips).

The realm of data that is at its core analog lurks is sometimes more of a problem (e.g. in optical network cables, mechanical hard drives [^3], copper traces on a PCB that route PCIe signals, DRAM memory[^4]) and sometimes much less (e.g. with USB 1.1). Especially in high density storage expected read error always needs to be carefully balanced with the amount of error correction the storage controller can provide.

*This is one of the first blog posts I publish on this blog, I'd love to hear your feedback on the site formerly known as twitter to [@felix_red_panda](https://twitter.com/felix_red_panda) my DMs are open*


[^1] of course that comes with downsides: 1) the lifetime cycle count goes down by a lot 2) the stress induced leakage current (SILC) of the flash cell becomes worse at a much higher rate 3) the SSD controller needs to be more powerful to manage more data per read operation and more error correction

[^2] stress induced leakage current

[^3] modern hard drive use tech such as HAMR (a laser pulse heats up a tiny spot on the drive for writing) so they need very advanced write heads to be able to write a magnetic signal in such a tiny spot it can't be to written anymore by normal magnet-only write heads

[^4] e.g. [row hammer](https://en.wikipedia.org/wiki/Row_hammer)-like security attacks
