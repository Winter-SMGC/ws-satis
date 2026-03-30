# ws-satis

A private [Satis](https://github.com/composer/satis) Composer package registry for the Winter-SMGC organization. It automatically builds and publishes PHP packages to GitHub Pages on demand.

**Registry URL:** https://winter-smgc.github.io/ws-satis

## Requirements

- PHP 8.4+
- [Composer](https://getcomposer.org/)

## Usage

Add the registry to your project's `composer.json`:

```json
{
  "repositories": [
    {
      "type": "composer",
      "url": "https://winter-smgc.github.io/ws-satis"
    }
  ]
}
```

Then require a package as usual:

```bash
composer require winter-smgc/<package-name>
```

## Adding a New Package

1. Add a new entry to the `repositories` array in `satis.json`:

```json
{
  "type": "vcs",
  "url": "https://github.com/Winter-SMGC/<repository-name>"
}
```

2. Commit and push the change.
3. Trigger a rebuild via the GitHub Actions workflow (see [Building the Registry](#building-the-registry)).

## Building the Registry

The registry is built automatically by the GitHub Actions workflow defined in `.github/workflows/build.yml`. It can be triggered in two ways:

- **Manually** — via the *Run workflow* button in the GitHub Actions UI.
- **Via API** — send a `repository_dispatch` event with event type `rebuild`:

```bash
curl -X POST \
  -H "Authorization: Bearer <GITHUB_TOKEN>" \
  -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/Winter-SMGC/ws-satis/dispatches \
  -d '{"event_type":"rebuild"}'
```

### Build Steps

1. Checks out the repository.
2. Sets up PHP 8.4 with Composer.
3. Installs Satis (`composer/satis:dev-main`).
4. Runs `php satis-tool/bin/satis build satis.json public --no-interaction`.
5. Deploys the generated `public/` directory to GitHub Pages.

## Local Build

```bash
# Install Satis
composer create-project --keep-vcs --no-dev composer/satis:dev-main satis-tool \
  --no-interaction \
  --no-progress

# Build the registry
php satis-tool/bin/satis build satis.json public --no-interaction
```

The generated repository will be available in the `public/` directory.

## Configuration

All repository and archive settings are defined in `satis.json`:

| Field | Value | Description |
|-------|-------|-------------|
| `name` | `winter-smgc/registry` | Registry identifier |
| `homepage` | https://winter-smgc.github.io/ws-satis | Public URL |
| `require-all` | `true` | Index all versions of every listed package |
| `archive.directory` | `dist` | Where ZIP archives are stored |
| `archive.format` | `zip` | Archive format |
| `archive.skip-dev` | `true` | Exclude dev dependencies from archives |
| `output-html` | `false` | Disable HTML page generation |

## Packages

| Package | Source |
|---------|--------|
| `ws-plugin-banner` | https://github.com/Winter-SMGC/ws-plugin-banner |

## License

See individual packages for their respective licenses.