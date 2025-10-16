# Making a Pull Request

Ready to contribute code or docs? This guide covers the pull request workflow we follow for Go Advanced Admin projects.

## Prerequisites

- A fork of the repository you are contributing to (e.g., `admin`, an integration repo, or `docs`).
- A feature branch dedicated to your change:
  ```bash
  git checkout -b feature/my-change
  ```
- All required tooling installed and tests passing locally.

## Commit Your Changes

1. Keep commits focused. Separate unrelated changes into their own pull requests.
2. Use clear commit messages that summarize what changed and why.
3. Run formatting and linting tools where applicable.
4. Update documentation and changelogs to reflect the change.

## Open the Pull Request

1. Push your branch to your fork.
2. Navigate to the upstream repository on GitHub and click **Compare & pull request**.
3. Fill in the pull request template:
   - **Summary:** Describe the change and link related issues.
   - **Testing:** List commands you ran or attach screenshots for UI updates.
   - **Checklist:** Confirm linting, tests, and documentation updates are complete.
4. Request reviews from maintainers or community members familiar with the area of the codebase.

## Collaborate on Reviews

- Respond to feedback promptly and courteously.
- Use follow-up commits to address review comments, and mark conversations as resolved when appropriate.
- Re-run tests after making updates to ensure nothing regressed.

## Merge and Celebrate

- Wait for all required checks and approvals before merging.
- Maintainers will handle the merge if you do not have permissions.
- After merging, delete your branch and follow up on any documentation deployment steps if needed.

Thank you for contributing—every pull request makes the project stronger!
