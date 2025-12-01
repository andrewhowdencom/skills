# How to Document Libraries

When creating or maintaining libraries intended for external consumption (i.e., not tied to a specific application but reusable by others), excellent documentation is critical for adoption and usability.

## README.md

Every external library must have a clear and comprehensive `README.md` at the root of the repository. This file serves as the primary entry point for users.

### Required Content

*   **Introduction:** A brief explanation of what the library does and why it exists.
*   **Installation:** Clear instructions on how to install the library.
*   **Examples:** Concrete code examples showing how to use the library's core features. Users should be able to copy-paste these examples to get started quickly.
*   **API Reference:** A link to the full API documentation (e.g., GoDoc for Go, Javadoc for Java) or a description of the public API if concise.

## Examples within the README

The examples section is the most important part of the README for new users. Ensure that:
*   The examples are self-contained and runnable.
*   They cover the most common use cases.
*   They clearly demonstrate the library's value proposition.
