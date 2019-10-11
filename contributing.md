# Contributing

Contributing rules are important! This will make sure the repository will be consistent and easy to maintain. This md will include:

- Versioning
- Flow control
- Commit message
- Merge checklist
- Style guide 

## Versioning
I'd like to talk about versioning first because this will be used in flow control.

A good practice for versioning is called <a href='https://semver.org'>*semantic versioning*</a>.

Tl;dr:

Given a version number MAJOR.MINOR.PATCH, increment the:

- MAJOR version when you make incompatible API changes,
- MINOR version when you add functionality in a backwards compatible manner, and
- PATCH version when you make backwards compatible bug fixes.


## Git Flow
There are many ways to manage workflow on git and Gitflow is just one flavour among all.

Gitflow is an abstract workflow for software development. The core idea of Gitflow is using two main branches - `master` for releasing and `develop` for continuous developing. We'll talk in more details soon.

![An abstract](https://wac-cdn.atlassian.com/dam/jcr:2bef0bef-22bc-4485-94b9-a9422f70f11c/02%20(2).svg?cdnVersion=623)

### Get git-flow

The git-flow toolkit could be downloaded to OSX by:

```bash
$ brew install git-flow
```
Or <a href='https://git-scm.com/download/win'>download and install Git</a> on windows.

### Setup

```bash
$ git flow init
Initialized empty Git repository in ~/project/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]
Branch name for "next release" development: [develop]

How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
```
This will guide you through a few branch setup. The default one would be nice enough to use. 

(Note: I noticed a few difference between OSX and Windows, but that won't hurt too much, just in case you noticed.) 

### Usage
As you might noticed above, `feature branches` are the main branches shall be interacting with `develop`, and `feature branches` shall NEVER be merged directly into `master` (only `develop` could get in touch with `master` ;).

Start a new feature on its branch:

```bash
$ git flow feature start feature_branch
```

Merge a finished feature back to develop:

```bash
$ git flow feature finish feature_branch
```

### Buffer zone between master and develop - release
It's not always the story that you have exactly what you want, super stable ready to ship in `develop` that can merge directly into `master`. Most the cases, when come to a point that `develop` is ready for a release, it shall be fork into the `release` branch (so development on other features could continue).

One solid rule about `release` is that no feature shall be added on `release`. Only documentation, bug fixing and other release-oriented job shall be merged to `release`.

And once all this is ready, `release` is ready for `master`. Don't forget to merge back to `develop` so the later development environment is up-to-date.

Start a release branch:

```bash
$ git flow release start 0.1.0  # Recall versioning above...
```

Finish a release branch:

```bash
$ git flow release finish '0.1.0'
```

### To fix a bug

Ok, I lied about only `develop` can talk to `master`, we also have `hotfix`.

There is only one purpose for `hotfix` - to fix a bug that exist on `master`.

Start to fix a bug:

```bash
$ git flow hotfix start hotfix_branch
```

Fixed a bug:

```bash
$ git flow hotfix finish hotfix_branch
```

## Commit message
Writing proper commit message is the best way to communicate with peer workers and your future self.
Here is how to make things easier and rules to follow:

>
- Separate subject from body with a blank line
- Limit the subject line to 50 characters
- Capitalise the subject line
- Do not end the subject line with a period
- Use the imperative mood in the subject line
- Wrap the body at 72 characters
- Use the body to explain what and why vs. how

(Ref: [Suggestions above are directly C&P from here](https://chris.beams.io/posts/git-commit/))


### Default editor
To be able to write long descriptive commit messages, try to use a default editor instead of `commit -m`.

setting:

```bash
$ git config --global core.editor vim  # I personally like vim, emacs or other editor could be used too
```

### Template
To provide a good guideline of commit messages, a pre-set template will make the rules easier to follow.

A good template (~/.gitmessage.txt) example:

```txt
Subject line (try to keep under 50 characters)

Multi-line description of commit,
feel free to be detailed.

[Ticket: X]
```
Set template:

```bash
$ git config --global commit.template ~/.gitmessage.txt
```

## Merge checklist
Before start up a merge request, tick all these boxes down below first:

- Pass all tests
- Keep out all irrelevant changes
- Update README.md if necessary
- Update version number

## Style guide
Majority Python projects would follow/partly follow PEP8. And it is very important to keep code style consistent for maintenance/readability reason.

A personal recommended system to ensure style consistency would include two components:

1. Use Pylint plugin to get real time guidance
2. Pre-commit hook checker to ensure commits' quality

A pre-commit hook include [*black*](https://github.com/psf/black) and [*flake8*](http://flake8.pycqa.org/) would be very powerful for *black* will reformat the code into standardised/consistent style and flake8 will check according to its rules (basically PEP8 rules).

### How to set up this hook

First, you need the pre-commit (remember to put this in your `requirements.txt` or equivalents):

```bash
$ pip install pre-commit
```

Then define what hooks you want to add before commit in `.pre-commit-config.yaml`.

To use *black* and *flake8*, in `.pre-commit-config.yaml` we will have:

```
repos:
-   repo: https://github.com/ambv/black
    rev: stable
    hooks:
    - id: black
      language_version: python3.6
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v1.2.3
    hooks:
    - id: flake8
```
 
 Finally, we can install all hooks by running:
 
 ```bash
 $ pre-commit install
 ```
 This will automatically search through hooks defined in the `.yaml` in `.git/`.
 
 Then you are ready to go. When you run a normal git commit, the pre-commit hooks will do the check first. If your code doesn't meet the quality, it will be rejected.

\[Update 20191011]
You might run into an issue with max length per line. And that's because the different default setting by *black* and *flake8*. 

Black allows a max length of 88 chars per line while flake8 is using old school 79 chars.

The fix is adjust either to make them match. I choose to adjust flake8 which could be done by adding a new file  `~/.config/flake8` with content: 

```
[flake8]
max-line-length = 88
```

This shall fix the problem. :D
 

#### Refs:
<a href='https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow'>GitFlow</a>

<a href='https://chris.beams.io/posts/git-commit/'>Git Commit</a>

<a href='https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration'>Git Configuration</a>

[Pre-commit hook](https://ljvmiranda921.github.io/notebook/2018/06/21/precommits-using-black-and-flake8/)

[flake8 config](http://flake8.pycqa.org/en/2.6.0/config.html)
