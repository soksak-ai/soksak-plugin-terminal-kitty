# soksak-plugin-terminal-kitty

Soksak terminal plugin backed by the Kitty terminal-state sidecar.

The plugin implements `soksak-spec-plugin-terminal` 0.0.13, declares PTY and terminal-state
sidecar requirements, and exposes the common terminal commands, status and operable nodes. The
published plugin archive contains `plugin.json`, `main.js` and its license; provider binaries are
installed independently.

## Verification

The package depends on `@soksak/soksak-contract-plugin-terminal` and `@soksak/soksak-kit-plugin-terminal`,
so every `make` invocation that installs requires `REGISTRY` on the make command line,
`https://registry.npmjs.org` included once the packages are published there. A value from the
environment is refused. The Makefile reads the requirement from `frontend/package.json` and refuses
`REGISTRY required: this package depends on @soksak/...` when it is absent.

The build input is identified by the `pnpm-lock.yaml` integrity, not by `REGISTRY`. pnpm fetches from
`REGISTRY` only a package whose integrity its content-addressable store does not already hold, so a
second install of the same lockfile on the same machine reads the store and never contacts `REGISTRY`.

`make lock` is the only owner operation that regenerates `frontend/pnpm-lock.yaml` after an exact
dependency declaration changes. It updates the lock without materializing packages; normal builds
continue to install the frozen state through `make prepare`.

```sh
make lock REGISTRY=http://host:port/
make verify REGISTRY=http://host:port/
make attest OUT=/absolute/release-output STORE=/absolute/local-release-store REGISTRY=http://host:port/
```

The login profile selects one installed `soksak-sdk` on `PATH`. `SDK_VERSION` is the single
required tooling version and Make checks the installed package and release documents. `STORE`
resolves exact unpublished runtime dependencies; no SDK or component source path is accepted.
