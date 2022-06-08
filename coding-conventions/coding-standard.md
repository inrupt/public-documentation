The definition of code quality can be categorized across two main dimensions:


## Tangible Things

- Branch coverage must adhere to the targets set for the project.

- Single purpose classes.

- Strict Lint rule enforcement.

- Using static analysis tools.

- Using an automated dependency checker - specifically Dependabot on all GitHub repositories.

- Security testing tools.

- Adherence to accepted structure (e.g. Java Maven archetype folder layout for source, tests and resources).

- Internationalisation from the start.

- Clear, but justified, use of recognised design patterns.

- A clear (and automatically enforcable) commit policy ([ConventionalCommits](https://www.conventionalcommits.org/en/v1.0.0/) has been recommended by Ruben Taelman, but Inrupt has not yet formally adopted anything).

- All Pull-Requests:
    - Must be reviewed by at least one other developer before being merged into the main code base.

    - Should be kept as short as possible (to allow quick and easy review).

    - Should be scoped to a single project (or sub-project).

    - Kick-off the CI build on creation, and automatically prevents Merging the PR if it breaks the build.

    - Our Continuous Integration pipeline will automatically fail the build on any static analysis violations, or a drop in branch code coverage from 100%.

- We enforce a consistent approach to error handling across our code bases, ensuring that all error conditions have access to rich contextual data (both static context (such as server up-time, server region, administrative preferred languages, etc.) and run-time context (such as request headers, client request WebID, client provided meta-data, etc.).

- We enforce full internationalization across our code bases, including supporting multilingual error messages, log messages, event messages, auditing messages, etc. We implement this support in a fully standardized way by using RDF vocabularies for all messages in the system.

- We provide the same consistent access to multilingual contextual data across all our codebases (i.e. not just for error handling), including logging, auditing, events and notifications. To support the variability of use-cases we again leverage the extensibility and interoperability of RDF as our underlying data model.

## Intangible Things

- Above all, strive for consistency (hence the enforcement of strict linting above, as that helps significantly).

- Code 'elegance'.

- 'Short' methods.

- All code being clear, precise and concise; meaning comments, class names, variable names, filenames, etc.

- Clear separation of interfaces.

- Highly descriptive and consistent error handling.

- Appreciation for the difficult, but crucial aspects of REST; self-descriptive messages and Hypertext As The Engine Of Application State (HATEOAS).

## Branching strategy

By default, the [GitHub Flow strategy](https://guides.github.com/introduction/flow/) should be followed:

- Main branch is the main development branch where all features and bugfixes are merged into. At any point in time, it should be deployable.
- Feature branches are created for every separate feature (including fixes) that is being developed, which are merged into the main branch once the feature is completed (via a PR).
- In order to link the state of the code to software releases, Git tags should be used to track releases, and a CHANGELOG file should document releases. Tags should be in the format `vX.X.X`, such as `v1.13.1`.

For projects that require the maintenance of multiple versions in parallel,
the following additions to this strategy can be made:

- Version-specific branches can be created when preparing an update to a legacy release. For example, if version 15 is the most recent release, a patch to version 14.x can be prepared in branch `version/14.x`. After release, this branch should be merged back into `main`.

**Note: Version branches should be created for major refactors in a future version, which should be merged into main as soon as possible. For example, if the current stable version is 15.x, and a change is planned which would require a major version change, a 16.x branch would be created, and then deleted once merged with main, which would then track 16.x.**

In both cases, feature branches must have a descriptive name following the format `type/short-description`,
where types from [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) can be used.
Examples of branch names are: `fix/broken-homepage`, `feat/fancy-button`.

Upon merging any branch into main, it should be ensured that not only the main branch is deployable, but *every commit* is deployable as well.
This means that every commit should be atomic, which means that each commit adds or fixes a specific thing, instead of containing partially-broken code.
This also ensures that each commit is revertible at any time.
Non-main branches can contain non-atomic commits for work in progress, but must then be rebased before merging to main.

## Common Principles (regardless of programming language)

### Delimit variables in message strings with `[` and `]`

In general (i.e., there's always justifiable exceptions, if rare) we always
delimit all variable values in message strings with `[` an `]`, including
messages defined in RDF vocabularies (i.e., it's a pretty universal
guideline!).

```javascript
// JavaScript
console.log(`Logged in successfully with WebID: [${webId}].`);
```

```java
// Java
throw new RuntimeException("Failed to open file: [" + filename + "], reason: [" + cause + "].");
```

```turtle
# Turtle vocabulary.
# (Inrupt's Vocab libraries (e.g., for [JavaScript](https://github.com/inrupt/solid-common-vocab-js))
# can replace placeholder values delimited by '{{' and '}}').
inrupt_ui_common:errFileUpload_exceededSizeLimit a rdfs:Literal ;
    rdfs:isDefinedBy inrupt_ui_common: ;
    rdfs:comment "Tip: To fix this problem, try and reduce the file size (e.g., crop an image, compress an audio file, etc.)."@en ;
    skos:definition "File [{{0}}] has size [{{1}}] that exceeds the allowable limit of [{{2}}]"@en ;
    skos:definition "La taille du fichier [{{1}}], de [{{0}}], dépasse la limite autorisée [{{2}}]"@fr .
```

#### Why?

The main reason is to more easily spot pesky instances of leading and/or
trailling whitespace (which can be very tricky to spot when debugging), but
also to consistently denote the notion of 
'[_**here be a value that is variable in some sense**_]'.

#### Why specifically the characters `[` and `]`?

No particular reason, except they seem to work well across all programming
and human-spoken languages, and don't seem to be intrusive to the eye, even to
non-technical end users (and therefore also applicable to highly styled user
interfaces).


### Avoid mutation by default

Try to avoid mutating objects, creating altered copies of data structures rather than manipulating them. This is not a hard-and-fast rule: mutable data is fine when the scope is small (roughly: "all references to the variable are visible in your editor without scrolling" - and yes, that's not a strict rule given different screen sizes), or when immutability would result in performance issues for your use-case.

#### Why?

To avoid changes made in one place in your code from cascading down to other places.

#### What does that look like (in JavaScript)?

Instead of:

```javascript
const numbers = [1, 2, 3];
	
for (let i = 0; i < numbers.length;i++) {
  numbers[i] = numbers[i] * 2;
}
```	

We would prefer:

```javascript
const numbers = [1, 2, 3];
const doubledNumbers = numbers.map(number => number * 2);
```

So we're not modifying the original numbers (nor are we initialising a mutable `i`, but since its scope is small, that would be fine).

## Style Guides

We provide explicit style guides for both Java and JavaScript/TypeScript development. It’s important to remember that these are only guides. Developers are explicitly allowed to deviate from these guidelines, but only with good reason (so code reviewers are responsible for identifying, discussing (if necessary) and agreeing with any deviations before approving Pull Requests). There's no way to predict every possible future situation, and any developer can bring new insights to a team. So use your best judgment - but do discuss potential deviations with your colleagues, if only because they might learn something from you!

[Java specific guidelines](java-coding-standards.md "Java specific guidelines")

[Javascript specific guidelines](javascript-coding-standards.md "Javascript specific guidelines")
