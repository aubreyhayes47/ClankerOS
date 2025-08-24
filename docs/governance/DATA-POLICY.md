# Data & Artifact Storage Policy

ClankerOS uses [Git Large File Storage (LFS)](https://git-lfs.github.com/) to version binary assets that must live alongside source code.

## LFS Usage

- Track binary files such as `.bin`, `.tar.gz`, and `.zip` with Git LFS.
- Avoid committing generated or intermediate artifacts directly; use LFS only for sources requiring revision history.
- Patterns for LFS tracking are declared in `.gitattributes`.

## External Artifact Storage

Large build or test outputs are not stored in Git. Instead:

- Upload artifacts like FPGA bitstreams, compiled binaries, and datasets to an external object store (e.g., S3, GCS).
- Reference external artifacts using URLs or metadata files in the repository.
- Ensure each artifact is reproducible from source or accessible via the published storage location.

This policy keeps the repository lightweight while enabling reproducible builds and audits.
