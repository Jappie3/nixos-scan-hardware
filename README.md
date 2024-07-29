# nixos-scan-hardware

## what

Stole the Perl script from https://github.com/NixOS/nixpkgs/blob/b34ee7d48436cb46343de9daf5abdc3bcab0dec3/nixos/modules/installer/tools/nixos-generate-config.pl, ripped a bunch of stuff out & made some edits so it could run with just Perl installed. Full credit for the original script goes to the NixOS maintainers who wrote it. When ran, it outputs a normal `hardware-configuration.nix` & a ***very*** minimal `configuration.nix` that contains all the kernel modules the script picked up, as well as the bootloader config and some other stuff.

## why

Main usecase: running this on non-NixOS hosts you want to provision with `nixos-anywhere` so that you don't accidentally forget a module that's critical for booting (resulting in you having to haul a monitor & keyboard to the server). If you have any suggestions / ideas, feel free to open a MR ^^

## how

Running: `perl nixos-generate-config.pl` (it might need root permissions for some commands, consider running w/ sudo or an equivalent in that case). The script creates the 2 output files in the current directory.

Example of `configuration.nix` output:

```nix
{lib, config, ... }: {
    boot.initrd.availableKernelModules = [ "nvme" "xhci_pci" "ahci" "usbhid" ];
    boot.initrd.kernelModules = [ ];
    boot.kernelModules = [ "kvm-amd" ];
    boot.extraModulePackages = [ ];

    # Use the systemd-boot EFI boot loader.
    boot.loader.systemd-boot.enable = true;
    boot.loader.efi.canTouchEfiVariables = true;


    # attrs:
    hardware.cpu.amd.updateMicrocode = lib.mkDefault config.hardware.enableRedistributableFirmware;
}
```