# Creating a Reproduction

A minimal reproduction helps us diagnose issues faster. This guide shows you how to isolate a problem and share it with
maintainers when reporting bugs or requesting help.

## Goals of a Reproduction

- **Minimal:** Include only the code and configuration needed to trigger the problem.
- **Deterministic:** Anyone should be able to follow your steps and see the same issue.
- **Documented:** Clearly explain how to run the reproduction and what outcome to look for.

## Steps to Build One

1. **Start from scratch:** Create a new project or use the official examples as a base. Avoid sharing your entire
   application.
2. **Add only relevant code:** Copy the smallest amount of logic that still produces the bug. Remove unrelated packages
   or features.
3. **Document setup:** Include a README or instructions detailing required environment variables, database setup, and the
   exact commands to run.
4. **Automate where possible:** Provide scripts or `Makefile` targets to set up dependencies and run the reproduction.
5. **Verify:** Run through the instructions on a clean environment to ensure they work end-to-end.

## Share the Reproduction

- Host the code in a public GitHub repository or [GitHub Gist](https://gist.github.com/) and link to it from your bug
  report or discussion.
- Mention relevant versions (Go, database, integrations) and any external services involved.
- Keep the reproduction updated if you discover additional information or if maintainers request adjustments.

High-quality reproductions save everyone time and lead to faster fixes—thank you for investing the effort!
