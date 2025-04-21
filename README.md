# Helm Release Resource Lister

A small CLI utility that inspects Helm release Secrets _inside the cluster_ and prints the list of Kubernetes resources rendered by a chosen release revision, highlighting whether each resource currently exists in the cluster.

```
✓ exists  – green
✗ absent  – red
```

---

## Features

* **Revision aware** – query the latest revision or any specific `--revision` you need.
* **Cluster‑scoped & namespaced** resources are both detected.
* **Robust decoding** – handles the single‑ or double‑base64 + gzip formats used by different Helm versions/plugins.
* **No cluster‑wide list calls** – works only with the release Secret and targeted GETs, so it’s friendly to restricted RBAC environments.
* **Colour output** – clearly see which objects are missing.

---

## Installation

```bash
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

The script relies on your default kube‑config _or_ in‑cluster service account credentials, exactly like `kubectl`.

---

## Usage

```bash
python helm_selector.py \
  --name       <release>     \
  --namespace  <namespace>   \
  [--revision <N>]
```

Examples:

* Inspect **latest** revision of release `backend` in namespace `prod`:

  ```bash
  python helm_selector.py --release-name backend --release-namespace prod
  ```

* Inspect **revision 12** of release `flux-ui` in namespace `flux-system`:

  ```bash
  python helm_selector.py --release-name flux-ui --release-namespace flux-system --revision 12
  ```

_Output sample:_

```
Resources in Helm release 'flux-ui' revision 12 (namespace flux-system):
-----------------------------------------------------------------------
Deployment                flux-system/flux-ui            -> ✓ exists
ClusterRole               flux-ui                         -> ✓ exists
ServiceAccount            flux-system/flux-ui            -> ✗ absent
...
```

---

## Exit codes

| Code | Meaning                                  |
|------|-------------------------------------------|
| 0    | Success                                   |
| 1    | Secret or revision not found / other error|

---

## Notes

* The script uses **ANSI escape codes**; if you pipe the output, colours might disappear (depending on your terminal). For CI environments without TTY support you can temporarily remove the colour codes.
* RBAC: the service account or kube‑config you use must have permission to **get** `secrets` in the release namespace _and_ **get** each listed resource to check its existence.

---

## License

MIT © 2025 Your Name

