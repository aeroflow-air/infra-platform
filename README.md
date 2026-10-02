# infra-platform

Source of the `br/platform` Bicep modules.

Nothing is published to a registry yet. The first module is `container-app-service`.

The module is at [`modules/container-app-service`](modules/container-app-service/README.md). It is not published and not pinned.

## Registry

The registry for now is GitHub Container Registry (`ghcr.io`), not Azure Container Registry. The pin form is `br:ghcr.io/aeroflow-air/container-app-service:<version>`. Publishing to `ghcr.io` needs the experimental Bicep feature `ociEnabled`, set in `bicepconfig.json`. Azure Container Registry remains the later bootstrap.
