# Overclock ThinkPad T430 Panel (B140RW02 V1) to 90Hz on Fedora 41

This guide walks you through overclocking the stock panel (AUO B140RW02 V1) on the Lenovo ThinkPad T430 from 60Hz to 90Hz using a custom EDID on **Fedora 41**.

> **Disclaimer**
> This mod involves flashing the EDID (Extended Display Identification Data) of your panel. Proceed at your own risk. Writing incorrect EDID data can result in a non-functional display. Triple-check the address before writing anything!



## ⚙️ Requirements

First, install the necessary tools:

```bash
sudo dnf install i2c-tools python3-i2c-tools edid-decode
```

Clone the `edid-rw` utility:

```bash
git clone https://github.com/bulletmark/edid-rw
cd edid-rw
```


## 🔍 Identify the Correct EDID Address

Run the following command and increment the address number (`0`, `1`, `2`, etc.) until you find the correct one:

```bash
sudo ./edid-rw 0 | edid-decode
```

Once the correct address is found, you should see output similar to:

```
Block 0, Base EDID:
  EDID Structure Version & Revision: 1.4
  Vendor & Product Identification:
    Manufacturer: AUO
    Model: 8510
    Made in: week 33 of 2010
  ...
  Detailed Timing Descriptors:
    DTD 1: 1600x900    60.00 Hz ...
    DTD 2: 1600x900    40.00 Hz ...
    ...
  Alphanumeric Data String: 'AUO          '
  Alphanumeric Data String: 'B140RW02 V1  '
```

You should only see 60Hz and possibly 40Hz entries before flashing the mod.


## 💾 Backup Your Original EDID

Once you've confirmed the EDID address (replace `0` with your address if different):

```bash
sudo ./edid-rw 0 > edid_backup.bin
```

Store this file safely in case you need to revert later.


## ⬇️ Download the Modded EDID

Download the modded EDID binary file:

* [t430\_b140rw02v1\_mod.bin](https://github.com/hauptkern/t430-b140rw02v1-oc/raw/refs/heads/main/t430_b140rw02v1_mod.bin)

This file includes a modified timing descriptor to enable 90Hz refresh rate.


## ✍️ Flash the New EDID

> ⚠️ **Triple-check the EDID address before proceeding!**

Write the new EDID to the correct address:

```bash
sudo ./edid-rw -w 0 < t430_b140rw02v1_mod.bin
```


## 🔁 Reboot and Enjoy 90Hz

Reboot your system:

```bash
sudo reboot
```

Go to your display settings, and you should now have the option to select **90Hz** as the refresh rate.


## ✅ Verification

You can verify the new EDID with:

```bash
sudo ./edid-rw 0 | edid-decode
```

Look for a DTD entry like:

```
DTD 1: 1600x900    90.00 Hz ...
```


## 📌 Notes

* Not all B140RW02 V1 panels will overclock stably to 90Hz. Test for artifacts, screen tearing, or instability.
* You can always restore the original EDID using your backup:

```bash
sudo ./edid-rw -w 0 < edid_backup.bin
```
