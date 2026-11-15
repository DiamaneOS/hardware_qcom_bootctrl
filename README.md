# Qualcomm boot control

DiamaneOS's fork of Qualcomm's A/B boot-control implementation from CodeLinaro
(`clo/la/platform/hardware/qcom/bootctrl`). Upstream history is kept, and
DiamaneOS changes are the commits on top.

## Build

- The AIDL service uses `libboot_control_qti` for Qualcomm slot attributes and
  links the GPT/UFS helpers from `vendor/qcom/opensource/recovery-ext`.
- `libboot_control_qti` exports its HIDL header dependency: its public
  interface uses the HIDL snapshot-merge status type.
- The service builds as a vendor variant and a recovery variant.
- `Android.mk.legacy` is not evaluated. It holds the old `bootctrl.<platform>`
  module definitions. The build uses the AIDL service and its versioned
  implementation libraries.

## Privileges

- The vendor service runs as `vendor_bootctl` (`aidl/config.fs`) with
  CAP_SYS_RAWIO only, for the UFS BSG ioctl that reads and switches the boot LUN.
- It logs to logd, since `/dev/kmsg` is root-only.
- The product adds `aidl/config.fs` to `TARGET_FS_CONFIG_GEN`.
- The product gives the `vendor_bootctl` group read-write access (ueventd) to the GPT
    disks holding A/B partitions, the misc partition and `/dev/ufs-bsg*`.
- The recovery variant keeps root and the kernel log: recovery runs without
  the vendor passwd file and without logd.

## Boot LUN

- The bootloader refuses to boot when the UFS boot LUN and the GPT active slot
  disagree, so the service never leaves them apart.
- A failed switch puts the old slot back exactly, with its successful flag and
  retry count. When the LUN write reports an error but the LUN already shows the
  new slot, the switch stays.
- Before a new slot is marked successful, the service checks the boot LUN. When
  the other slot is a working fallback, it reads the LUN, writes the same value
  and reads it back. If that fails, the slot stays unmarked and the bootloader
  returns to the old build after its retries. Without a fallback it only reads
  and logs, since refusing would end in fastboot.

## Upstream and licensing

Upstream copyright headers and `NOTICE` are kept. Licence texts are in
`LICENSES/`.
