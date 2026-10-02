# container-app-service

Bicep module for one HTTP workload on Azure Container Apps. It is the first `br/platform` module. It is not published, and it is not pinned.

ADR-0006 does not name a file path. This file is `modules/container-app-service/main.bicep` so the folder matches the module name `container-app-service`. A later publish could target `br/platform:container-app-service:<version>` from this file. Nothing has been published.

Capability-to-module mapping is still open (ADR-0009). This README does not decide it.

## What it creates

| Resource | API version | Name |
| --- | --- | --- |
| `Microsoft.OperationalInsights/workspaces` | 2023-09-01 | `<name>-logs` |
| `Microsoft.App/managedEnvironments` | 2024-03-01 | `<name>-env` |
| `Microsoft.App/containerApps` | 2024-03-01 | `<name>` |

The container app has a system-assigned managed identity. That is the smallest identity included here: ADR-0006's scaffold requires identity inside the module and does not name a kind. ADR-0007's user-assigned identity with `AcrPull` is created at bootstrap, with its role assignment, and is not in this module.

The workspace is the diagnostics sink the scaffold requires. The environment sends app logs to it (`appLogsConfiguration.destination`: `log-analytics`). No workspace SKU is set. ADR-0007 places Log Analytics on the planned platform pipeline; it is in this module because the scaffold requires diagnostics and the parameter list has no workspace id.

The app uses the built-in Consumption workload profile, single revision mode, and `minReplicas: 0`. No scale rule is set, so the platform's default HTTP scale rule applies. `maxReplicas` is not set. The ingress target port is 8080, the template port named in ADR-0007. Container size is 0.25 vCPU and 0.5 GiB, the bottom of the Consumption range. ADR-0007 does not set a size.

Every resource is tagged `aeroflow-module` = `container-app-service:unpublished`. The version is the literal `unpublished` because the module is not published. ADR-0006 does not define a naming pattern, so names are derived from `name` only. Organisation, prefix and registry are not hard-coded (ADR-0007).

## Parameters

| Name | Decorators | Description |
| --- | --- | --- |
| `name` | `@minLength(2)` `@maxLength(28)` | Name of the container app. Lowercase letters, numbers and hyphens, 2 to 28 characters, starting with a letter. The environment is named `<name>-env` and the log workspace `<name>-logs`. |
| `location` | | Azure region for the container app, its environment and its log workspace. |
| `containerImage` | | Container image reference for the HTTP workload. |
| `ingress` | `@allowed(['external', 'internal'])` | HTTP ingress. external is reachable on the internet; internal stays inside the environment. |

`name` is capped at 28 characters so `<name>-env` stays within 32 characters. The [Azure naming rules](https://learn.microsoft.com/en-us/azure/azure-resource-manager/management/resource-name-rules) list `Microsoft.App/containerApps` as 2–32 characters. They do not list `managedEnvironments`.

There are no outputs.

## What this module does not do

- No virtual network, Key Vault, storage or queues.
- No Azure Verified Modules, and no `br/public` or `br/platform` imports. `br/platform:types` is not published, so this module does not import it.
- No sealed `advanced` parameter. ADR-0006 describes one for thin modules. It is not in the scaffold list this module was built to, and the parameter list is the four above.
- No Dapr, no custom scale rules, no Dedicated or Flexible workload profiles, no multiple-revision mode (ADR-0007).
- No registry and no `AcrPull` role assignment.
- No publish, no release tag, and no pin.
