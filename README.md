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


## Upstream and licensing

Upstream history, copyright headers and NOTICE are retained. Individual files
carry their applicable Linux Foundation, Apache-2.0 and BSD-3-Clause-Clear
terms; these build adaptations do not relicense upstream code.
