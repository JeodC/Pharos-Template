# Pharos Ports Template Repository

A starting point for hosting your own ports or wine bottles, on the same setup [RHH-Ports](https://github.com/JeodC/RHH-Ports) runs on. Fork it, add your ports, and the workflows publish releases that [Pharos](https://github.com/JeodC/RHH-Ports/tree/main/ports/released/apps/pharos) and stock PortMaster can install from. Once the first release is out, add your repository's URL to Pharos' `.sources` file and open the app to check your ports show up.

Pharos follows a one repository, one purpose rule. Don't mix ports with bottles, or keep a `ports.json` and a `winecask.json` in the same repository.

## Layout

- `ports/released/` or `bottles/released/` holds what gets published. Subfolders are fine, e.g. by genre or engine. Work in progress goes outside `released/`.
- `templates/` has a starter `port.json` and `bottle.json`.
- `.github/workflows/`
  - **Release Ports** and **Generate Bottles** zip whatever changed, upload it to a release, and update `docs/ports.json` or `docs/winecask.json`. They run every 12 hours, on pushes to `released/`, or by hand. A forced run re-zips everything.
  - **Generate Screenshots ZIP** bundles every screenshot into `images.zip` for Pharos. Ports repositories also get an `images.zip` from the PortMaster catalog step, which Pharos prefers, so this one mostly matters for bottles.
  - All of them use the built-in `GITHUB_TOKEN`. No Personal Access Token is needed.

Release Ports only checks out port metadata, then pulls in the ports it zips. If you move your ports out of `ports/released/`, update its `sparse-checkout` paths too.

## Packaging

Each port or bottle is a launcher next to a folder holding its metadata:

```
Port/
  ├── port/
  │   ├── port.json         # required
  │   ├── screenshot.png    # required
  │   ├── README.md         # instructions
  │   ├── cover.png         # optional cover art
  │   ├── gameinfo.xml      # optional EmulationStation metadata
  │   └── other port files
  └── Port.sh
```

Bottles have the same shape, with `bottle.json`. Anything missing its json or a screenshot is skipped.

In `port.json`:

- `name` becomes the zip's file name. Stick to letters, numbers, `.`, `_` and `-`--GitHub strips anything else from release asset names, and the download 404s.
- `porter` lists everyone who made the port possible. When you build on someone else's port, keep their names.
- `store` takes `{"name": "Steam", "gameurl": "https://store.steampowered.com/app/<appid>/", "developerurl": ""}` entries. A Steam link also turns on [sale badges](#discounts).

## PortMaster

Release Ports also publishes a [PortMasterV3](https://portmaster.games/) catalog to `ports-latest`: a PortMaster-format `ports.json`, an `images.zip` of screenshots, and a `.source.json` file. Copying that file into `PortMaster/config/` on a device adds your ports to stock PortMaster.

- The source file's prefix and display name come from your repository name. Commit a `docs/<name>.source.json` to set them yourself.
- PortMaster reads source files in file name order, and when two sources offer a port with the same name, the first one wins. The official ones are `020_portmaster.source.json` and `021_portmaster.multiverse.source.json`. Name yours `010_<prefix>.source.json` and your versions win; anything sorting after them and PortMaster's do. Tell your users which.
- Ports can declare runtimes in `attr.runtime`, e.g. `["weston_pack.squashfs"]`. Official PortMaster runtimes are referenced automatically. A custom one has to be committed to `runtimes/`, so the catalog can hash it, and uploaded to a `runtimes-latest` release.
- Bottles are Pharos-only. The catalog step is skipped when `docs/ports.json` is empty.

## GitHub Pages

[RHH-Ports' `docs/`](https://github.com/JeodC/RHH-Ports/tree/main/docs) is a working site to copy. It reads `docs/ports.json` and shows download counts, recent updates and sales. Download counts come from `lifetime_downloads`, which Release Ports keeps per port, plus the live count on the release.

## Discounts

Ports with a Steam link in `store` show live sale percentages. The prices come from [IsThereAnyDeal](https://isthereanydeal.com/), through a [Cloudflare Worker](https://workers.cloudflare.com/) RHH-Ports runs at `https://rhh-ports-discounts.jeodc.workers.dev/`.

- **Pharos** checks every port in every repository it loads against that Worker, so your ports get sale badges and the "On Sale" view with no setup.
- **A Pages site** needs a separate Worker, since this one only answers browser requests from `jeodc.github.io`. Cloudflare's free tier and an IsThereAnyDeal API key cover it. It takes `?appids=<id>,<id>&country=US` and returns `{"<appid>": {"<shop>": {"cut": <percent>, "url": "<link>"}}}`. Point `DISCOUNT_API_URL` in `loadports.js` at it.
