# MMOU Website

Public website for Massive Multi-Task Omni Understanding and Reasoning.

Website: https://mmou-nv-umd.github.io/

Dataset: https://huggingface.co/datasets/nvidia/MMOU

## Development

Use Node.js 24 and npm.

```sh
npm ci
npm run dev
```

## Production Build

```sh
npm run build
```

The production site is generated in `dist/`.

## Deployment

GitHub Actions builds and deploys the site to GitHub Pages on every push to
`main`. The workflow can also be started manually from the Actions tab.

The publishing source in Settings > Pages is GitHub Actions.
