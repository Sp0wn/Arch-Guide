# Arch-Guide

This is my personal installation guide based on 
the ArchWiki. It includes additional comments that
explain each step and its variations in more detail.
I've also added some recommendations for customizing the OS.

# Table of contents

# Pre-installation

## Keyboard layout

The keyboard layouts are stored in the keymaps directory which
is `/usr/share/kbd/keymaps/`. Then it is divided in 
architecture, then in layouts, and then languages. Available 
layouts can be listed with `localectl list-keymaps`. In order to 
change to layout use `loadkeys` and the name of the file.

```
localectl list-keymaps
loadkeys us
```

# Installation

# Configuration

# Post-installation
