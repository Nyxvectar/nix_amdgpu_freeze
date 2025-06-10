_Notice, the log are full of sudden crashes, and the same thing happen up to 30 times or more in the past 3 days, maybe just a part of it is enough.
The repo is a attachment of this [post](https://discourse.nixos.org/t/randomly-flickering-freezing-darken-on-amd-gpu/65416). **Below is the original post**._

---

Jun 09, 2025
An [issue](https://gitlab.freedesktop.org/drm/amd/-/issues/4308) was commited to AMD.

---

Jun 10, 2025
A [new log](https://github.com/Nyxvectar/nix_amdgpu_freeze/blob/main/driverLoad.log) was added.

---

*I'm experiencing persistent display issues with my AMD Radeon RX 6500 XT (4GB GDDR6) and other GPUs on NixOS 25.05 and would appreciate your expertise. Here are the technical details:*

### Hardware & Validation
- **GPU**: AMD RX 6500 XT
- **Failure Modes** (random occurrence):
  - ⚠️ **Screen flickering**
  - ⚠️ Transient black screens (then it died.)
  - ⚠️ Artifacts/corruption
  - ⚠️ **Complete system freeze (requires hard reset)**
![1|690x465, 25%](upload://1Zb5C8QVUxyhTeMnCPL8RJja5z2.jpeg)
![2|666x500, 25%](upload://kUjB9ZWkgaOdDHvUk0XLPGbdvxK.jpeg)

- **Stability Tests**:
  - ✅ **FurMark stress test: 1hr stable at 70°C (2560x1080) and it hadn't crashed when testing**
![3|337x500, 50%](upload://sok8frfjE1aWDXjIRSpFJnQq1TX.jpeg)
  - ✅ **`amdgpu_top` recognizes GPU correctly**

  - ✅ Vulkan/OpenCL workloads functional (Sometimes?)

### Attempted Fixes
1. i have tried the integrated graphics on 9950x and bought a new card
2. i have asked NixOS-CN members in order to solve the problem
3. Tested both Xorg (GNOME) and Hyprland (Wayland) sessions
4. Installed different firmware and mesa or other basis pkgs.

### Diagnostic Evidence
- I tried to get some logs' part and put here, maybe useful.
```
# 1. AMD GPU drivers may have compability problems with wayland.

  .gnome-shell-wr[2287]: Could not issue 'GetUnit' systemd call
  .kgx-wrapped[2809]: vkGetPhysicalDeviceSurfacePresentModesKHR(): A surface is no longer available. (VK_ERROR_SURFACE_LOST_KHR)
  Jun 08 08:29:41 nixos systemd-coredump[12002]: Process 11989 (.Hyprland-wrapp) of user 1000 dumped core.

# 2. The system services maybe causes the problem, too.

  gvfs-udisks2-volume-monitor[2531]: Error launching lsof: No such file or directory
  tracker_indexing_tree_remove: assertion 'TRACKER_IS_INDEXING_TREE (tree)' failed

# 3. The AMD driver's status has err.

  Jun 09 16:36:30 nixos kernel: amdgpu 0000:03:00.0: [drm] *ERROR* dc_dmub_srv_log_diagnostic_data: DMCUB error - collecting diagnostic data
  Jun 09 16:36:33 nixos kernel: amdgpu 0000:03:00.0: amdgpu: SMU: I'm not done with your previous command: SMN_C2PMSG_66:0x00000029 SMN_C2PMSG_82:0x00000000
  Jun 09 16:36:33 nixos kernel: amdgpu 0000:03:00.0: amdgpu: Failed to disable gfxoff!

# 4. The wayland compositor crashed.

  Jun 09 16:36:27 nixos .gnome-shell-wr[1761]: Connection to xwayland lost
  Jun 09 16:36:27 nixos .gnome-shell-wr[1761]: Xwayland terminated, exiting since it was mandatory

# 5. Input method conflict ( may it cause wrong dbus communication ?

  .gnome-shell-wr[2287]: Failed to launch ibus-daemon: No such file or directory
  org.fcitx.Fcitx5.desktop[2466]: Error spawning command-line `lsof -t "/run/media/vespr/Ventoy"': No such file or directory

# 6. Some repo were lost.

  qq.desktop[16759]: dlopen:libcurl.so: cannot open shared object file
  com.tencent.wechat.desktop[17086]: sh: line 1: /usr/bin/lsblk: No such file or directory
  firefox.desktop[2946]: [ERROR wr_glyph_rasterizer::platform::unix::font] Unable to load glyph: 36

```

- If not enough, i also can provide full journal.
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
- But Gentoo had made me too tired, and, i hate arch.

As a boarding school student, i thought maybe i have no time but if i could, i am happy to provide additional testing data as needed. Tku for ur help! :wink:
