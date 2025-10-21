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
4. **Update documentation** if needed (SKILL.md, EXAMPLES.md)
5. **Commit your changes** (`git commit -m 'Add amazing feature'`)
6. **Push to the branch** (`git push origin feature/amazing-feature`)
7. **Open a Pull Request**

## Development Guidelines

### Testing New Features

Test with at least 3 scenarios:
1. **Simple**: Basic package installation (e.g., single package on local system)
2. **Complex**: Multi-package, multi-target (e.g., several packages on multiple VMs)
3. **Edge Case**: Jump hosts, GPU, version-specific requirements, etc.

### Code Quality Standards

- Maintain autonomous execution (no manual steps for users)
- Add comprehensive error recovery
- Include verification tests
- Update SKILL.md with new capabilities
- Add examples to EXAMPLES.md

### Documentation

When adding features, update:
- **SKILL.md**: Add implementation details with bash commands
- **EXAMPLES.md**: Add real-world usage examples
- **README.md**: Update feature list if needed
- **CHANGELOG.md**: Document changes

## Quality Standards

**Priorities:**
- **Correctness** > Speed
- **Safety** > Convenience
- **Autonomy** > User interaction
- **Transparency** > Abstraction
- **Robustness** > Simplicity

## Testing Checklist

Before submitting a PR, verify:
- [ ] Feature works on target platforms (Linux/macOS/Windows)
- [ ] Error handling is comprehensive
- [ ] All commands execute autonomously
- [ ] Installation is verified functionally
- [ ] Documentation is updated
- [ ] Examples are added
- [ ] No manual user intervention required

## Examples of Good Contributions

### Adding Package Support
```markdown
**What**: Add support for Terraform CLI installation
**Testing**:
- Simple: Install terraform on local Ubuntu
- Complex: Install terraform on multiple VMs
- Edge: Install specific version of terraform
**Documentation**:
- Updated SKILL.md with terraform detection
- Added terraform verification commands
- Added example to EXAMPLES.md
```

### Improving Error Recovery
```markdown
**What**: Better handling of GPG key failures
**Testing**:
- Simulate GPG key failure
- Verify automatic retry with alternative method
**Documentation**:
- Updated error recovery section in SKILL.md
- Added troubleshooting note in README.md
```

### Adding Target Support
```markdown
**What**: Add support for Podman containers
**Testing**:
- Install packages in podman containers
- Verify detection works
**Documentation**:
- Updated target detection in SKILL.md
- Added podman examples to EXAMPLES.md
```

## Questions?

Open an issue for discussion or reach out to maintainers.

## License

By contributing, you agree that your contributions will be licensed under the MIT License.
