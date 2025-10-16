# Adding Translations

Help us reach a global audience by translating the Go Advanced Admin documentation. This guide covers how to propose a
new language and contribute translated content.

## Decide What to Translate

- Start with high-traffic pages such as the Quick Start, Core Features, or Integration guides.
- Coordinate with other translators in [GitHub Discussions](https://github.com/go-advanced-admin/docs/discussions) to
  avoid duplicating work.
- If your language is not yet available, propose a structure for translated files (e.g., `docs/pt-br/`).

## Translation Workflow

1. **Open an issue:** Let maintainers know which language and pages you want to translate. Use the
   [documentation issue template](https://github.com/go-advanced-admin/docs/issues/new?template=documentation.yml) and tag
   it as a translation proposal.
2. **Create a branch:** Fork the repository and create a feature branch for your translation work.
3. **Copy the source page:** Duplicate the original English markdown file into a locale-specific folder. Keep the same
   file name so cross-links remain predictable.
4. **Translate the content:** Provide accurate, clear translations. Preserve code blocks, callouts, and formatting.
5. **Update navigation:** If you add a new language, update `mkdocs.yml` (or the relevant navigation file) to include the
   translated pages.
6. **Open a pull request:** Mention the tracking issue, list the translated pages, and request reviews from native
   speakers if possible.

## Translation Tips

- Keep terminology consistent across pages—consider creating a glossary for recurring technical terms.
- Leave short phrases (such as code keywords or package names) in English when translating would cause confusion.
- Review updates to the English docs regularly so translations stay in sync.

Translations make the project more accessible and welcoming. Thank you for helping the community grow!
