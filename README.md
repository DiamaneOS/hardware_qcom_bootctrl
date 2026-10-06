# Qualcomm boot control

DiamaneOS maintains Fairphone's Qualcomm A/B boot-control implementation. The
`android17` branch starts from Fairphone Gerrit `platform/hardware/qcom/bootctrl`
at `7733efc0cce1149ce909e41cdf43164ab7bed8bf`.

- The AIDL service uses `libboot_control_qti` for Qualcomm slot attributes and
  links the GPT/UFS helpers from `vendor/qcom/opensource/recovery-ext`.
- The library exports its HIDL header dependency because its public interface
  uses the HIDL snapshot-merge status type. This keeps the existing
  implementation and supports the normal and recovery service variants.
- The vendor service runs as `vendor_bootctl` (`aidl/config.fs`) with
  CAP_SYS_RAWIO only, for the UFS BSG ioctl that switches the boot LUN; stock
  runs it as root. It logs to logd, since `/dev/kmsg` is root-only. The product:
  - adds `aidl/config.fs` to `TARGET_FS_CONFIG_GEN`;
  - gives the `vendor_bootctl` group read-write access (ueventd) to the GPT
    disks holding A/B partitions, the misc partition and `/dev/ufs-bsg*`.
- The recovery variant keeps root and the kernel log: recovery runs without
  the vendor passwd file and without logd.
- Native ARM64 builds of both variants passed against Android 17. That is build
  evidence, not verification of device slot switching, recovery or updates.
- The root `Android.mk.legacy` is kept for source history but not evaluated. It
  defined the old `bootctrl.<platform>` modules against the legacy updater
  library whenever A/B updates were enabled. The product uses the source AIDL
  services and their versioned implementation libraries instead; GPT/UFS
  support stays in the recovery-support project.

## Upstream and licensing

Upstream history, copyright headers and NOTICE are retained. Individual files
carry their Linux Foundation, Apache-2.0 and BSD-3-Clause-Clear terms; these
build adaptations do not relicense upstream code.
