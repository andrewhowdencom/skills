# Project Template

This repository is a template for new software projects, designed to guide AI software engineers (agents) through a
structured, documentation-driven development process.

## AGENTS.md

The `AGENTS.md` file serves as the "README for AI Agents". Just as `README.md` communicates important context to human
developers, `AGENTS.md` provides context, conventions, and instructions specifically for AI coding agents.

In this project, it acts as a conditional router, directing agents to detailed documentation in the
`/docs/development/` directory based on the specific task they are performing. This ensures agents adhere to the
project's established conventions and best practices.

### Usage with Agents

Most modern AI coding tools will automatically detect and consume `AGENTS.md` if it exists in the root of the
repository.

*   **Jules:** Automatically reads `AGENTS.md` to understand project-specific constraints and commands.
*   **Cursor:** Indexes `AGENTS.md` to improve code generation and chat responses.
*   **Aider:** Can be configured to read `AGENTS.md` for context.
*   **GitHub Copilot:** Uses `AGENTS.md` (or similar instruction files) to tailor suggestions.

### Learn More

*   To understand the specific instructions for *this* project, simply read the [AGENTS.md](./AGENTS.md) file.
*   To learn more about the `AGENTS.md` standard and its adoption across the industry, visit
    [agents.md](https://agents.md).

## Documentation

The documentation in `/docs/development/` is structured according to the [Divio documentation
system](https://documentation.divio.com/), which categorizes content into four types:

*   **Tutorials:** Learning-oriented guides to help the agent get started.
*   **How-to guides:** Goal-oriented steps to solve specific problems.
*   **Reference:** Technical descriptions of the machinery and how to operate it.
*   **Explanation:** Understanding-oriented discussions of concepts and background.

This structure provides a clear and comprehensive resource for the agent, enabling it to work efficiently and
effectively.
