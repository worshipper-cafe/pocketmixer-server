# PocketMixer Server — Installation & Setup

PocketMixer Server runs on macOS and captures multi-channel audio from a USB audio interface (such as the Behringer XR18). It mixes and streams a personalised stereo feed to each musician running the PocketMixer client app on their phone or tablet.

## Requirements

- macOS 13 (Ventura) or later
- A USB multi-channel audio interface (e.g. Behringer XR18, Midas MR18)
- A network connection for the server and all performers:
    - **Ethernet (recommended)** — wired connections to the same switch or router give the lowest latency and most reliable audio with no packet loss
    - **WiFi 6 (802.11ax) or later** — if running wirelessly, WiFi 6 or above is strongly recommended; older standards have higher jitter and are more prone to dropouts under load

---

## Installation

1. Download `PocketMixerServer.dmg` from the [Releases](../../releases) page.
2. Open the DMG and drag **PocketMixer Server** into your Applications folder.
3. Eject the DMG.

---

## First Launch

Because PocketMixer Server is distributed outside the Mac App Store, macOS will block it on the first open.

1. Double-click **PocketMixer Server** in Applications.
2. macOS will show _"PocketMixer Server cannot be opened because it is from an unidentified developer."_
3. Open **System Settings → Privacy & Security**, scroll down, and click **Open Anyway** next to the PocketMixer Server entry.
4. Click **Open** in the confirmation dialog.

You only need to do this once.

---

## Allow Local Network Access

PocketMixer Server announces itself on the local network so client devices can find it automatically. macOS may ask for permission the first time.

1. When prompted with _"PocketMixer Server would like to find and connect to devices on your local network"_, click **Allow**.
2. If you missed the prompt, go to **System Settings → Privacy & Security → Local Network** and enable the toggle next to **PocketMixer Server**.

Without this permission, client apps will not be able to discover the server automatically. You can still connect manually by entering the server's IP address in the client app.

---

## Allow Microphone Access

PocketMixer Server can use your Mac's microphone to send talkback audio from the operator to the musicians.

1. On first launch a dialog will ask for microphone access — click **Allow**.
2. If you missed it, go to **System Settings → Privacy & Security → Microphone** and enable **PocketMixer Server**.

---

## Setup

### 1. Connect your audio interface

Plug your USB audio interface into the Mac before launching PocketMixer Server. The interface must be recognised by macOS — open **Audio MIDI Setup** to confirm it appears as a multi-channel input device.

### 2. Configure the server

Open the **Settings** tab:

| Setting | Recommendation |
|---|---|
| Input Device | Select your USB audio interface |
| Output Device | Select the interface or built-in output for your tech monitor |
| Sample Rate | 48000 Hz |
| Buffer Size | 8 ms (384 samples) — increase if you hear dropouts |
| Password | Optional — leave blank for open access |

### 3. Configure the input matrix

Open the **Matrix** tab. This is only available after streaming is started — if the grid is empty, return here after step 4.

The matrix is a grid with your interface's physical input channels as rows and mix channels as columns. Each cell is a toggle. When a cell is lit, that input is routed into that mix channel.

**Mix channels** are what performers hear and control in the client app — they are not the same as raw interface inputs. For example, if your XR18 sends a kick drum on input 1 and a snare on input 2, you might route both to mix channel 1 ("Drums") so each performer has a single drums fader.

**Default routing** is one-to-one: input 1 → mix channel 1, input 2 → mix channel 2, and so on. This works for simple setups where each input is already a distinct mix element.

**To customise:**

1. Click a cell to toggle that input–mix channel connection on or off. A cell lights up blue when active.
2. A mix channel can receive multiple inputs — toggle as many input rows as needed in that column. All routed inputs are summed into the mix channel.
3. Click **Apply Routing** to push the changes to the audio engine. Changes are not live until you apply them.
4. Click **Reset to Default** to restore the one-to-one passthrough at any time.

The routing is saved automatically to `~/.pocketmixer/matrix.json` and reloaded when streaming restarts.

### 4. Start streaming

Click **Start Streaming**. The indicator turns green when the server is active and announcing itself on the network.

### 5. Connect clients

On each performer's device, open the PocketMixer app. The server should appear automatically in the list. Tap it to connect, enter the password if one is set, and start mixing.

---

## Known Issues

- **Audio dropouts on WiFi:** If performers experience audio gaps, increase the jitter buffer in the client app (Settings → Jitter Buffer). Start at 20 ms and raise until dropouts stop.
- **AirDrop causes audio gaps:** AirDrop's AWDL interface can coalesce UDP packets and cause ~140 ms gaps. The server Info tab shows a warning if AWDL is active. Disable it by running `sudo ifconfig awdl0 down` in Terminal while the server is in use.
- **USB interface dropouts:** Use a buffer size of 8 ms or larger. Very small buffer sizes (under 4 ms) are unreliable due to macOS scheduler jitter.

---

## Uninstalling

Drag **PocketMixer Server** from Applications to the Trash. Server settings are stored in `~/.pocketmixer/` and can be deleted separately.
