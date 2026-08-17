# Portfolio — mobile test build

A reduced build of the 3D portfolio, made to find out whether it survives on a
phone. The full-size version crashed on iOS after loading: it allocated ~560 MB
of texture memory against a ~1 GB budget.

Rebuilt from the same Blender export — no geometry was removed, no rooms changed.

|                    | full build | this build |
| ------------------ | ---------: | ---------: |
| download           |   156.3 MB |    23.1 MB |
| model (.glb)       |      81 MB |     6.1 MB |
| texture memory     |     558 MB |     149 MB |
| meshes / triangles | 232 / 491,690 | 232 / 491,273 |

## What changed

- Every texture in the model capped at 512 px and re-encoded as WebP
  (58.7 MB of images became 2.48 MB). Textures are referenced through
  `EXT_texture_webp`, so the loader needs that extension.
- Geometry compressed with meshopt (`EXT_meshopt_compression`), which is why
  `index.html` sets a `MeshoptDecoder`. Float positions rather than the default
  quantization — at 14 bits the position error was 41% and visibly deformed the
  model.
- Crown stills 1600 -> 512 px, gallery photos 1600 -> 1024 px.
- The theater no longer plays from startup. It opens when you first walk into
  the theater's zone, on VOTE. Previously an embedded player decoded video for
  the whole session, from anywhere on the map.
- Atmospheric dust particles removed (2,000 points plus a per-frame update).

## Not addressed

The JS heap sits at ~1760 MB and is unchanged from the full build — it tracks
geometry and the collision octree, not textures. If this still crashes, that is
the next thing to look at.

## Running it

Any static server from this folder, e.g.

    python3 -m http.server 8001

The unoptimised 81 MB export is deliberately not in this repo (see
`.gitignore`); it lives alongside the working copy for A/B comparison.
