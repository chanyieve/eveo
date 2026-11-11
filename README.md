# Wash — a watercolor study

An interactive watercolor sketchbook in a single HTML file. Place pigment with a
pointer and watch water carry it: the wash spreads, the edges dry first, pigment
drifts outward and fixes to the paper. Everything is rendered procedurally — there
are no image assets and no build step.

**Try it:** open `index.html`, or visit the GitHub Pages URL once it is enabled.

## Controls

| Control | What it does |
| --- | --- |
| **Pigment** | Mix between blue and ochre. Colour is mixed subtractively, so layering blue over ochre deepens toward a grey-green rather than averaging to grey. |
| **Water** | How wet the brush is. More water means paler, faster-spreading colour and softer edges. |
| **Brush** | Stroke radius. |
| **Pause** / `Space` | Stops the water. You can keep painting; the wash resumes where it left off. |
| **Clear** / `C` | Empties the paper. |

Hold the pointer still and the brush keeps feeding the puddle, so pigment blooms
outward. A pen with pressure support paints proportionally narrower or wider.

## How it works

The paper is a coarse grid (about 2–3 CSS px per cell). Each cell stores five
numbers: water, suspended blue, suspended ochre, dried blue, dried ochre.

1. **Water** equalises with wet neighbours and creeps into dry paper only above a
   paper-dependent threshold, so the edge of a puddle holds like a bead. It
   evaporates faster where a wet cell touches dry paper.
2. **Suspended pigment** is exchanged across cell faces in proportion to the water
   level difference, so it flows from deep water toward the drying rim and none is
   created or lost. A slow divergence-free circulation stirs it a little.
3. **Fixing**: pigment drops out of suspension as the cell dries, faster for ochre,
   which is what produces the darker rim and the granular settling in the paper tooth.

Colour is composited with Beer–Lambert absorption
(`transmittance = base_colour ^ concentration`), which is why blue and ochre
overlap like glazes rather than like layers of opaque paint. The paper tooth is a
tile generated at start-up with value noise and multiplied over the wash.

The simulation runs on the CPU and the low-resolution canvas is scaled up, so edges
are soft rather than crisp.

## Performance

- At a 1280 × 717 viewport (~147k cells), one simulation step costs about 2.1 ms and
  one render about 0.7 ms — roughly 2.8 ms per frame, leaving plenty of headroom for 60 fps.
- At most two simulation steps run per frame and the loop never tries to catch up, so
  a slow device dries more slowly instead of stuttering.
- If frames keep overrunning the budget, the grid is coarsened automatically.
- Repainting is limited to the dirty rectangle; with the whole sheet wet that is the
  entire canvas, which is the worst case.
- Touch devices start from a smaller grid (~80k cells) than pointer devices.

## Publishing to GitHub Pages

```sh
git init
git add index.html README.md .nojekyll
git commit -m "Watercolor study"
git branch -M main
git remote add origin git@github.com:<your-user>/<your-repo>.git
git push -u origin main
```

Then in the repository: **Settings → Pages → Build and deployment → Source: Deploy
from a branch**, branch `main`, folder `/ (root)`. The site appears at
`https://<your-user>.github.io/<your-repo>/`.

No build, no dependencies, no server needed.

## License

MIT — see `LICENSE`.
