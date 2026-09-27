# CPUID Fault Emulation (DKMS)

A lightweight Linux kernel module that emulates architectural CPUID faulting on AMD processors (Zen 1–3) using AMD SVM (Secure Virtual Machine) hardware virtualization.

Includes a systemd oneshot service and a Polkit rule allowing unprivileged users in the `wheel` group to seamlessly load and unload the module on-demand without entering a password.

## Why is this needed?

AMD processors prior to Zen 4 lack hardware MSR support for CPUID faulting. Certain Windows games running through Wine, Proton (such as Proton-CachyOS or GE-Proton), and [linuwux-runtime](https://github.com/brcly/linuwux-runtime) require CPUID faulting to emulate specific CPU features and bypass anti-tamper or environment checks.

Because this module uses AMD SVM, it conflicts with KVM. The included systemd service automatically handles unloading KVM before engaging emulation and safely restoring KVM when stopped.

## Features

- **DKMS support**: Automatically compiles across kernel updates.
- **Systemd service (`cpuid-fault-emulation.service`)**: Manages KVM swapping and module loading.
- **Polkit rule (`10-cpuid-fault-emulation.rules`)**: Allows members of the `wheel` group to start/stop the service without `sudo` password prompts.
- **Safe devirtualization**: Devirtualization is restricted to Ring 0 (`CPL == 0`).
- **Power management**: Automatically suspends/resumes virtualization across system sleep/hibernate.

## Usage

Start emulation:
```sh
systemctl start cpuid-fault-emulation.service
```

Stop emulation (restores KVM):
```sh
systemctl stop cpuid-fault-emulation.service
```

## Credits

- **LinUwUx from CS.RIN.RU**: Original creator and author of the `cpuid_fault_emulation` kernel module.
- **[loystonpais](https://github.com/loystonpais/linuwowo)**: Inspiration for the systemd service integration and KVM management.

## License

GPL
