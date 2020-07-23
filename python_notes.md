---
title: Python Notes
layout: page
permalink: "/python_notes"
---

# Misc Python Notes

### Certificates on MacOS

Got a `CERTIFICATE_VERIFY_FAILED`? 
Python versions don't always seem to end up with their SSL certificates on MacOS. This can be rectified with:

```bash
/Applications/Python\ 3.8/Install\ Certificates.command
```

I've seen posts suggesting simply installing the `certifi` package should do the trick, but that didn't help on my workstation.
