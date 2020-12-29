This is a set of example component source files for AWS IoT Greengrass 2.0. It is used both as repository for doing development within a VM and as a placeholder for interesting components.

# VS Code Remote Development

When doing development, in order to not lose work within a VM, especially when rolling back VM changes to test component updates to the operating system, follow these steps:

1. Fork this repository into your home directory (e.g., `/home/ggc_user`)
1. Rename the repository to `GreengrassDevelopment` (`mv greengrassv2-components GreengrassDevelopment`)
1. Ensure you have proper Git credentials working and can commit from the remote SSH session

The directory layout for local component development follows the Greengrass documentation for file format, but instead of using `~/GreengrassCore` uses `~/GreengrassDevelopment`. Under `~/GreengrassDevelopment` is the `recipe/` directory that holds all recipe files, and along side that the `artifacts/` directory that holds all artifact files that will be uploaded to your S3 bucket once the component is published.

The file structure should look like this:

```
GreengrassDevelopment/
├── LICENSE
├── README.md
├── artifacts
│   └── test.hello_world
│       └── 1.0.0
│           └── hello_world.py
└── recipes
    └── test.hello_world-1.0.0.yaml
```

All recipes are named by component and version and stored in the `recipes/` directory while the artifacts must be broken out by `artifacts/COMPONENT_NAME/COMPONENT_VERSION/`.

## Local Aliases

In order to quickly issue local `greengrass-cli` commands, the following aliases are created to deploy and remove a component based on name and version:

```
# Per session or in .bashrc file create exports for the NAME and VERSION
export COMPONENT_NAME=full.name.without.version
export COMPONENT_VERSION=1.0.0

# These aliases can be included in the .bashrc file
alias gg_deploy='sudo /greengrass/v2/bin/greengrass-cli deployment create \
  --recipeDir ~/GreengrassDevelopment/recipes \
  --artifactDir ~/GreengrassCore/artifacts \
  --merge "$COMPONENT_NAME=$COMPONENT_VERSION"'
alias gg_remove='sudo /greengrass/v2/bin/greengrass-cli deployment create \
  --remove "$COMPONENT_NAME"'
alias gg_get_debug='sudo /greengrass/v2/bin/greengrass-cli get-debug-password'
```

## Development Cycle

A normal development cycle looks like this:

1. Set the component name and version as exports in a shell
1. Use `gg_debug` to get credentials if using the HTTP debug console
1. Develop the recipe file and optionally any artifacts, then:
   1. `gg_deploy` to deploy, review functionality and log files
   1. Issue the `gg_remove` command to remove the component
   1. Once completed, make changes and repeat as needed (deploy)
