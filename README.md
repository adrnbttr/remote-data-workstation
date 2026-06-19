# remote-data-workstation

Infrastructure-as-code, powered by **Ansible**, to provision a secure and
reproducible **remote data-engineering workstation**.

The idea is simple: instead of pulling large datasets down to a laptop over the
internet, run the development environment on a VM that sits **next to the
database** (same region / private network). Heavy work stays close to the data,
the laptop becomes a thin client you SSH into — and iteration gets dramatically
faster.

> 🚧 Work in progress — see the roadmap below.

## License

MIT — see [LICENSE](./LICENSE).
