# CHANGELOG for `redis_cluster`

Inspired by [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

Note: this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [0.3.5] - Unreleased
### Fixed
- Fixed a Ruby 3 compatibility bug where keyword args could not be passed through Node#execute.

## [0.3.4] - 2023-10-24
### Changed
- Added error handling to refresh IP addresses when a `CannotConnectError` exception is encountered.
