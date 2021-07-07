# JavaScript-Specific Guidelines

## Browser Support

- Test and target compilation for the latest two versions of the most popular browsers. The current list is:
  - Desktop:
    -  Chrome / macOS, Ubuntu, Windows
    -  FireFox / macOS, Ubuntu, Windows
    -  Safari  / macOS
    -  Edge (Chromium) / Windows
  - Mobile:
    -  iOS / Safari
    -  Android / Chrome
    -  Android / Samsung Internet
-  For applications, consider multiple screen sizes when implementing the UI:
    - Small: 360x640
    - Medium: 768x1024
    - Large: 1366x768
    - X-Large: 1600x900

## Organize code conceptually in packages

- Use a folder hierarchy to represent packages of related code.
- Dependencies are usually tree-shaped.
  - Other graph shapes might indicate an architectural problem.
- One index file with non-default ES6 exports exposes the public API.

## Coding style

### Defensive programming

- Validate your assumptions (at the moment you make them):
  - For typing, this is possible through TypeScript.
  - Methods throw an error when called with invalid arguments.

### Asynchronicity

- Prefer async/await over explicit Promises, and prefer those over
  callbacks.
  - Promise wrappers for native Node functions exist.
- Use callbacks if needed for performance reasons.
  - Especially for streams.

## Testing

- Ensure all code is tested with unit tests, or has a comment explaining why there's no test and is
  [skipped for the coverage report](https://github.com/gotwarlost/istanbul/blob/master/ignoring-code-for-coverage.md)
  - Classes have corresponding unit tests.
  - Replace dependencies by mocks with validated assumptions.
  - High coverage is important, but not a goal in itself.
  - Ensure not only that the code is used, but that its effects are tested.
  - Write integration tests for groups of classes or functions.
  - Since each test has one specific responsibility, integration tests check how those responsibilities
    interact. 

## Tools and Quality Practices

1. Add an `.nvmrc` file to your repository, and [set up your shell](https://github.com/nvm-sh/nvm#deeper-shell-integration)
  to read it and switch to the correct Node/npm version automatically.
2. Use Node 12.
3. Use npm.
4. Use TypeScript for larger projects; ES6 JavaScript (through Babel) is acceptable for apps.
  1. Put source code in the `src` folder.
  2. Put transpiled code in the `dist` folder.
  3. Add non-default export to an index file, ensuring that tree-shaking is possible with the `sideEffects` flag in `package.json`.
5. Annotate everything in the API that\'s intended for internal use, but is externally accessible, as `@internal` in its TSDoc, and
  [prevent TypeScript from emitting their declarations](https://www.typescriptlang.org/v2/en/tsconfig#stripInternal)
6. Only use the `any` type as a last resort - which in practice basically means *never*. If you *do* use
  it, accompany it with a comment explaining why you chose to use it anyway. Keep in mind that some
  native methods return `any`, like `JSON.parse` - cast those to a known type as soon as possible, preferably
  with a [type guard](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards).
7. Write tests with Jest.
  1. Put tests next to source files. In libraries, this is typically done by having a `foo.ts` and `foo.test.ts`
    file; in apps, this is typically done by
    using folders (`foo/index.jsx`, `foo/index.test.jsx`, `foo/styles.js`).
8. Use ESLint.
  1. On all code, including tests.
  2. Use the [Inrupt standard eslint configs](https://github.com/inrupt/javascript-style-configs).
9. Use Prettier on all code, including tests. (This will be done by the linter.)
10. Collect code coverage statistics with Jest.
  1. Strive for 100% branch coverage.
  2. Coverage does not tell the full story; for complex projects, consider TestCafe as well.
11. Set up commit hooks with Husky to lint, compile, and test before pushing.
12. Set up GitHub actions to run on Node 12 and LTS.
13. One repository is one npm module by default.
  1. If more complex reuse becomes a necessity, consult with others before converting to a multi-package repo with Lerna.
14. When building applications, use the Next.js framework. For documenting components, use Storybook. For libraries, use rollup.
  1. Generate minified and non-minified versions.
  2. Ensure that all generated versions export a mapping file.
15. When including logging, use [loglevel](https://www.npmjs.com/package/loglevel).

## Library and API design
JavaScript is a multi-paradigm language that supports
functional and object-oriented programming.
Within those paradigms, several degrees of freedom exist;
for instance,
functions can be pure or non-pure,
and objects can be mutable or immutable.

**Within one library, one paradigm will generally be dominant.**
For example, a library will export either a set of classes
(with perhaps a smaller number of functions to complement those classes)
or a set of functions
(with perhaps a number of objects used or created by those functions).

**The choice of paradigm should be influenced by the use case at hand.**
An example are the various DOM APIs,
where HTTP functionality is exposed as a function (`fetch`),
whereas element manipulations are exposed as objects (`Element`).

**Making these choices is not an exact science**,
but below are some principles to guide the decision process.
However, note that there are many considerations to codebase design beyond paradigms.
The differences between two functional approaches can be bigger
than the differences between a certain functional and object-oriented approach.
Therefore, this choice needs to be considered as one parameter to the overall design
rather than a single decisive factor.
Decisions can be facilitated by comparing different concrete API designs
rather than judging a single option in isolation.

Keep in mind that **design choices are a means to an end**,
and that end is ultimately to serve the users of our libraries
in the best way possible.
The important question is therefore
whether it makes the lives of developers better.
Some other guiding questions are:
- How **intuitive** is the design to use?
  - When new Solid or Linked Data concepts are introduced,
    objects could help make them tangible and thereby facilitate understanding.
- Which API design results in **simple client code**?
  - Write some common snippets of code for multiple designs and compare.
- How is exposed **functionality grouped** together?
    - By namespaces or naming conventions?
    - For object orientation, how is it distributed across objects?
- What **kinds of state** does the use case involve?
    - What state needs to be passed around?
    - How is the state encapsulated?
- What is the **life cycle** of data?
- To what extent do we need **different implementations** through the same abstractions?
  - Substitutability could be a signal for object orientation (`Element` and subclasses),
    whereas a lack thereof could be captured functionality (`fetch`).
- How many **entry points** do library users need to remember?
- How well does the design allow **IDE support** such as autocompletion?
- Are there **practical considerations for (im-)mutability**?
    - Is immutability needed for certain algorithms (such as diffing)?
    - Is mutability preferred for (measured!) performance issues?

### Guidance for object orientation

- Organize your code as classes.
  - Specifically, use ES6 classes.
  - Since your class represents an instantiatable thing, name it with a noun. E.g., Command and
    CommandExecutor, *not* executeCommand.
  - Typically there is one main class per file, which is the default export.
  - Give your file the name of the class, plus the applicable file extension.
- A class has a single responsibility.
  - Usually, it should be possible to describe in one sentence what a class does.
- A class encapsulates state.
  - The class is responsible for keeping (only) its own state consistent.
  - Consumers should not know and not depend on how an object is implemented; much less how its dependencies are implemented.
  - If a method is consistently invoked with the same parameter, the consumer might have too much knowledge about the class's
    state. Consider making it a constructor parameter instead.
- Classes have limited knowledge about how other classes work.
  - Follow the [Law of Demeter](http://misko.hevery.com/2008/07/18/breaking-the-law-of-demeter-is-like-looking-for-a-needle-in-the-haystack/),
    and expect specific dependencies to be passed in rather than traversing object trees to find them.
- Every class has a corresponding test file with unit tests.
  - Write classes in a unit-testable way (which depends on architecture).
- Write any exported executables as minimal wrappers around a class.
  - Any command-line script instantiates a class with the right arguments.
  - That class can be independently unit-tested (the script much less so).

#### Design for substitutability

- There exist three kinds of objects: objects that *are* things,
  objects that *do* things, and objects that *make* things.
  - The first group are data structures that represent a logical unit of information. They typically
    have few dependencies (mostly other data structures).
  - The second group are classes that process data and/or interact with the environment. They can
    depend on other objects for behavior, but they should usually not instantiate those.
  - The third group are factories, whose sole job is to instantiate other objects.
  - A regular object should not know how to instantiate its dependencies; rather, it takes its
    dependencies as constructor arguments. This allows for unit testing and changing behavior. Only
    factories know how to construct object trees.
- Prefer composition over inheritance for reuse of functionality.
  - Inheritance is useful for polymorphism and substitutability; so do inherit for interface reasons.
  - However, rather than relying too much on inherited functionality, extract that functionality into
    reusable classes.
  - This simplifies testing and changing behavior, and avoids the need to retest inherited behavior.

### API design

#### Use an `options` parameter for optional parameters

When a function has optional parameters, make it optionally accept an object containing those parameters.

This allows you to avoid having to change the API later when you add more options, it makes it clear to callers what 'boolean' options do, and ensures that callers do not have to pass `undefined` for options that they do not want to override.

Instead of e.g.

```javascript
function getElement(contents: string, target?: string, title? :string): HTMLElement {
  const element = document.createElement(target ? 'a' : 'span');
  element.textContent = contents;
  if (target) {
    element.setAttribute('href', target);
  }
  if (title) {
    element.setAttribute('title', title);
  }
  return element;
}

const element = getElement('Do not do this', undefined, 'This is visible on hover');
```

do something like

```javascript
function getElement(contents: string, options?: Partial<{ target: string; title: string; }>): HTMLElement {
  const element = document.createElement(options?.target ? 'a' : 'span');
  element.textContent = contents;
  if (options?.target) {
    element.setAttribute('href', options.target);
  }
  if (options?.title) {
    element.setAttribute('title', options.title);
  }
  return element;
}

const element = getElement('Try this instead', { title: 'This is visible on hover' });
```

#### Enable compile-time errors if possible

Try to make sure that invalid invocations are not allowed by your type signatures.

Because developers using TypeScript will get a warning if they call your code incorrectly at build
time, rather than at runtime.

Instead of:

```javascript
function showTypeAndName(personOrCompany: { type: 'person' | 'company', name?: string }) {
  console.log(personOrCompany.type === 'person' ? 'This is a person' : 'This is a company');

  if (personOrCompany.type === 'person') {
    if (!personOrCompany.name) {
        throw new Error('A person should have a name.');
    }

    console.log('This person is called', personOrCompany.name);
  }
}
```

we prefer:

```javascript
function showTypeAndName(personOrCompany: { type: 'person', name: string } | { type: 'company' }) {
  console.log(personOrCompany.type === 'person' ? 'This is a person' : 'This is a company');

  if (personOrCompany.type === 'person') {
    console.log('This person is called', personOrCompany.name);
  }
}
```

(This is somewhat contrived, but the point is that if you notice you\'re writing code that handles
runtime errors, see if you can change the code so that there is no valid code path that leads to
that error - at least when using TypeScript. You might still want to include the runtime check for
library consumers not using an editor or IDE that supports TypeScript.)
