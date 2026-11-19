# Changelog

All notable changes to this project are documented in this file. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Changed

- The Neos 8 branch is renamed from `neos-8` to `1.x` (development constraint `1.x-dev`); README compatibility table lists the tested Neos versions per line.
- CI (`composer validate`), Dependabot for GitHub Actions (monthly, grouped) and auto-merge for minor/patch Dependabot updates.

## [1.2.1] - 2026-11-19

### Changed

- Constraint narrowed from `neos/neos: ^8.0` to `^8.3`: the range that is actually tested.
- README: the Neos 9 row of the compatibility table now points at `^2.0` (was `^0.3`).
- Removed the PSR-4 autoload entry for a `Classes/` directory that does not exist (the package is Fusion and YAML only).
- README: exclusion rules, output example and cache description now match the actual behaviour; tested Neos versions listed.
