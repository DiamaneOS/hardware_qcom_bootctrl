# Qualcomm boot control

DiamaneOS maintains Fairphone's Qualcomm A/B boot-control implementation.
The `android17` branch starts from Fairphone Gerrit
`platform/hardware/qcom/bootctrl` at
`7733efc0cce1149ce909e41cdf43164ab7bed8bf`.

The AIDL service uses `libboot_control_qti` for Qualcomm slot attributes and
links the GPT/UFS helpers supplied by `vendor/qcom/opensource/recovery-ext`.
The library exports its HIDL header dependency because its public interface
uses the HIDL snapshot-merge status type. This preserves the existing
implementation and supports the normal and recovery service variants.

Native ARM64 builds of both variants passed against Android 17. This is build
evidence, not verification of device slot switching, recovery or updates.

Upstream history, copyright headers and NOTICE are retained. Individual files
carry their applicable Linux Foundation, Apache-2.0 and BSD-3-Clause-Clear
terms; these build adaptations do not relicense upstream code.

The root `Android.mk.legacy` is retained for source history but not evaluated.
It defined the old `bootctrl.<platform>` modules against the legacy updater
library whenever A/B updates were enabled. The product uses the source AIDL
services and their versioned implementation libraries instead. GPT/UFS support
remains in the recovery-support project.
