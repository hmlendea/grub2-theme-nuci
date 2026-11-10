# Documentation Maintenance — GRUB2 Theme Nuci

Procedures for maintaining documentation quality and consistency.

## Documentation Structure

```
docs/
├── INDEX.md                      # Master index
├── architecture.md               # Architecture overview
├── design-decisions.md           # Key design decisions
├── invariants.md                 # System-wide invariants
├── repository-overview.md        # Repository purpose and scope
├── repository-structure.md       # Source tree layout
├── data-model.md                 # Data model specification
├── components/
│   └── theme-package.md          # Theme package deep-dive
├── integrations.md               # External integrations
├── build-and-deployment.md       # Installation and deployment
├── compatibility-contracts.md    # Stability contracts
├── testing.md                    # Test strategy and procedures
├── troubleshooting.md            # Common issues and fixes
├── faq.md                        # Frequently asked questions
├── api-reference/
│   ├── INDEX.md                  # API reference index
│   ├── theme-descriptor.md       # theme.txt grammar
│   ├── asset-specifications.md   # PNG requirements
│   └── icon-mapping.md           # Icon conventions
├── behaviour/
│   ├── boot-sequence.md          # Boot phase behaviour
│   ├── rendering.md              # Rendering pipeline
│   └── input-handling.md         # Input processing
├── flows/
│   ├── theme-load.md             # Theme load sequence
│   ├── asset-load.md             # Asset loading
│   └── menu-render.md            # Menu rendering
├── api-usage-examples.md         # Practical examples
├── ambiguities-and-open-questions.md  # Unresolved items
├── change-guide.md               # Change procedures
├── configuration.md              # Configuration options
├── concurrency-and-scheduling.md # Concurrency analysis
├── dependencies.md               # Dependency catalogue
├── documentation-maintenance.md  # This file
├── error-handling.md             # Error handling patterns
├── logging.md                    # Logging (N/A - documented)
├── quick-start.md                # Quick start guide
├── security.md                   # Security considerations
└── state-and-persistence.md      # State management
```

## Maintenance Principles

### 1. Single Source of Truth

- **Root documents** (README.md, CONTRIBUTING.md, SECURITY.md, etc.) are authoritative
- **docs/** documents complement and expand; do not duplicate root content
- Cross-reference with repository-relative links

### 2. Template Fidelity

- GitHub community files (CONTRIBUTING.md, SECURITY.md, etc.) follow strict templates
- Do not modify fixed prose; only fill placeholders
- See [github-md-template-fidelity](/home/horatiu/.copilot/skills/github-md-template-fidelity/SKILL.md)

### 3. British Spelling

- Use British English: organise, realise, licence, behaviour, colour, etc.
- See [language.english.instructions.md](/home/horatiu/.config/Code%20-%20OSS/User/prompts/instructions/language/language.english.instructions.md)

### 4. Caveman Mode

- Documentation should be concise, low-fluff
- Prefer tables, code blocks, diagrams over prose
- See [common.caveman-mode.instructions.md](/home/horatiu/.config/Code%20-%20OSS/User/prompts/instructions/common/common.caveman-mode.instructions.md)

## Update Triggers

| Trigger | Documents to Review |
|---------|---------------------|
| New asset added | asset-specifications.md, icon-mapping.md, repository-structure.md, compatibility-contracts.md |
| theme.txt changed | theme-descriptor.md, data-model.md, compatibility-contracts.md, api-usage-examples.md |
| New icon added | icon-mapping.md, repository-structure.md, faq.md |
| Installation procedure changed | build-and-deployment.md, configuration.md, quick-start.md, faq.md |
| GRUB2 version tested | compatibility-contracts.md, dependencies.md, testing.md |
| Bug fixed | troubleshooting.md, faq.md, ambiguities-and-open-questions.md |
| Security issue | security.md, SECURITY.md, privacy.md |
| New contributor | CONTRIBUTING.md, quick-start.md, change-guide.md |

## Review Checklist

Before merging documentation changes:

- [ ] British spelling used throughout
- [ ] Repository-relative links valid (test with `grep -r ']\(' docs/ | grep -v '^.*\.md:.*\](\.\./'` )
- [ ] No duplicate content with root documents
- [ ] Cross-references updated
- [ ] Mermaid diagrams render correctly
- [ ] Code blocks have correct language tags
- [ ] Tables use consistent formatting
- [ ] No trailing whitespace
- [ ] Line length reasonable (no hard limit, but avoid excessive)

## Link Validation

```bash
# Check for broken internal links
#!/bin/bash
# validate-links.sh

for f in docs/**/*.md docs/*.md; do
  grep -oP '\]\(\K[^)]+' "$f" | while read link; do
    # Skip external links
    [[ "$link" =~ ^https?:// ]] && continue
    [[ "$link" =~ ^mailto: ]] && continue
    [[ "$link" =~ ^# ]] && continue  # Anchor links

    # Resolve relative to file directory
    dir=$(dirname "$f")
    target="$dir/$link"
    target=${target%.md}  # Remove .md if present

    if [ ! -f "$target" ] && [ ! -f "$target.md" ] && [ ! -d "$target" ]; then
      echo "BROKEN LINK in $f: $link -> $target"
    fi
  done
done
```

## Style Guide

### Headings

- Use ATX style (`#`, `##`, `###`)
- One H1 per document
- Hierarchical: H1 → H2 → H3

### Tables

- Use for structured data (contracts, mappings, inventories)
- Header row required
- Align pipes for readability in source

### Code Blocks

- Always specify language: ```bash, ```text, ```mermaid
- Use for commands, config, diagrams
- Keep lines under 100 chars

### Diagrams

- Use Mermaid for sequence, flow, class diagrams
- Keep diagrams simple; split complex ones
- Test rendering in GitHub/GitLab

### Cross-References

- Use relative links: `[text](../file.md#anchor)`
- Prefer descriptive link text over "here" or "link"
- Link to specific sections when relevant

## Versioning

- Documentation versioned with repository (git tags)
- No separate documentation versioning
- Breaking changes noted in release notes

## Automation

### Pre-commit Hooks (Recommended)

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/igorshubovych/markdownlint-cli
    rev: v0.40.0
    hooks:
      - id: markdownlint
        args: ['--config', '.markdownlint.json']
```

### CI Validation (Recommended)

```yaml
# .github/workflows/docs.yml
name: Documentation
on: [push, pull_request]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Check links
        run: ./docs/validate-links.sh
      - name: Lint markdown
        run: npx markdownlint-cli docs/
```

## Related Documents

- [change-guide.md](./change-guide.md) — Change procedures
- [github-md-template-fidelity](/home/horatiu/.copilot/skills/github-md-template-fidelity/SKILL.md) — Template compliance
- [language.english.instructions.md](/home/horatiu/.config/Code%20-%20OSS/User/prompts/instructions/language/language.english.instructions.md) — Spelling rules