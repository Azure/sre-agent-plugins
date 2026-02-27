# sre-agent-plugins
Azure SRE Agent Plugins

A plugin marketplace for [GitHub Copilot CLI](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace).

## Usage

Register this marketplace with Copilot CLI:

```sh
copilot plugin marketplace add Azure/sre-agent-plugins
```

Browse available plugins:

```sh
copilot plugin marketplace browse sre-agent-plugins
```

Install a plugin:

```sh
copilot plugin install PLUGIN-NAME@sre-agent-plugins
```

## Adding plugins

1. Add the plugin directory under `plugins/` in this repository.
2. Add an entry for the plugin in [`.github/plugin/marketplace.json`](.github/plugin/marketplace.json).
