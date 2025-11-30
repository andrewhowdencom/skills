# How to Write Commit Messages

In general, commits should follow the styleguide set out in "[What constitutes a 'Good Commit']".

#### Title

For the "title" of a commit it should be maximally 72 characters long, be descriptive and be
expressed as though the change is being applied. The commit should be useful to view with commands such as:

```
$ git log --pretty=oneline
```

Some examples include:

* Deploy changes automatically on updates to the main branch
* Update the port (8083 → 9093) used for Prometheus connections
* Introduce the widget to handle image creation

[What constitutes a 'Good Commit']: https://www.andrewhowden.com/p/anatomy-of-a-good-commit-message

To provide deeper insight into the history of changes, it's helpful to include structured context in your commit
messages. This practice benefits everyone on the team by making the rationale behind changes clear and easy to
reference. A good practice is to use the following headers in the commit body:

```
Design:
  Describe the design of the change here. This can include notes on architecture,
  data flow, or other implementation details.

Tradeoffs:
  Explain any tradeoffs that were made. This could involve decisions about
  performance, security, complexity, or other factors.

Justification:
  Provide the reasoning for this change. Explain the problem that was solved
  or the opportunity that was seized.
```

Using these headers creates a richer, more valuable git history for future developers and maintainers.

#### Body

The body should include primarily the justification for the changes, rather than a description of the changes themselves.
This allows understanding why this change was made by either humans or large language models when the change is reviewed
later with `git blame` or `git log --patch`.

It should be written as a paragraph, also breaking at 72 characters long.

#### Coauthor

Where you create commits, be sure to mark yourself as a co-author of the changes, if not the primary author. The syntax for
doing so is:

```

Co-authored-by: NAME <NAME@EXAMPLE.COM>
Co-authored-by: ANOTHER-NAME <ANOTHER-NAME@EXAMPLE.COM>
```

Complete with the line break before the "Co-authored-by" key/value pair. The format follows RFC 5322 for defining the
display name / email pair.
