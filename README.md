# JupyterLab SDK for Workshop

This SDK provides JupyterLab as a persistent browser-accessible service for
interactive Python development in Workshop. The Python virtual environment is
persisted on the host to preserve installed packages across workshop updates.
Optionally, connect the `venv` plug to the `uv` SDK for a uv-managed
environment.

---

## Reference workshop

A minimal workshop:

```yaml
# workshop.yaml
name: notebooks
base: ubuntu@24.04
sdks:
  - name: system
    plugs:
      jupyter:
        interface: tunnel
        endpoint: 127.0.0.1:8888
  - name: jupyter
    channel: latest/stable

actions:
  verify: |
    source /var/lib/workshop/sdk/jupyter/venv/bin/activate
    jupyter --version
```

This demonstrates a minimal JupyterLab environment with the browser tunnel
connected, so JupyterLab is accessible at `http://localhost:8888` on the host.

---

## Using the SDK

### Prerequisites, project layout

1. No prerequisite SDKs are required for the default setup. The `uv` SDK is
   optional; see [Using a uv-managed venv](#using-a-uv-managed-venv) below.
2. No specific project layout is needed. Notebooks are saved in `/project/`
   alongside your project files.
3. The first launch installs JupyterLab into the virtual environment via the
   `setup-project` hook, so it takes longer than subsequent launches.

### Access JupyterLab

After `workshop launch`, open `http://localhost:8888` in a browser.

- No login is required because the authentication token is disabled.
- JupyterLab serves from `/project/`, so notebooks are saved alongside project
  files.

### Verify from the command line

To confirm JupyterLab is installed and on the path:

```bash
workshop shell
source /var/lib/workshop/sdk/jupyter/venv/bin/activate
jupyter --version
```

To list running Jupyter servers:

```bash
workshop shell
source /var/lib/workshop/sdk/jupyter/venv/bin/activate
jupyter server list
```

### Using a uv-managed venv

By default, JupyterLab runs inside a Python virtual environment managed by the
SDK itself. If you prefer to manage packages with `uv`, you can connect the
`jupyter:venv` plug to the `uv` SDK's `venv` slot.

When connected, the uv SDK's virtual environment is mounted at the jupyter
SDK's venv path. JupyterLab is still installed into the venv on first launch.
This lets you manage packages with `uv pip` instead of plain `pip`.

```yaml
# workshop.yaml
name: notebooks
base: ubuntu@24.04
sdks:
  - name: system
    plugs:
      jupyter:
        interface: tunnel
        endpoint: 127.0.0.1:8888
  - name: uv
    channel: latest/stable
  - name: jupyter
    channel: latest/stable

connections:
  - plug: jupyter:venv
    slot: uv:venv
```

---

## Plugs (resources this SDK consumes)

### `venv`

- Interface: `mount`
- Workshop target: `$SDK/venv`
- Purpose: Persists the Python virtual environment between workshop updates.
  JupyterLab and any other packages installed into the venv survive across
  workshop updates.

## Slots (resources this SDK provides)

### `jupyter`

- Interface: `tunnel`
- Endpoint: `127.0.0.1:8888`
- Purpose: Exposes the JupyterLab server to the host for browser access.
  Connect a matching plug on the `system` SDK to make JupyterLab accessible at
  the plug address on the host.

---

## Documentation and guidance

- [JupyterLab official documentation](https://jupyterlab.readthedocs.io/)
- [Workshop documentation](https://ubuntu.com/workshop/docs/)

---

## Community and support

- JupyterLab community: [JupyterLab GitHub](https://github.com/jupyterlab/jupyterlab)
- Jupyter community forum: [Jupyter Discourse](https://discourse.jupyter.org/)
- Workshop forum:
  [Discourse](https://discourse.ubuntu.com/)
- Please review our
  [Code of Conduct](https://ubuntu.com/community/ethos/code-of-conduct) before
  participating.

---

## Contributions

All contributions, including code, documentation updates, and issue reports,
are welcome!

- See `CONTRIBUTING.md` for guidelines.
- Open issues or pull requests on the official repository.

---

## License and copyright

Copyright 2025 Canonical Ltd.

JupyterLab is licensed under the
[BSD 3-Clause License](https://opensource.org/licenses/BSD-3-Clause).
