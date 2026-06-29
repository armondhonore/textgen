# Nexlayer deploy fix guidance (pinned)

PINNED: do not regenerate nexlayer.yaml or any Dockerfile. The deployment uses the
prebuilt published image `atinoda/text-generation-webui:default-cpu` (no source build).

Root cause of prior failures:
1. An empty `textgen-models` PVC was mounted at `/home/user/app/models`, a path that
   does not exist in the atinoda image (its app dir is `/home/app/textgen`). The image
   runs as non-root UID 6972, so a root-owned empty PVC there causes a permission/crash
   loop. The PVC is removed — the Gradio UI serves without any model.
2. The Gradio server only binds 0.0.0.0:7860 when launched with `--listen`. We pass it
   via the `EXTRA_LAUNCH_ARGS` env var, which the atinoda entrypoint reads.

Keep nexlayer.yaml exactly as committed. No volumes. No Dockerfile.
