# fleet-autopkg-recipes

Fleet's software feature is [experimental](https://fleetdm.com/docs/configuration/yaml-files#software) and therefore so are these recipes. Use at your own risk.

In Fleet 4.74.0, Fleet is making breaking changes to the software YAML. The following keys are moving from package YAML files to the software key in the team YAML files: self_service, labels_include_any, labels_exclude_any, categories.

As part of Fleet 4.74.0, Fleet is releasing a script to automatically migrate old YAML to new YAML.

What to do when upgrading to 4.74.0?
Before upgrading, run Fleet’s script to automatically migrate old YAML to new YAML
Upgrade to 4.74.0
Run GitOps
