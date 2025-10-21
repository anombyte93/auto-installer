# Contributing to Auto-Installer

Thank you for your interest in contributing! This project welcomes contributions from the community.

## How to Contribute

### Reporting Bugs

Found a bug? Please open an issue with:
- Clear description of the problem
- Steps to reproduce
- Expected vs actual behavior
- Environment details (OS, AI tool, package being installed)
- Error messages or logs

### Suggesting Features

Have an idea? Open an issue with:
- Clear description of the feature
- Use case / motivation
- Expected behavior
- Example usage

### Submitting Code

1. **Fork the repository**
2. **Create a feature branch** (`git checkout -b feature/amazing-feature`)
3. **Test your changes** with realistic scenarios
4. **Update documentation** if needed
5. **Commit your changes** (`git commit -m 'Add amazing feature'`)
6. **Push to the branch** (`git push origin feature/amazing-feature`)
7. **Open a Pull Request**

## Development Guidelines

### Testing New Features

Test with at least 3 scenarios:
1. **Simple**: Basic package installation
2. **Complex**: Multi-package, multi-target
3. **Edge Case**: Jump hosts, GPU, version-specific, etc.

### Code Quality

- Maintain autonomous execution (no manual steps)
- Add comprehensive error recovery
- Include verification tests
- Update SKILL.md with new capabilities

### Documentation

- Update README.md with new features
- Add examples for new use cases
- Document any new requirements

## Tournament Methodology

This project uses tournament-based optimization:
1. Create variations of the feature
2. Test with realistic scenarios
3. Compare using weighted evaluation
4. Iterate until convergence

If adding major features, consider running a mini-tournament to validate quality.

## Quality Standards

- **Correctness** > Speed
- **Safety** > Convenience
- **Autonomy** > User interaction
- **Transparency** > Abstraction

## Questions?

Open an issue for discussion or reach out to maintainers.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
