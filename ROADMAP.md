# Roadmap from dwl to waybtr

Note: for each goal below, the relevant existing codebases can likely be
inherited (rather than written from scratch) to accomplish the task.

## Built-in status bar

Integrate the functionality of [slstatus] directly into dwl. Rendering a
status bar in-process removes the dependency on external bar clients and the
status-parsing plumbing that comes with them. The slstatus codebase can
likely be inherited for the component/feature implementations.

- Render bar text (clock, system info, etc.) in-process via drwl
- Replace the current "write status to stdout of `-s`" mechanism
- Configure entirely in `config.h`

## Built-in wallpaper

Integrate the functionality of [swaybg] directly into dwl. Loading a
wallpaper in-process avoids spawning and managing a separate client at
startup. The swaybg codebase can likely be inherited for the rendering and
image-loading logic.

- Set a solid color or image per output, configured in `config.h`
- Render through the wlroots scene graph
- Replace external `swaybg`/`swaybgd` usage

## Deprecate external startup and status plumbing

Once the above are in place, remove the mechanisms that exist only to
support external bars and startup scripts:

- The `-s` child process option and its status output over stdin
- `STATUS INFORMATION` protocol in `dwl(1)` and the `<&-` workarounds
  documented in the README

Deprecation will be gradual: announce in the README first, remove in a
follow-up release.

## Tiny core executable + shared library

Restructure the codebase into a small core executable plus a shared library
holding the compositor logic. This is the enabling step for hot reloading:
future compilations would only need to rebuild the library, which the
core executable could reload at runtime without restarting the session or
tearing down the Wayland state.

## Hot-reloadable YAML configuration

Deprecate `config.h` (and the edit-and-recompile workflow) in favor of a
YAML configuration file that is reloaded at runtime when it changes. This
depends on the shared-library restructuring above, and replaces
"recompile to apply settings" with "save file to apply settings".

[slstatus]: https://tools.suckless.org/slstatus/
[swaybg]: https://github.com/swaywm/swaybg
