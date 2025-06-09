**Subject:** Intermittent Black Screen/Flickering/Freezing on AMD GPU (NixOS 25.05)

*I'm experiencing persistent display issues with my AMD Radeon RX 6500 XT (4GB GDDR6) and other GPUs on NixOS 25.05 and would appreciate your expertise. Here are the technical details,*

### Hardware & Validation
- **GPU**: AMD RX 6500 XT
- **Stability Tests**:
  - ✅ **FurMark stress test: 1hr stable at 70°C (2560x1080) and it hadn't crashed when testing**
  - ✅ **`amdgpu_top` recognizes GPU correctly**
  - ✅ Vulkan/OpenCL workloads functional
- **Failure Modes** (random occurrence):
  - ⚠️ **Screen flickering**
  - ⚠️ Transient black screens (then it died.)
  - ⚠️ Artifacts/corruption
  - ⚠️ **Complete system freeze (requires hard reset)**

### Attempted Fixes
1. i have tried the integrated graphics on 9950x and bought a new card
2. i have asked NixOS-CN members in order to solve the problem
3. Tested both Xorg (GNOME) and Hyprland (Wayland) sessions
4. Installed different firmware and mesa or other basis pkgs.

### Diagnostic Evidence
- Full system logs (last 72hrs) [available here](https://github.com/Nyxvectar/nix_amdgpu_freeze).
- My present NixOS dotfiles [available here](https://github.com/Nyxvectar/dotfiles).
(p.s. i am new to nix so the dotfiles and other configs are shabby.)

### System Configuration
```nix
# Relevant /etc/nixos/configuration.nix fragments:
# Kernel 6.15.1
...
users.users.vespr = {
    isNormalUser = true;
    description = "Vespr";
    extraGroups = [ "networkmanager" "wheel" "video" "render" ];
    packages = with pkgs; [];
    shell = pkgs.fish;
};
...
services.xserver = {
    enable = true;
    displayManager.gdm.enable = true;
    desktopManager.gnome.enable = true;
    videoDrivers = [ "amdgpu" ];
};
...
hardware.graphics = {
    enable = true;
    enable32Bit = true;
};
hardware.graphics.extraPackages = with pkgs; [
    mesa
    amdvlk
    amdenc
];
...
programs.hyprland.enable = true;  # Hyprland as Wayland compositor
```

**Additional Notes**:  
- Issue occurs during light workloads (e.g., web browsing)
- No thermal throttling observed (`sensors` shows <75°C junction temp)
- **The distros i past used on the same hardware platform didnot have this issue**

As a boarding school student, i thought maybe i have no time but if i could, i am happy to provide additional testing data as needed. Tku for ur help!

