# Cosmos Network Deployment

Ansible playbooks for standing up Cosmos SDK networks, adapted to run Cascadia (`cascadiad`) nodes.

You can use it to spin up a local single node testnet, build a multi-node testnet from scratch, join an existing network or add the supporting services a testnet usually needs, like an explorer, a faucet and an IBC relayer. It's based on Hypha Co-op's [cosmos-ansible](https://github.com/hyphacoop/cosmos-ansible) toolkit, with the defaults switched over to the Cascadia chain repos and the `uCC` denom.

## What's included

| Playbook | What it sets up |
| --- | --- |
| `node.yml` | A chain node: builds or downloads the binary, configures it, runs it under systemd with Cosmovisor and can create a validator |
| `faucet.yml` | A token faucet for a testnet |
| `hermes.yml` | Two chains plus a Hermes relayer between them for IBC testing |
| `bigdipper.yml` | Big Dipper 2.0 block explorer |
| `blockscout.yml` | Blockscout EVM explorer |
| `consensus-monitor.yml` | A dashboard for watching validator consensus |
| `gaia-mainnet-export.yml` | Exports and edits a mainnet genesis file for testing upgrades |

The roles in `roles/` also cover state sync, swap files, Nginx with SSL, Prometheus, node exporter, PANIC alerts and creating DigitalOcean droplets.

## Requirements

- Python 3
- Ansible, installed with `pip install ansible` rather than apt so you get a recent version
- SSH access to the target machines (Ubuntu)

Install the Ansible roles and collections the playbooks use:

```bash
ansible-galaxy install -r requirements.yml
```

## Quick start

Start a local single node testnet on a server you can SSH into:

```bash
git clone https://github.com/AI-pro017/cosmos-network-deployment.git
cd cosmos-network-deployment
ansible-playbook node.yml -i examples/inventory-local.yml -e 'target=SERVER_IP_OR_DOMAIN'
```

Then log into the machine and watch the node start:

```bash
journalctl -fu cascadiad
```

The `examples/` folder has inventories for most setups: a three node testnet from existing keys or from scratch, a developer testnet, an IBC testnet, joining public testnets and the explorer and monitoring services. [examples/README.md](examples/README.md) walks through each one.

## Managing nodes

`node_control.py` runs common operations across every node in an inventory:

```bash
./node_control.py -i inventory.yml restart
```

The operations are `start`, `stop`, `restart`, `reboot` and `reset` (which wipes chain data with `unsafe-reset-all`). `-i` defaults to `inventory.yml`, and `-t` sets the server IP or domain for inventories that use a `target` variable.

## Configuration

Default settings live in `roles/<role>/defaults/main.yml`. Override any of them in your inventory or on the command line with `-e`. [docs/Playbook-Variables.md](docs/Playbook-Variables.md) lists the main ones, and the `docs/` folder has longer guides for multi-node testnets, the Hermes relayer and monitoring.

## Linting

Python is checked with pylint and YAML with yamllint. Run `./lint.sh` to check both. Outside CI it also formats the Python files with autopep8 first. The configs are in `.config/`.

## License

Apache 2.0. See [LICENSE](LICENSE).
