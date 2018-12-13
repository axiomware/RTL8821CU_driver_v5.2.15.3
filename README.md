# RTL8821CU_RTL8811CU_driver
RTL8811CU driver

The Realtek RTL8811CU-CG is a highly integrated single-chip that supports 1-stream 802.11ac solutions with Multi-user MIMO (Multiple-Input, Multiple-Output) and Wireless LAN (WLAN) USB interface controller. It combines a WLAN MAC, a 1T1R capable WLAN baseband, and RF in a single chip. The RTL8811CU-CG provides an outstanding solution for a high-performance integrated wireless device.:
- USB high speed interface
- 802.11ac/abgn, 802.11ac
- 2.4 GHz Support
- 5.8 GHz Support
- Supports concurrent mode (operates as two virtual WLAN interfaces)
- MIMO config - 1x1
- MU-MIMO
- AC wave2
- 256 QAM

## Linux driver support
Linux uses 8821cu.ko for driver. Check if the USB sub-system recognizes the device.  
```bash
lsusb | grep Realtek
Bus 001 Device 003: ID 0bda:c811 Realtek Semiconductor Corp.
```
```bash
lsmod | grep 8821cu
8821cu               2260992  0
cfg80211              589824  1 8821cu
usbcore               253952  6 ehci_hcd,xhci_pci,btusb,8821cu,xhci_hcd,ehci_pci
```

If 8821cu is present, everything is good to go!

## Building the driver
If the driver is not present, it can be built using the included driver sources.  
 - [Driver sources](./driver) - version : RTL8821CU_WiFi_linux_v5.2.15.3_28211_COEX20171114-2525.20180611
 - [Documents](./document)
 - Build tools - Install build tools, if needed

```bash
# Install build tools
sudo apt-get install build-essential -y
sudo apt-get install bc -y
sudo apt-get install unzip git -y

# install kernel headers
sudo apt-get install linux-headers-$(uname -r)

# check
apt search linux-headers-$(uname -r)
ls -l /usr/src/linux-headers-$(uname -r)
```
#### Option 1 - Direct build

```bash
git clone https://github.com/axiomware/RTL8821CU_driver_v5.2.15.3.git

cd RTL8821CU_driver_v5.2.15.3/driver

# Modify Makefile, if needed (see installation document for details)
# default Makefile is for:
# x86 target
# Concurrent mode enabled
# Monitor mode enabled

# Build
make

#install
sudo make install
sudo modprobe 8821cu
```
#### Option 2 - DKMS build (verified on Debian 9)

DKMS is a system which will automatically recompile and install a kernel module when a new kernel gets installed or updated. To make use of DKMS, install the dkms package, which on Debian (based) systems is done like this:

```bash
sudo apt-get install dkms

DRV_NAME=rtl8821CU
DRV_VERSION=5.2.15.3

git clone https://github.com/axiomware/RTL8821CU_driver_v5.2.15.3.git

# Modify Makefile, if needed (see installation document for details) 
# default Makefile is for:
# x86 target
# Concurrent mode enabled
# Monitor mode enabled

sudo cp -r  RTL8821CU_driver_v5.2.15.3/driver /usr/src/${DRV_NAME}-${DRV_VERSION}

# Build and install
sudo dkms add -m ${DRV_NAME} -v ${DRV_VERSION}
sudo dkms build -m ${DRV_NAME} -v ${DRV_VERSION}
sudo dkms install -m ${DRV_NAME} -v ${DRV_VERSION}

# To remove a driver, do the following:
DRV_NAME=rtl8821CU
DRV_VERSION=5.2.15.3
sudo dkms remove ${DRV_NAME}/${DRV_VERSION} --all
```

## Test Driver

```bash
sudo iwconfig
enp3s0    no wireless extensions.

enp1s0    no wireless extensions.

wlx30eb1f04ecad  unassociated  Nickname:"<WIFI@REALTEK>"
          Mode:Auto  Frequency=2.412 GHz  Access Point: Not-Associated
          Sensitivity:0/0
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality:0  Signal level:0  Noise level:0
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0

enp2s0    no wireless extensions.

lo        no wireless extensions.

wlp0s18u1u2  unassociated  Nickname:"<WIFI@REALTEK>"
          Mode:Auto  Frequency=2.412 GHz  Access Point: Not-Associated
          Sensitivity:0/0
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality:0  Signal level:0  Noise level:0
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0

```

```bash
sudo iw list
Wiphy phy1
        max # scan SSIDs: 9
        max scan IEs length: 2304 bytes
        max # sched scan SSIDs: 0
        max # match sets: 0
        max # scan plans: 1
        max scan plan interval: -1
        max scan plan iterations: 0
        Retry short limit: 7
        Retry long limit: 4
        Coverage class: 0 (up to 0m)
        Supported Ciphers:
                * WEP40 (00-0f-ac:1)
                * WEP104 (00-0f-ac:5)
                * TKIP (00-0f-ac:2)
                * CCMP-128 (00-0f-ac:4)
        Available Antennas: TX 0 RX 0
        Supported interface modes:
                 * IBSS
                 * managed
                 * AP
                 * monitor
                 * P2P-client
                 * P2P-GO
        Band 1:
                Bitrates (non-HT):
                        * 1.0 Mbps
                        * 2.0 Mbps
                        * 5.5 Mbps
                        * 11.0 Mbps
                        * 6.0 Mbps
                        * 9.0 Mbps
                        * 12.0 Mbps
                        * 18.0 Mbps
                        * 24.0 Mbps
                        * 36.0 Mbps
                        * 48.0 Mbps
                        * 54.0 Mbps
                Frequencies:
                        * 2412 MHz [1] (20.0 dBm)
                        * 2417 MHz [2] (20.0 dBm)
                        * 2422 MHz [3] (20.0 dBm)
                        * 2427 MHz [4] (20.0 dBm)
                        * 2432 MHz [5] (20.0 dBm)
                        * 2437 MHz [6] (20.0 dBm)
                        * 2442 MHz [7] (20.0 dBm)
                        * 2447 MHz [8] (20.0 dBm)
                        * 2452 MHz [9] (20.0 dBm)
                        * 2457 MHz [10] (20.0 dBm)
                        * 2462 MHz [11] (20.0 dBm)
                        * 2467 MHz [12] (20.0 dBm) (no IR)
                        * 2472 MHz [13] (20.0 dBm) (no IR)
                        * 2484 MHz [14] (20.0 dBm) (no IR)
        Band 2:
                Bitrates (non-HT):
                        * 6.0 Mbps
                        * 9.0 Mbps
                        * 12.0 Mbps
                        * 18.0 Mbps
                        * 24.0 Mbps
                        * 36.0 Mbps
                        * 48.0 Mbps
                        * 54.0 Mbps
                Frequencies:
                        * 5180 MHz [36] (20.0 dBm) (no IR)
                        * 5200 MHz [40] (20.0 dBm) (no IR)
                        * 5220 MHz [44] (20.0 dBm) (no IR)
                        * 5240 MHz [48] (20.0 dBm) (no IR)
                        * 5260 MHz [52] (20.0 dBm) (no IR, radar detection)
                        * 5280 MHz [56] (20.0 dBm) (no IR, radar detection)
                        * 5300 MHz [60] (20.0 dBm) (no IR, radar detection)
                        * 5320 MHz [64] (20.0 dBm) (no IR, radar detection)
                        * 5500 MHz [100] (20.0 dBm) (no IR, radar detection)
                        * 5520 MHz [104] (20.0 dBm) (no IR, radar detection)
                        * 5540 MHz [108] (20.0 dBm) (no IR, radar detection)
                        * 5560 MHz [112] (20.0 dBm) (no IR, radar detection)
                        * 5580 MHz [116] (20.0 dBm) (no IR, radar detection)
                        * 5600 MHz [120] (20.0 dBm) (no IR, radar detection)
                        * 5620 MHz [124] (20.0 dBm) (no IR, radar detection)
                        * 5640 MHz [128] (20.0 dBm) (no IR, radar detection)
                        * 5660 MHz [132] (20.0 dBm) (no IR, radar detection)
                        * 5680 MHz [136] (20.0 dBm) (no IR, radar detection)
                        * 5700 MHz [140] (20.0 dBm) (no IR, radar detection)
                        * 5720 MHz [144] (20.0 dBm) (no IR, radar detection)
                        * 5745 MHz [149] (20.0 dBm) (no IR)
                        * 5765 MHz [153] (20.0 dBm) (no IR)
                        * 5785 MHz [157] (20.0 dBm) (no IR)
                        * 5805 MHz [161] (20.0 dBm) (no IR)
                        * 5825 MHz [165] (20.0 dBm) (no IR)
                        * 5845 MHz [169] (disabled)
                        * 5865 MHz [173] (disabled)
                        * 5885 MHz [177] (disabled)
        Supported commands:
                 * new_interface
                 * set_interface
                 * new_key
                 * start_ap
                 * new_station
                 * set_bss
                 * join_ibss
                 * set_pmksa
                 * del_pmksa
                 * flush_pmksa
                 * remain_on_channel
                 * frame
                 * set_channel
                 * connect
                 * disconnect
        Supported TX frame types:
                 * IBSS: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * managed: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * AP: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * AP/VLAN: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * P2P-client: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * P2P-GO: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
        Supported RX frame types:
                 * IBSS: 0xd0
                 * managed: 0x40 0xd0
                 * AP: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
                 * AP/VLAN: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
                 * P2P-client: 0x40 0xd0
                 * P2P-GO: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
        WoWLAN support:
                 * wake up on anything (device continues operating normally)
        software interface modes (can always be added):
                 * monitor
        interface combinations are not supported
        Device supports scan flush.
Wiphy phy0
        max # scan SSIDs: 9
        max scan IEs length: 2304 bytes
        max # sched scan SSIDs: 0
        max # match sets: 0
        max # scan plans: 1
        max scan plan interval: -1
        max scan plan iterations: 0
        Retry short limit: 7
        Retry long limit: 4
        Coverage class: 0 (up to 0m)
        Supported Ciphers:
                * WEP40 (00-0f-ac:1)
                * WEP104 (00-0f-ac:5)
                * TKIP (00-0f-ac:2)
                * CCMP-128 (00-0f-ac:4)
        Available Antennas: TX 0 RX 0
        Supported interface modes:
                 * IBSS
                 * managed
                 * AP
                 * monitor
                 * P2P-client
                 * P2P-GO
        Band 1:
                Bitrates (non-HT):
                        * 1.0 Mbps
                        * 2.0 Mbps
                        * 5.5 Mbps
                        * 11.0 Mbps
                        * 6.0 Mbps
                        * 9.0 Mbps
                        * 12.0 Mbps
                        * 18.0 Mbps
                        * 24.0 Mbps
                        * 36.0 Mbps
                        * 48.0 Mbps
                        * 54.0 Mbps
                Frequencies:
                        * 2412 MHz [1] (20.0 dBm)
                        * 2417 MHz [2] (20.0 dBm)
                        * 2422 MHz [3] (20.0 dBm)
                        * 2427 MHz [4] (20.0 dBm)
                        * 2432 MHz [5] (20.0 dBm)
                        * 2437 MHz [6] (20.0 dBm)
                        * 2442 MHz [7] (20.0 dBm)
                        * 2447 MHz [8] (20.0 dBm)
                        * 2452 MHz [9] (20.0 dBm)
                        * 2457 MHz [10] (20.0 dBm)
                        * 2462 MHz [11] (20.0 dBm)
                        * 2467 MHz [12] (20.0 dBm) (no IR)
                        * 2472 MHz [13] (20.0 dBm) (no IR)
                        * 2484 MHz [14] (20.0 dBm) (no IR)
        Band 2:
                Bitrates (non-HT):
                        * 6.0 Mbps
                        * 9.0 Mbps
                        * 12.0 Mbps
                        * 18.0 Mbps
                        * 24.0 Mbps
                        * 36.0 Mbps
                        * 48.0 Mbps
                        * 54.0 Mbps
                Frequencies:
                        * 5180 MHz [36] (20.0 dBm) (no IR)
                        * 5200 MHz [40] (20.0 dBm) (no IR)
                        * 5220 MHz [44] (20.0 dBm) (no IR)
                        * 5240 MHz [48] (20.0 dBm) (no IR)
                        * 5260 MHz [52] (20.0 dBm) (no IR, radar detection)
                        * 5280 MHz [56] (20.0 dBm) (no IR, radar detection)
                        * 5300 MHz [60] (20.0 dBm) (no IR, radar detection)
                        * 5320 MHz [64] (20.0 dBm) (no IR, radar detection)
                        * 5500 MHz [100] (20.0 dBm) (no IR, radar detection)
                        * 5520 MHz [104] (20.0 dBm) (no IR, radar detection)
                        * 5540 MHz [108] (20.0 dBm) (no IR, radar detection)
                        * 5560 MHz [112] (20.0 dBm) (no IR, radar detection)
                        * 5580 MHz [116] (20.0 dBm) (no IR, radar detection)
                        * 5600 MHz [120] (20.0 dBm) (no IR, radar detection)
                        * 5620 MHz [124] (20.0 dBm) (no IR, radar detection)
                        * 5640 MHz [128] (20.0 dBm) (no IR, radar detection)
                        * 5660 MHz [132] (20.0 dBm) (no IR, radar detection)
                        * 5680 MHz [136] (20.0 dBm) (no IR, radar detection)
                        * 5700 MHz [140] (20.0 dBm) (no IR, radar detection)
                        * 5720 MHz [144] (20.0 dBm) (no IR, radar detection)
                        * 5745 MHz [149] (20.0 dBm) (no IR)
                        * 5765 MHz [153] (20.0 dBm) (no IR)
                        * 5785 MHz [157] (20.0 dBm) (no IR)
                        * 5805 MHz [161] (20.0 dBm) (no IR)
                        * 5825 MHz [165] (20.0 dBm) (no IR)
                        * 5845 MHz [169] (disabled)
                        * 5865 MHz [173] (disabled)
                        * 5885 MHz [177] (disabled)
        Supported commands:
                 * new_interface
                 * set_interface
                 * new_key
                 * start_ap
                 * new_station
                 * set_bss
                 * join_ibss
                 * set_pmksa
                 * del_pmksa
                 * flush_pmksa
                 * remain_on_channel
                 * frame
                 * set_channel
                 * connect
                 * disconnect
        Supported TX frame types:
                 * IBSS: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * managed: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * AP: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * AP/VLAN: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * P2P-client: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
                 * P2P-GO: 0x00 0x10 0x20 0x30 0x40 0x50 0x60 0x70 0x80 0x90 0xa0 0xb0 0xc0 0xd0 0xe0 0xf0
        Supported RX frame types:
                 * IBSS: 0xd0
                 * managed: 0x40 0xd0
                 * AP: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
                 * AP/VLAN: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
                 * P2P-client: 0x40 0xd0
                 * P2P-GO: 0x00 0x20 0x40 0xa0 0xb0 0xc0 0xd0
        WoWLAN support:
                 * wake up on anything (device continues operating normally)
        software interface modes (can always be added):
                 * monitor
        interface combinations are not supported
        Device supports scan flush.

```
