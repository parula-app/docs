---
description: Play your music archive
---

# Music library

Pia uses the excellent music server [mpd](https://www.musicpd.org) to play your music and Internet radio stations. We hope you will enjoy mpd even outside of Pia.

1. Install mpd: `# apt install mpd mpc`
2. Configure the directory where your music files are.
   * If your music library is on the local disk: `mpc mount local /path/to/music/`
   * If your music library is on a USB stick: `mpc mount mystick udisks://by-uuid-2F2B-D136`
   * If your music library is on a Samba file server: `mpc mount myserver smb://myserver/music/`
   * If your music library is on an NFS file server: `mpc mount myserver nfs://myserver/music/`
   * Alternatively:
     1. Edit `/etc/mpd.conf`
     2. Change `music_directory` to the directory where your music files are.
     3. Restart the mpd server:`sudo systemctl restart mpd.service`
3. \(Skip this unless you hear no music.\) Configure the sound output via PulseAudio and the right sound device.
   1. It may already be _correct by default_, depending on your Linux distribution and setup. If not:
   2. Find the right audio device: `pacmd list-sinks | grep "name:"`
   3. Edit `/etc/mpd.conf`
   4. ```text
      audio_output {
             type            "pulse"
             name            "Pia - Device speaker"
             server          "localhost"
             sink            "alsa_output.usb-...analog-stereo"
      }
      ```
   5. Restart the mpd server:`sudo systemctl restart mpd.service`
4. Activate the new config
   1. Enable at boot:`sudo systemctl enable mpd.service`
   2. Scan your music library: `mpc update --wait`
5. Test it with some [native mpd clients](https://www.musicpd.org/clients/).
   1. `# apt install gmpc`
   2. Start menu \| Multimedia \| GNOME Music Player Client
   3. You can also control playback with your smartphone by using the mpdroid client \([APK](https://f-droid.org/repo/com.namelessdev.mpdroid_58.apk) via [F-Droid](https://f-droid.org/en/packages/com.namelessdev.mpdroid/), or [Google Play Store](https://play.google.com/store/apps/details?id=com.namelessdev.mpdroid&hl=en)\).
   4. Once that works, ...
6. Try some [voice commands for calendar](../user/music.md)
