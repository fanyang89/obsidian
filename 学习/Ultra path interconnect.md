[Intel Ultra Path Interconnect (UPI) Bandwidth](https://stackoverflow.com/questions/64116638/intel-ultra-path-interconnect-upi-bandwidth)

> A google search for "intel UPI bandwidth" found [nas. nasa. gov/hecc/support/kb/…](https://www. nas. nasa. gov/hecc/support/kb/skylake-processors_550. html#:~:text=The%20UPI%20runs%20at%20a, second%20 (GB%2Fs)). which connects GT/s and GB/s: 2 links between a pair of sockets means 41.6 GB/s aggregate bidirectional bandwidth, or 20.8 GB/s each direction, 10.4 per link. Seems pretty clear if that source is accurate.

Example from the Intel Xeon Platinum 8160 (2 UPI links between chips):

- Local Bandwidth for Reads (each socket) ~112 GB/s
- Remote Bandwidth for Reads (one-direction at a time) ~34 GB/s
- Local bandwidth scales perfectly in two-socket systems, and remote bandwidth also scales very well when using both sockets (each socket reading data from the other socket).
