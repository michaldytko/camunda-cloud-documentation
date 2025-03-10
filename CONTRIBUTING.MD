# Contributing guide

## Getting started

- [Contributing guide](#contributing-guide)
  - [Getting started](#getting-started)
  - [Welcome](#welcome)
  - [Contributor guide](#contributor-guide)
  - [Prerequisites](#prerequisites)
    - [Contributor License Agreement](#contributor-license-agreement)
    - [Code of Conduct](#code-of-conduct)
    - [Code formatting](#code-formatting)
    - [Opening a pull request](#opening-a-pull-request)
    - [Conventional commits](#conventional-commits)
    - [Commit message header formatting](#commit-message-header-formatting)
    - [Code reviews](#code-reviews)
    - [Best practices for reviewers](#best-practices-for-reviewers)
      - [Inspiration](#inspiration)

## Welcome

Our documentation is open source, and we welcome new contributions. We take pride in maintaining and encouraging a friendly, welcoming, and collaborative open-source community. All contributors to our project are expected to adhere to our [Code of Conduct].

## Contributor guide

Anyone can open an issue regarding our documentation, or contribute a change with a pull request to update the documentation's content.

We would love it if you took the time to contribute to our project. You can do so in a variety of ways:

- Contribute your knowledge — If you see a section of the documentation that you feel you could improve by adding something that’s missing, whether that’s by adding a helpful tip, screenshot, or code snippet, please feel free to [open a pull request].
- Help us improve — If you have an idea for a new feature request, long-term improvements, or interesting solutions that you feel would benefit the Camunda Cloud ecosystem and our documentation, we’d be happy to look at your issue.
- File a bug report — Is a link broken? Does a screenshot not load? Does something need alt-text to improve accessibility? Don’t hesitate to reach out to us and let us know by [opening an issue].
- If you would like to get a better understanding of how our documentation is built and contribute back upstream to the tooling that this documentation uses, we encourage you to get started with the [Docusaurus 2] framework.

## Prerequisites

You need to be comfortable working in the either the [GitHub web-based editor] or a text editor such as Visual Studio Code or Atom. You will also need to understand the basics of [Git] and [GitHub] to have your best experience contributing to the Camunda.

### Contributor License Agreement

You will be asked to sign our [Contributor License Agreement] (CLA) when you open a pull request. We are not asking you to assign copyright to us, but to give us the right to distribute your code without restriction. We ask this of all contributors in order to assure our users of the origin and continuing existence of the code.

*Note: In most cases, you will only need to sign the CLA once.*

### Code of Conduct

By contributing to this repository, you are also expected to adhere to our community [Code of Conduct].

### Code formatting

Before submitting your pull request for code review, please ensure your code has met the requirements listed below:

- Is your code formatted according to our [code style guidelines]?
- Does your writing follow our [Technical Writing Style Guide]?
- Is your code covered by unit tests?
  - It is the responsibility of contributors to ensure that their contributed code passes automated testing.
  - Ask us if you are not sure where to write the tests or what kind of tests you should write.
  - Java: Please follow our [testing best practices].
  - Have a look at other tests in the same module for how it works.
  - In rare cases, it is not feasible to write an automated test. Please ask us if you think that is the case for your contribution.
- Do your commits follow the [Conventional Commits] guidelines and syntax?

### Opening a pull request

Before opening a pull request to the Camunda Cloud Documentation, it’s important to review our how-to’s. These are a variety of these [instructional documents] that detail how to get started on your contributor journey.

- [Documentation Guidelines] — These guidelines detail our process for adding a new documentation page, opening your first pull request, our code reviewers and their areas of expertise, and how to structure your documentation files.
- Review our [Pull Requests and Code Review] wiki.

### Conventional commits

Commit messages in Camunda projects should follow the [Conventional Commits] guidelines. By standardizing around this specification regarding [formatting of commit messages], this establishes a set of rules for creating a clear and cohesive commit history. This allows for maintainers to run automated tooling on top of a project, which lowers the burden on the community as a whole by ensuring tests can be introduced that improve the long-term health of the codebase.

This convention is utilized in conjunction with [SemVer] (semantic versioning), by describing the type, scope, and description made in commit messages.

The commit message should be structured as follows:

```
    <header>
    <BLANK LINE>
    <body>
    <BLANK LINE> (optional - mandatory with footer)
    <footer> (optional)
```

### Commit message header formatting

Commit message headers should follow this formatting example:

    `{type}(scope): {description}`

All header messages should be kept between 72-120 characters.

Examples:

- **docs(reference)**: Add start event to bpmn symbol support matrix.
- **perf(broker)**: Reduce latency in backpressure.
- **feat(clients/go)**: Allow more than 9000 jobs in a single call.

**Type** should be one of the following categories:

- **build**: Changes that impact the project’s build system (e.g. Maven, Docker, etc.)
- **ci**: Changes to a project’s CI configuration files and scripts (e.g. Jenkins, GitHub Actions, etc.)
- **deps**: A change to the external dependencies (this is already used by Dependabot.)
- **docs**: A change to the documentation
- **feat**: A proposed new feature
- **fix**: A bug fix
- **perf**: A code change that improves project performance
- **refactor**: A code change that does not change the project’s behavior
- **style**: A change to align the code with a project’s style guide
- **test**: Adding missing tests or correcting existing tests
- **chore**: Routine, reoccuring project maintenance
- **scope**: The name of the changed component (e.g. engine, journal, README)
- **description**: A short description of the proposed change, written in present tense

### Code reviews

Thorough code reviews are necessary to produce a high-quality product. When code contribution guidelines are unclear, the code review process can lead to frustration. The Camunda Cloud team strives to make code reviews a positive experience for both reviewers and contributors.

While new and returning contributors are highly encouraged to read the contributor guidelines before opening a pull request, experienced contributors and project maintainers should regularly review the contributor guide as well to ensure they are up to date.

### Best practices for reviewers

Code reviewers are a crucial part of any open source project, and as such, have distinct responsibilities.

- Minimize delays — Review pull requests you are assigned to on a weekly basis.
- Having doubts? Review the [code style guidelines].
- Principles, not opinions
  - Keep your feedback in scope of ensuring the principles of the documentation are adhered to. Avoid personal attacks when reviewing code.
- Bring the code up a letter grade or two
  - When reviewing, aim to provide feedback that allows the contributor to submit code that will boost the overall quality of the content in the codebase.

For further guidelines and best practices to keep in mind as a reviewer, please refer to our [Pull Requests and Code Review] wiki.

#### Inspiration

This documentation is inspired and influenced by the Zeebe documentation, the Camunda Platform 7 documentation, the Microsoft contributor guidelines,
the Apache Spark contributor guidelines, and the Kubernetes contributor guide.

[Getting Started]:./getting-started
[Welcome]: ./welcome
[Contributor Guide]: ./contributor-guide
[Prerequisities]: ./prerequisities
[Code of Conduct]: ./code-of-conduct.md
[documentation]: https://github.com/camunda-cloud/camunda-cloud-documentation
[open a pull request]: https://github.com/camunda-cloud/camunda-cloud-documentation/compare
[conventional commits]: https://www.conventionalcommits.org/en/v1.0.0/#summary
[variety]: https://github.com/camunda-cloud/camunda-cloud-documentation/tree/master/howtos
[Documentation guidelines]: https://github.com/camunda-cloud/camunda-cloud-documentation/blob/master/howtos/documentation-guidelines.md
[opening an issue]: https://github.com/camunda-cloud/camunda-cloud-documentation/issues
[Docusaurus 2]: https://v2.docusaurus.io/
[GitHub web-based editor]: https://docs.github.com/en/codespaces/the-githubdev-web-based-editor
[Git]: https://git-scm.com/
[GitHub]: https://lab.github.com/
[Contributor License Agreement]: https://cla-assistant.io/camunda-cloud/camunda-cloud-documentation
[code style guidelines]: https://github.com/camunda-cloud/zeebe/wiki/Code-Style
[testing best practices]: https://docs.camunda.io/docs/apis-clients/java-client/zeebe-process-test/
[Conventional Commits]: ./conventional-commits
[template and settings files]: https://github.com/camunda/camunda-bpm-platform/tree/master/settings
[Technical Writing Style Guide]: https://github.com/camunda-cloud/camunda-cloud-documentation/blob/master/howtos/technical-writing-styleguide.md
[Pull Requests and Code Review]: https://github.com/camunda-cloud/zeebe/wiki/Pull-Requests-and-Code-Reviews
[SemVer]: http://semver.org/
