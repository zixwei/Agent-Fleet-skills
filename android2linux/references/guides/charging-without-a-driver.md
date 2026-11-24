# Charging a phone with no charger driver

What you can rely on when a phone's charger chip has no mainline Linux driver, how to charge it, and how to measure what is happening. The example is the PM6150 charger ("SMB5") in Snapdragon 720G phones.

## The missing driver

The phone's charger is part of the **PM6150** PMIC and belongs to Qualcomm's **"SMB5"** charger generation. Mainline, and every `sm7125-mainline` branch up to `sm7125-7.4.0-next-20261113`, only carries **`qcom_smbx`** (formerly `qcom_pmi8998_charger`), which targets PMI8998/PM660: the older **"SMB2"** generation. There is no SMB5 driver anywhere upstream.

Ubuntu Touch charges properly because it runs Android's kernel, which has Qualcomm's **`qpnp-smb5`** driver. Under a mainline kernel, nothing drives the charger.

## What the hardware defaults give you

With no driver, the chip runs on its **power-on defaults**:

- **No fast charging.** It trickles at whatever the default mode allows.
- **No status reporting.** The battery `status` reads "Unknown" all the time; the USB-C type supply reports `online=0`; `power_role` can show `[source]` while `usb_vbus` is off: nothing is negotiated.

What that means in practice:

| Setup | Result |
|---|---|
| USB-C **wall charger**, **screen off** | Works, slowly: capacity and voltage climb over half an hour |
| **PC USB port** | Roughly break-even with the screen on; the battery still supplies part of the load |
| Screen on, heavy use | Drains, about 10 %/hour at full brightness with a browser open |

The power chip still supplies part of the load on its own, which is why a PC port can nudge the gauge up rather than only slowing the drain. But it is not real charging, and with the screen on it is close to a wash.

**The practical advice:** charge from a wall charger with the screen off. It is slow but it works.

## How to measure charging without trusting the gauge

The battery gauge (`qcom_qg`) reads capacity and voltage, but its current reading is unreliable and its status is always "Unknown". Measure by **capacity and voltage over time** instead:

- Note capacity (%) and voltage (mV), wait a known interval, note them again.
- A rising voltage alongside rising capacity is real charging.
- Use `current_now` only for its **sign**: positive means drawn from the battery, negative means charging (into the battery). Do not trust its magnitude; the gauge can read only a few hundred mA while the voltage climbs clearly.

Read the values from the standard power-supply sysfs nodes for the battery. Because the status field is useless here, always cross-check with capacity and voltage.

## Suspend

Suspend is **`s2idle`** only. It works: the phone sleeps and resumes, with a kernel warning logged on resume (benign). Suspend matters for charging because a suspended phone with the screen off is the best case for the trickle-charge defaults: it is the state in which a wall charger actually gains.

## What a real fix would take

This is not a setting you can toggle. A proper fix needs:

1. **Port SMB5 support** into the kernel charger driver. There is a proposed upstream direction for adding it to `qcom_smbx`; it is not there yet.
2. **A charger node in the device tree** describing this PMIC's charger to that driver.
3. **Building your own kernel** with both, since neither is in the current kernel you are running.

Until then, watch `sm7125-mainline` for charger work. The status is: it charges slowly on its defaults, and that will not change without kernel work.

## Last verified

Driver absence checked against `sm7125-mainline` up to `sm7125-7.4.0-next-20261113`, and suspend behavior: 2026-11-21. Checked 2026-11-21.
