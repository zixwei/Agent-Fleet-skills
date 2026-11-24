# Isolated checks

Prepare an empty temporary directory as `REVIEW_SNAPSHOT`. For a pinned commit, run `git archive "$REVIEW_HEAD" | tar -xf - -C "$REVIEW_SNAPSHOT"` in Bash with `pipefail` enabled. If export attributes or submodules affect the files needed for checks, copy those files from the pinned revisions. For local changes, include the file versions in the selected review scope. Exclude Git metadata, local credentials, and machine-specific files.

Choose a trusted local Linux image with a shell, `tar`, and the project toolchain. Supply compatible dependencies offline. If manifests and lockfiles match the trusted baseline, add a self-contained copy of its dependencies at the path the runner expects, such as `node_modules` in the snapshot for Node. For Python, use an image with a compatible virtualenv and invoke its interpreter by path. If dependencies changed, use an offline environment prepared for those versions. Pick the check from the trusted baseline.

This Bash example streams the snapshot into a temporary filesystem and runs Node's built-in tests. Set `REVIEW_SNAPSHOT` to the disposable directory and `REVIEW_IMAGE` to the local image. Replace `node --test` with the project's check when needed.

```bash
(
  set -o pipefail
  tar -C "${REVIEW_SNAPSHOT:?set the snapshot directory}" -cf - . |
    docker run --rm -i --pull=never --network=none --read-only \
      --user 65534:65534 --cap-drop=ALL --security-opt=no-new-privileges \
      --tmpfs /tmp:rw,nosuid,nodev,exec,mode=1777 --env HOME=/tmp \
      --entrypoint /bin/sh "${REVIEW_IMAGE:?set the local image}" \
      -ec 'mkdir /tmp/review; tar -xf - -C /tmp/review; cd /tmp/review; exec "$@"' \
      -- node --test
)
```

Keep the container free of host mounts, forwarded credentials, and the Docker socket. If the image, runtime, or offline dependencies are unavailable, perform a static review and record the skipped checks and reasons in the report.
