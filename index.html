# Linux Audio for Musicians
### A practical guide for 2026 — written for people who think in signal chains, not system architecture

---

## Before we start: what this guide assumes

You know what a signal chain is. You've plugged a guitar into a DI, routed it to a channel strip, sent it to a bus, printed it to a track. You think in terms of *in*, *out*, *through*, and *why is there a click every 30 seconds*.

This guide does not assume you know what a kernel module is.

It does assume you want to record real instruments, with real latency requirements, on Linux — and that you're tired of documentation written by people who find the architecture interesting rather than the music.

---

## Part 1: The mental model (read this, skip nothing)

### Think in signal chains, not layers

Every piece of Linux audio documentation you'll find starts with a diagram that looks like this:

```
Application → PulseAudio/JACK → ALSA → Hardware
```

This is technically accurate and practically useless, for the same reason that explaining a mixing console by starting with its power supply is technically accurate and practically useless.

Here's the model that actually helps:

**Your audio interface is a patch bay.** It has physical inputs and outputs, a fixed number of channels, and it runs at a clock rate you set. Everything else in the system is trying to get signals to and from that patch bay reliably, without dropping chunks.

**PipeWire is the studio.** It's the environment in which all your applications — your DAW, your browser, your software synths — exist simultaneously and can route audio between each other and to the patch bay. It manages who gets to talk, when, and in what order.

**ALSA is the cable from the patch bay to the wall.** It's the low-level driver that lets PipeWire talk to your hardware at all. In normal operation, you will never think about it. It will only matter if your interface isn't showing up at all, in which case there's a troubleshooting section later.

That's the whole stack. ALSA talks to hardware. PipeWire routes everything. Your apps talk to PipeWire.

A note on history: you'll read a lot about PulseAudio and JACK as if they're things you need to choose between. On any modern Linux distribution in 2026, PipeWire has replaced both. It speaks their languages for compatibility — apps written for JACK or PulseAudio work without modification — but PipeWire is the actual ground truth. Start there.

---

## Part 2: Latency — the thing that actually matters

### The bet you're making

Every audio system works by processing audio in chunks. Your interface captures some samples, hands them to the computer, the computer processes them, hands them back, the interface plays them out. The size of that chunk — the **buffer** — determines your latency.

Here's the mental model: **your buffer size is a bet you're making about how long your CPU has to process a chunk before the next one arrives.**

If your buffer is 128 samples at 48kHz, you're telling the system: "I bet I can process 2.67ms worth of audio, consistently, every 2.67ms, with no interruptions." If you win that bet every time, you hear nothing wrong. If you lose it — the CPU gets interrupted by something, a USB packet is late, a driver hiccuped — you get an **xrun**: a dropout, a click, a glitch.

This is why the advice "just lower your buffer size" is incomplete. A lower buffer is a harder bet. Whether you can win it depends on your hardware, your kernel configuration, your interface's USB implementation, and what else your system is doing.

### The numbers

At 48kHz (standard for most interfaces):

| Buffer size | Latency (one-way) | Practical feel |
|---|---|---|
| 64 samples | 1.3ms | Aggressive. Requires optimized kernel, nothing else running. |
| 128 samples | 2.7ms | Good for most tracking. The target for serious work. |
| 256 samples | 5.3ms | Fine for most musicians. Not noticeable on guitar or keys. |
| 512 samples | 10.7ms | Getting perceptible. Okay for mixing, not ideal for tracking. |
| 1024 samples | 21.3ms | Noticeable. Only use this if lower causes xruns. |

Round-trip latency (in to out) is roughly double, plus a fixed hardware offset that varies by interface. Your interface's hardware buffer adds a few milliseconds on top — check your device's specs.

**The practical starting point:** Use 256 samples until you have a reason not to. If you're tracking live instruments and the delay is perceptible, try 128. If you get xruns at 128, you need to look at your kernel setup before blaming your interface.

### The realtime kernel question

Consumer Linux kernels are not optimized for audio. They can be interrupted by other system processes mid-chunk, which causes xruns at low buffer sizes. A **realtime (RT) kernel** reduces this dramatically by giving audio processes the ability to preempt almost everything else.

On CachyOS, you already have good options. The `linux-cachyos` kernel includes BORE scheduling and other latency improvements. For serious tracking work, `linux-cachyos-rt` or `linux-cachyos-lts-rt` gives you a fully preemptible kernel.

Install it, set it as default in your bootloader, reboot, and your achievable minimum buffer size will likely drop by half.

You'll also want to ensure your user is in the `realtime` group:

```bash
sudo usermod -aG realtime $USER
```

Log out and back in. PipeWire will automatically use realtime scheduling when available.

---

## Part 3: Getting PipeWire right

### Verify what you have

First, confirm PipeWire is running and your interface is visible:

```bash
pw-cli list-objects | grep -i node
```

You should see your interface listed. If you see it, PipeWire has found it. If you don't, that's an ALSA problem — skip to the troubleshooting section.

The tool you'll use most for monitoring is `pw-top`. Run it in a terminal while you work:

```bash
pw-top
```

This shows you every active audio node, its current buffer size (quantum), and — critically — whether it's experiencing xruns (the `E` column). This is your real-time view into what the stack is actually doing.

### Where configuration lives

PipeWire follows a consistent Linux convention: system defaults live in `/usr/share/pipewire/` and you override them in `~/.config/pipewire/`. Never edit the system files — your changes will be overwritten by package updates.

Create this structure if it doesn't exist:

```bash
mkdir -p ~/.config/pipewire/pipewire.conf.d
mkdir -p ~/.config/pipewire/pipewire-pulse.conf.d
mkdir -p ~/.config/wireplumber/wireplumber.conf.d
```

Configuration is split across fragments — individual `.conf` files that are loaded in filename order. Name them with number prefixes to control load order: `10-timing.conf`, `20-devices.conf`, and so on.

After any configuration change:

```bash
systemctl --user restart pipewire pipewire-pulse wireplumber
```

No need to reboot. Changes take effect immediately.

### Setting your buffer size

Create `~/.config/pipewire/pipewire.conf.d/10-timing.conf`:

```
context.properties = {
    default.clock.rate = 48000
    default.clock.quantum = 256
    default.clock.min-quantum = 64
    default.clock.max-quantum = 1024
}
```

`default.clock.quantum` is your buffer size in samples. `default.clock.rate` is your sample rate — match this to what your interface is set to in hardware. A mismatch here causes subtle problems that produce no error message.

`min-quantum` and `max-quantum` set the range that applications can request. A DAW like Ardour can request a smaller quantum than the default; PipeWire will honor it down to your minimum.

### The pro-audio profile

By default, PipeWire tries to be friendly — it maps your interface's channels to logical positions (front-left, front-right, etc.) and lets the desktop manage routing. This is fine for listening to music. It's not what you want for recording.

The **pro-audio profile** bypasses that abstraction: PipeWire connects directly to every physical channel on your interface, raw and numbered. Your DAW sees all 18 (or 32, or however many) inputs and outputs as individual channels, exactly as they come from the hardware.

To set this for a specific device, you need its name. Find it:

```bash
pw-cli list-objects | grep alsa_card
```

Then set the profile. Replace `<card-name>` with what you found:

```bash
wpctl set-profile <card-id> pro-audio
```

To make this persistent, create `~/.config/wireplumber/wireplumber.conf.d/10-pro-audio.conf`:

```
monitor.alsa.rules = [
  {
    matches = [{ node.name = "~alsa_card.*" }]
    actions = {
      update-props = {
        api.alsa.use-acp = false
        api.acp.auto-profile = false
        api.acp.auto-port = false
        device.profile = "pro-audio"
      }
    }
  }
]
```

This tells WirePlumber: for all ALSA cards, ignore the channel mapping system and use raw pro-audio passthrough.

---

## Part 4: The things that feel broken but aren't

This section is not an appendix. It's the part most people need first.

### "I see the meters moving but I hear nothing"

You're on the pro-audio profile, and your desktop apps are sending audio to AUX0/AUX1 — the raw channel names — instead of to a logical stereo output. The fix is to create a virtual device that maps raw channels to named positions. This is covered in Part 6.

If you haven't set up pro-audio yet, the other common cause is that PipeWire is sending audio to a different device than you think. Run `pw-top` and watch which nodes are active when you play something.

### "There's a click or pop every 20-30 seconds"

Almost always a quantum mismatch. Two applications are running at different buffer sizes, and PipeWire is doing a periodic resync between them. 

Check `pw-top` — look for nodes showing different quantum values. The fix is usually to lock the quantum in your JACK configuration (see Part 5) or to close the application causing the mismatch.

If the clicks are irregular and accompanied by xruns in `pw-top`, you have a genuine timing problem: your buffer is too small for your system, or you need an RT kernel.

### "Audio works, but my interface disappears when I open a second app"

ALSA in its raw form can only talk to one application at a time. This is why PipeWire exists — it's the one app that holds the ALSA connection, and everything else talks to PipeWire. If an application is bypassing PipeWire and talking to ALSA directly (some older software does this), it will lock out everything else.

Check if the app has a setting for audio backend. Force it to use PipeWire, PulseAudio compatibility, or JACK (all of which go through PipeWire on a modern system). Never let an app use the `hw:` ALSA device directly if you can avoid it.

### "My latency is fine in the DAW but there's a delay when monitoring through software"

This is correct behavior, not a bug. When you record a track with software monitoring, the signal goes: interface input → ALSA → PipeWire → DAW → PipeWire → ALSA → interface output. That round trip adds your buffer latency twice, plus the DAW's own processing delay.

Solutions, in order of preference:

1. **Use hardware monitoring.** Most interfaces with direct monitoring mix the input signal back to the output in hardware, before any of the software stack is involved. Zero latency. Use this for tracking whenever possible.
2. **Reduce your buffer size.** At 128 samples / 48kHz, round-trip software latency is around 8-12ms depending on your interface — below the threshold of perception for most musicians.
3. **Use Ardour's latency compensation.** Ardour measures and compensates for round-trip latency automatically when you run its latency calibration wizard.

### "I can't get below 512 samples without xruns"

Before blaming your hardware, check these in order:

```bash
# Is your user in the realtime group?
groups | grep realtime

# Is PipeWire actually using realtime scheduling?
journalctl --user -u pipewire | grep -i realtime

# What's using your CPU while audio is running?
htop
```

USB interfaces introduce additional jitter because USB is a polled bus — data doesn't transfer continuously but in packets, and the timing of those packets varies. This is inherent to USB and means USB interfaces generally can't match the low-latency floor of PCIe or Thunderbolt interfaces. 128-256 samples is typically the practical floor for USB, even on ideal hardware.

### "My sample rate setting doesn't seem to do anything"

PipeWire's configured sample rate and your interface's hardware sample rate must match. If your interface has a hardware sample rate selector (physical switch, or software utility), set it there first, then match it in your PipeWire config. If they disagree, PipeWire will do a silent sample rate conversion — which costs CPU and subtly degrades quality.

Verify the actual rate your interface is running:

```bash
cat /proc/asound/card*/stream*
```

---

## Part 5: JACK for DAW work

### Why JACK (via PipeWire)

Applications like Ardour, Reaper, and most professional DAWs communicate using the JACK protocol. It gives them precise timing control, deterministic latency, and the ability to create direct connections between applications — routing audio from a software synth into a DAW track, for example.

On a modern system, you don't install JACK. You use PipeWire's JACK compatibility layer, which is already present. Verify:

```bash
pw-jack ardour
```

If Ardour opens and shows JACK in its audio setup, you're done. If not, install `pipewire-jack` (package name varies by distro — on Arch-based systems it's part of `pipewire`).

### Configuring JACK timing

Create `~/.config/pipewire/jack.conf.d/10-timing.conf`:

```
jack.properties = {
    node.latency = 128/48000
    node.lock-quantum = true
    node.force-quantum = 128
}
```

`node.latency` sets your buffer as a fraction of sample rate — numerator/denominator. `node.lock-quantum` prevents other applications from changing the buffer size while JACK is running. `node.force-quantum` enforces your chosen size system-wide.

**This is the most important setting for stable DAW work.** When `node.lock-quantum` is true, you've committed: every application in the graph runs at the same buffer size, no negotiation, no resync clicks.

### Viewing and managing connections

```bash
pw-jack jack_lsp          # list all JACK ports
pw-jack jack_lsp -c       # list with connections
```

For a graphical view, `qpwgraph` is the modern tool — it shows your entire PipeWire graph, JACK and PulseAudio nodes together, and lets you drag connections between them. Install it; it's essential.

---

## Part 6: Multi-interface setups

### The problem

You have more than one audio interface. Maybe a primary interface for tracking and a second one for monitoring, or a dedicated headphone amp, or a hardware unit that shows up as its own USB device. By default, PipeWire sees them separately and won't let a DAW address both simultaneously as a single aggregate device.

### Aggregating interfaces in PipeWire

Create `~/.config/pipewire/pipewire.conf.d/20-aggregate.conf`:

```
context.modules = [
  { name = libpipewire-module-combine-stream
    args = {
      node.name = "combined-interface"
      node.description = "All Interfaces"
      combine.mode = sink
      audio.position = [ AUX0 AUX1 AUX2 AUX3 ... ]  # list all channels
      stream.props = {}
      stream.rules = [
        { matches = [{ node.name = "alsa_output.usb-Interface_A*" }]
          actions = { create-stream = { audio.position = [ AUX0 AUX1 ] } } }
        { matches = [{ node.name = "alsa_output.usb-Interface_B*" }]
          actions = { create-stream = { audio.position = [ AUX2 AUX3 ] } } }
      ]
    }
  }
]
```

Find your exact device names with `pw-cli list-objects | grep alsa_output`.

**Important caveat:** Two USB interfaces do not share a clock. Over time — minutes to hours — they will drift relative to each other. For critical recording, this matters. Mitigations:

- Use a single interface with enough channels
- Use interfaces that support word clock and lock them together
- Use an interface that presents all its channels as a single USB device (many pro interfaces with ADAT expansion do this)

For monitoring and headphone mixing, drift is usually imperceptible. For tracking, be aware.

---

## Part 7: MIDI and RTP-MIDI

### Standard USB MIDI

USB MIDI devices are detected automatically by PipeWire. Verify:

```bash
aconnect -l         # list MIDI connections (ALSA view)
pw-cli list-objects | grep midi   # PipeWire view
```

In Ardour or any JACK-aware DAW, MIDI ports appear alongside audio ports in the connection manager.

### RTP-MIDI (network MIDI)

RTP-MIDI lets you send MIDI over Ethernet — essential for multi-room setups or hardware like the iConnectivity mioXL. Linux support is provided by `ravenna-alsa-lkm` (for Ravenna/AES67) or, for standard RTP-MIDI, `rtpmidid`.

Install `rtpmidid`:
```bash
# Arch/CachyOS
yay -S rtpmidid
```

Enable and start it:
```bash
systemctl --user enable --now rtpmidid
```

`rtpmidid` creates ALSA sequencer ports that appear in your MIDI connection manager. It advertises itself via mDNS, so it will appear on compatible hardware automatically. For the mioXL specifically: it should discover `rtpmidid` and offer a connection from its network configuration interface. Once connected, the mioXL's ports appear in `aconnect -l` and are available to any application.

To make connections persistent across reboots, use `aconnectrc` or configure them in your DAW's MIDI connection manager.

---

## Part 8: Channel mapping for desktop apps

*This section only matters if you're running the pro-audio profile and want your browser, media player, etc. to output to specific channels correctly. If you only care about DAW work, skip this.*

With pro-audio profile active, PipeWire exposes raw channels (AUX0, AUX1, ...) instead of named positions (FL, FR). Desktop apps expect named positions. The fix is to create virtual devices that map between them.

This is genuinely one of the rougher edges of the Linux audio stack. It works well once configured, but the configuration is verbose. Here's a minimal example — stereo output to the first two channels of your interface:

Create `~/.config/pipewire/pipewire.conf.d/30-desktop-outputs.conf`:

```
context.modules = [
  { name = libpipewire-module-loopback
    args = {
      node.description = "Main Stereo Out"
      capture.props = {
        node.name = "virtual.stereo_out"
        media.class = "Audio/Sink"
        audio.position = [ FL FR ]
      }
      playback.props = {
        node.name = "playback.stereo_out"
        audio.position = [ AUX0 AUX1 ]
        target.object = "alsa_output.usb-YourInterface-00.playback.0.0"
        stream.dont-remix = true
        node.passive = true
      }
    }
  }
]
```

Replace the `target.object` value with your interface's actual name from `pw-cli list-objects | grep alsa_output`.

"Main Stereo Out" will now appear in your desktop sound settings and route correctly to your interface's first two channels.

For additional outputs (headphone send, monitor B, etc.), add more loopback blocks to the same file, each with unique `node.name` values and different `AUX` channel assignments.

---

## Reference: commands you'll actually use

```bash
# See everything PipeWire knows about
pw-cli list-objects

# Monitor real-time performance and xruns
pw-top

# Graphical connection manager (install separately)
qpwgraph

# List JACK ports
pw-jack jack_lsp

# List MIDI connections
aconnect -l

# Check your interface's actual sample rate
cat /proc/asound/card*/stream*

# Check if realtime scheduling is active
journalctl --user -u pipewire | grep -i realtime

# Restart everything after config changes
systemctl --user restart pipewire pipewire-pulse wireplumber

# See WirePlumber's current device list and profiles
wpctl status

# Inspect a specific device (use the ID from wpctl status)
wpctl inspect <id>
```

---

## Appendix: when ALSA is actually the problem

If your interface doesn't appear in `pw-cli list-objects` at all, the issue is below PipeWire.

```bash
# Does the OS see the hardware?
lsusb | grep -i audio     # for USB interfaces
lspci | grep -i audio     # for PCIe cards

# Does ALSA have a driver loaded for it?
aplay -l

# Which driver modules loaded?
cat /proc/asound/modules
```

If `lsusb` sees it but `aplay -l` doesn't, ALSA's driver module didn't load — try `sudo modprobe snd-usb-audio` and check `dmesg | tail -20` for errors.

If `aplay -l` sees it but PipeWire doesn't, restart WirePlumber: `systemctl --user restart wireplumber`.

If none of this helps, your interface may not be USB Class Compliant — it may require a proprietary driver that doesn't exist on Linux. Check the Linux audio hardware compatibility database before purchasing interfaces.

---

*This guide was written for PipeWire 1.x / WirePlumber 0.5.x on a modern distribution (Arch-based, Fedora 40+, Ubuntu 24.04+). Some configuration syntax differs in older versions.*
