# GitHub Pages directory

Served at `https://<owner>.github.io/robolynk-light/`.

`manifest.json` points at the firmware **release asset**, not a file in this repo:
a 16 MB binary committed to git stays in history forever. Publish the `.bin` as a
release, then bump the version and URL here.

ESP Web Tools requires HTTPS. GitHub Pages provides it; opening `index.html` from
disk will not work — the install button will report that serial access is blocked.
