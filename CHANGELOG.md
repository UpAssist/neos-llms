# Changelog

All notable changes to this project are documented in this file. The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

### Fixed

- `/llms.txt` returned 404 on Neos 9 sites with a `uriPathSuffix` (the Neos default is `.html`): the route part handler applied the site's suffix to the llms.txt route. The route now sets `uriPathSuffix: ''`. Sites without a suffix (such as upassist.com) were not affected.

### Changed

- Removed the PSR-4 autoload entry for a `Classes/` directory that does not exist (the package is Fusion and YAML only).
- README: exclusion rules, output example and cache description now match the actual behaviour; tested Neos versions listed.
