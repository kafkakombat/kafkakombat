# Releases and Publishing Model

## Public release channel

KafkaKombat release artifacts are published through the project website:

- https://kafkakombat.com/releases

This keeps the primary download flow stable even if repository structure evolves.

## Recommended GitHub usage for the current stage

At the current public stage, GitHub can be used for:

- project overview and discovery
- linking to the public release page
- publishing release notes
- optionally attaching binary release artifacts to GitHub Releases

## Suggested release practice

Recommended approach:

1. keep the repository focused on project information and documentation
2. publish release notes in GitHub tags/releases
3. keep the canonical public download page on the website
4. optionally duplicate release archives in GitHub Releases for convenience

## Why not commit release archives into the repository tree

Committing large binary release archives directly into the Git repository makes repository history heavier and less convenient to maintain.

For this reason, release archives are better published through:

- the website release page
- GitHub Releases assets

rather than as regular tracked files in the repository tree.
