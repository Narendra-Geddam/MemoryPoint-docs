# MemoryPoint Docs

Optimized DevOps study notes with pre-rendered dark-mode diagram assets.

## Main Document

- [MemoryPoint-ALL.md](./MemoryPoint-ALL.md) - consolidated full reference, organized topic-wise.

## Source Documents

- [network-HAjenkins-ec2-ebs.md](./network-HAjenkins-ec2-ebs.md)
- [RBAC.md](./RBAC.md)
- [pod.md](./pod.md)
- [troubleshoot-lifecycle.md](./troubleshoot-lifecycle.md)

## Diagram Assets

All Mermaid diagrams are converted to SVG and embedded as Markdown images.

- `assets/diagrams/network-HAjenkins-ec2-ebs/`
- `assets/diagrams/RBAC/`
- `assets/diagrams/pod/`
- `assets/diagrams/troubleshoot-lifecycle/`

## Theme

Diagrams are rendered in dark mode for night reading.

## Rules for Any New Markdown File

When a new `.md` file is added:

1. Detect all Mermaid blocks.
2. Convert Mermaid diagrams to SVG under `assets/diagrams/<file-name>/`.
3. Render diagrams in dark mode (`theme: dark`, dark background).
4. Replace Mermaid code blocks in the source `.md` with image links to generated SVG files.
5. Keep all original educational depth and content (no content dropping).
6. Add the new file to `MemoryPoint-ALL.md` under the correct topic section.
7. Keep the master file organized topic-wise for revision clarity.
8. Update this `README.md` source/asset lists accordingly.
