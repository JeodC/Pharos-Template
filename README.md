# Pharos Ports Template Repository

This repository is a template intended to be forked and used as a base for your own ports or wine bottles repository. The structure is:

- `.github/workflows`
  - In here you will find a sample screenshot collector. It will collect all screenshot image files it finds and bundle them into one `images.zip` release, which Pharos will pull and unzip for local screenshot rendering.
  - You will also find a sample YAML to generate port or bottle releases. This will run every 12 hours or when there's a change in `ports/*` or `bottles/*`. You can modify this easily to change where your released ports/bottles live. You may also wish to store `ports.json` or `winecask.json` in a `docs/` folder. If you do, you will need to modify the YAML.
    - You will need to generate a GitHub Personal Access Token and apply it to your repository as `TOKEN` so the workflow can remove old zip files from existing releases.
    - Pharos is designed with a one repository, one purpose rule. You should not mix ports with bottles or have a ports.json and winecask.json present in the same repository.
- `ports/` or `bottles/`
  - The centralized location for your ports/bottles. You can add subfolders for better organization, for example `released` and `WIP`.

## Optional GitHub Pages

You can set up a GitHub pages website to display your releases in a user-friendly browser page. An example website backend exists at https://github.com/JeodC/RHH-Ports/tree/main/docs.

## Metadata

Your port will go through Pharos's autoinstaller, which means you can (and should) include metadata for maximum accessibility! Each port or bottle should be packaged like this:

```
Port/
  ├── port/
  │   ├── port.json         # REQUIRED
  │   ├── gameinfo.xml      # Optional metadata for EmulationStation
  │   ├── README.md         # Instructions
  │   ├── screenshot.png    # REQUIRED
  │   ├── cover.png         # Optional cover artwork
  │   └── other port files
  └── Port.sh
```

```
Bottle/
  ├── bottle/
  │   ├── bottle.json       # REQUIRED
  │   ├── gameinfo.xml      # Optional metadata for EmulationStation
  │   ├── README.md         # Instructions
  │   ├── screenshot.png    # REQUIRED
  │   ├── cover.png         # Optional cover artwork
  │   └── other bottle files
  └── Bottle.sh
```

Each port/bottle must have a `port.json`/`bottle.json` file and a screenshot in order to be validated as a port/bottle. Examples for each are included in this template repository.


## Use with Pharos

[Pharos App](https://github.com/JeodC/RHH-Ports/tree/main/ports/released/apps/pharos) is designed to link with github repositories matching the above structure. Once you've established your repository, add its URL to Pharos' `.sources` file and load the app to verify it works.
