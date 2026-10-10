# Qualcomm boot control

DiamaneOS's fork of Fairphone's Qualcomm A/B boot-control implementation
(Fairphone Gerrit `platform/hardware/qcom/bootctrl`). Upstream history is
kept; DiamaneOS changes are the commits on top.

## Build

- The AIDL service uses `libboot_control_qti` for Qualcomm slot attributes and
  links the GPT/UFS helpers from `vendor/qcom/opensource/recovery-ext`.
- `libboot_control_qti` exports its HIDL header dependency: its public
  interface uses the HIDL snapshot-merge status type.
- The service builds as a vendor variant and a recovery variant.
- `Android.mk.legacy` is not evaluated. It holds the old `bootctrl.<platform>`
  module definitions; the build uses the AIDL service and its versioned
  implementation libraries.

## Privileges

- The vendor service runs as `vendor_bootctl` (`aidl/config.fs`) with
  CAP_SYS_RAWIO only, for the UFS BSG ioctl that switches the boot LUN.
- It logs to logd, since `/dev/kmsg` is root-only.
- The product:
  - adds `aidl/config.fs` to `TARGET_FS_CONFIG_GEN`;
  - gives the `vendor_bootctl` group read-write access (ueventd) to the GPT
    disks holding A/B partitions, the misc partition and `/dev/ufs-bsg*`.
- The recovery variant keeps root and the kernel log: recovery runs without
  the vendor passwd file and without logd.

## Upstream and licensing

Upstream copyright headers and `NOTICE` are kept. Licence texts are in
`LICENSES/`.
