# Auto-select scene references

This local integration adds Shot Workspace-style automatic image selection to the
standalone H3 Prompt Writer. It is available in **Reference mode** for the normal
Single prompt workspace; it does not change the existing MiniMax H3 prompt-writing
contract or the input-mode implementations.

## Use it

1. Open Reference mode and upload your image references (up to the existing limit of
   nine images). Video and audio references can remain in the same workspace.
2. Enter the scene in the Creative Brief.
3. In the media panel, choose **Auto-select scene references**.
4. The selected model analyses any images that do not already have a cached
   description, then makes a separate text-only selection using the scene brief and
   the saved descriptions.
5. Review the selected images and their reasons. You can include or exclude images
   manually, or choose **Use all images** to clear the scene-specific filter.
6. Generate or refine the H3 prompt as usual. When the Creative Brief still matches
   the brief used for selection, only the active image subset is attached to that
   generation request. The original uploads remain in the media library.

## Model and cache behavior

- The first analysis pass sends all images in the current Reference library to the
  selected vision model so it can describe them. This is necessary to compare every
  available image with the scene.
- Selection itself is a separate text-only call: it uses the saved descriptions and
  scene brief, not image pixels.
- The final H3 prompt-generation call attaches only the selected images (plus any
  video/audio references, which this feature deliberately leaves unchanged).
- Descriptions are cached on the media assets for the lifetime of the current media
  session. The prepared-image SHA-256 hash is checked, so replacing or editing an
  image causes it to be analysed again.
- The same selected model/provider performs both stages. There is no separate
  vision-model selector in this integration.
- The description cache is session-scoped; it is not a permanent cross-launch asset
  database.

## Reference-label behavior

The selection filters prompt inputs without deleting or renumbering uploaded media.
Selected images retain their original `<Picture N>` labels, even when the subset is
non-consecutive. Video and audio references are preserved because this feature only
selects still images. If the Creative Brief explicitly names a `<Picture N>` tag,
that image is retained even if the model's selector would otherwise omit it.

If the Creative Brief changes after selection, the old selection is treated as
stale and is not applied to Generate or Refine. Run Auto-select again for the new
scene. Changing, adding, removing, replacing, or reordering media clears the current
selection; cached descriptions for unchanged assets remain reusable by the next
selection run.

## Scope

This integration currently targets the Single Reference workflow. It does not yet
apply separate selections to individual Sequence shots, and it does not automatically
select video or audio references.
