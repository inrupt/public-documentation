# JavaScript-Specific Guidelines 

## Organize code conceptually in packages 

-   Use a folder hierarchy to represent packages of related code.

-   Dependencies are usually tree-shaped.

    -   Other graph shapes might indicate an architectural problem.

-   One index file with non-default ES6 exports exposes the public API.

## Coding style

### Defensive programming 

-   Validate your assumptions (at the moment you make them):

    -   For typing, this is possible through TypeScript.

    -   Methods throw an error when called with invalid arguments.

### Asynchronicity 

-   Prefer async/await over explicit Promises, and prefer those over
    callbacks.

    -   Promise wrappers for native Node functions exist.

-   Use callbacks if needed for performance reasons.

    -   Especially for streams.

## Testing

-   Ensure all code is tested with unit tests, or has a comment explaining why there's no test and is [skipped for the coverage report](https://github.com/gotwarlost/istanbul/blob/master/ignoring-code-for-coverage.md)

    -   Classes have corresponding unit tests.

    -   Replace dependencies by mocks with validated assumptions.

    -   High coverage is important, but not a goal in itself.

    -   Not only ensure that code is used, but that its effects are tested.

-   Write integration tests for groups of classes or functions.

    -   Since each has one specific responsibility, integration tests check how those responsibilities interact. 

## Tools and Quality Practices

1.  Add an `.nvmrc` file to your repository, and [set up your shell](https://github.com/nvm-sh/nvm#deeper-shell-integration) to read it and switch to the correct Node/npm version automatically.

2.  Use Node 12.

3.  Use npm.

4.  Use TypeScript for larger projects; ES6 JavaScript (through Babel) is acceptable for apps.

    1.  Put source code on the `src` folder.

    2.  Put transpiled code in the `lib` folder.  (NO DECISION YET - Vinnie suggests dist instead to avoid confusion with projects using that folder for source code)
    
    3.  Add non-default export to an index file, ensuring that tree-shaking is possible with the `sideEffects` flag in `package.json`.

5.  Marking everything API that\'s intended for internal use but is externally accessible as `@internal` in its TSDoc, and [prevent TypeScript from emitting their declarations](https://www.typescriptlang.org/v2/en/tsconfig#stripInternal)

6.  Only use the `any` type as a last resort - which in practice basically means *never*. If you *do* use it, accompany it with a comment explaining why you chose to use it anyway. Keep in mind that some native methods return `any`, like `JSON.parse` -  those to a known type as soon as possible, preferably with a [type guard](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards).

7.  Write tests with Jest.

    1.  Put tests and mocks under a separate test folder, not under src.

    2. There are counter-arguments for having tests alongside the source (NO DECISION WHICH YET).

8.  Use ESLint.

    1.  On all code, including tests.

    2.  Use the `eslint-config-es` rule set. (NOT SURE WHAT THIS IS EXACTLY?)

9.  Use Prettier on all code, including tests.

10. Collect code coverage statistics with Jest.

    1.  Strive for 100% branch coverage.

    2.  Coverage does not tell the full story; for complex projects, consider Stryker as well.

11. Set up commit hooks with Husky to lint, compile, and test.

12. Set up Travis integration to run on Node 12 and LTS. (NO DECISION ON TRAVIS YET - E.G. WE USE GITHUB ACTIONS FOR THE LIT)

13. One repository is one npm module by default.

    1.  If more complex reuse becomes a necessity, consult with others before converting to a multi-package repo with Lerna.

14. When targeting the browser, use webpack. (NO DECISION ON WEBPACK YET - E.G. WE USE ROLLUP FOR SOME LIBRARIES)
    1.  Generate minified and non-minified versions.

    2.  Ensure that all generated versions are debuggable through a mapping file.

15. When including logging, use loglevel. (OR IS \'DEBUG\' A BETTER CHOICE HERE: <https://www.npmjs.com/package/debug> ?)

## Code structure (functional or object-oriented)

Generally, projects use either a functional or an object-oriented approach, or some combination of the two paradigms. When deciding on which approach to take, consider your use-case and your target audience. Some prominent JavaScript projects (e.g. React, Vue, Express) are largely function-oriented, whereas others (e.g. Angular, Sequelize) lean more strongly on OOP principles.

-   The React project has provided a detailed explanation for their current preference for the functional style (after initially being object-oriented): [here](https://reactjs.org/docs/hooks-intro.html#classes-confuse-both-people-and-machines)

-   The VueJS project has also detailed general problems with their Class API: [here](https://github.com/vuejs/rfcs/pull/17#issuecomment-494242121) (beware: this is just a tiny part of a much broader discussion within the Vue community!).

### Degree of encapsulation

By making sure that changes in one part of the code do not affect other parts of the code, as [pure functions](https://en.wikipedia.org/wiki/Pure_function) do, it\'s generally easier for people to modify that code without needing to be familiar with the broader codebase. Classes also provide conceptual encapsulation, but just at a coarser, less fine-grained level (i.e. by encapsulating multiple methods and multiple pieces of state within a single Class). So the overall principle here is that not having to keep the entire codebase in mind lowers the cognitive load. And even if you are familiar with the entire codebase, encapsulation makes it easier to avoid introducing bugs.

One consideration to help guide your choice might be the 'amount' of state needed to implement a feature. If you feel there is a 'lot' of inter-related or inter-dependent state that you feel should be encapsulated together, then perhaps a Class is warranted rather than long argument lists to pure functions.

### Default Guidance

Our default guidance (at the moment) is to strive for pure functions. The motivation here is just KISS - Kiss It Simple, Stupid.

### When using a functional approach 

When called with the same arguments, pure functions will always return the same values, and they do not manipulate state outside of themselves.

Additionally, pure functions are *really* easy to unit test: there is no need to mock out dependencies, or to add complex initialisation code before running the actual test. All you have to do is provide the function with some input, and assert that it returns the expected output.

##### What does that look like?

There's a thin layer in your code that deals with input and output, and passes control over to a pure function as soon as possible. For example:

```javascript
function onSubmit(event) {
  // Input side-effects
  event.preventDefault();
  const formData = { /* form data extracted from the DOM */ };

  // Hand over control to the pure function
  const request = getRequestFromFormData(formData);

  // Output side-effects
  fetch(request);
}

function getRequestFromFormData(formData) {
  // Validate/filter form data, call other pure functions, etc.
  // Then generate a new Request object and return it.
}
```

### When using object orientation

With this paradigm, we make the following decisions:

-   Organize your code as classes.

    -   Specifically, use ES6 classes.

    -   Since your class represents an instantiatable thing, name it with a noun. E.g., Command and CommandExecutor, *not* executeCommand.

    -   Typically there is one main class per file, which is the default export.

    -   Give your file the name of the class, plus the applicable file extension.

-   A class has a single responsibility.

    -   Usually, it should be possible to describe in one sentence what a class does.

-   A class encapsulates state.

    -   The class is responsible for keeping (only) its own state consistent.

    -   Consumers should not know and not depend on how an object is implemented; much less how its dependencies are implemented.

    -   If a method is consistently invoked with the same parameter, the consumer might have too much knowledge about the class's state. Consider making it a constructor parameter instead.

-   Classes have limited knowledge about how other classes work.

    -   Follow the [Law of Demeter](http://misko.hevery.com/2008/07/18/breaking-the-law-of-demeter-is-like-looking-for-a-needle-in-the-haystack/), and expect specific dependencies to be passed in rather than traversing object trees to find them.

-   Every class has a corresponding test file with unit tests.

    -   Write classes in a unit-testable way (which depends on architecture).

-   Write any exported executables as minimal wrappers around a class.

    -   Any command-line script instantiates a class with the right arguments.

    -   That class can be independently unit-tested (the script much less so).

#### Design for substitutability

-   There exist three kinds of objects: objects that *are* things,
    objects that *do* things, and objects that *make* things.

    -   The first group are data structures that represent a logical unit of information. They typically have few dependencies (mostly other data structures).

    -   The second group are classes that process data and/or interact with the environment. They can depend on other objects for behavior, but they should usually not instantiate those.

    -   The third group are factories, whose sole job is to instantiate other objects.

    -   A regular object should not know how to instantiate its dependencies; rather, it takes its dependencies as constructor arguments. This allows for unit testing and changing behavior. Only factories know how to construct object trees.

-   Prefer composition over inheritance for reuse of functionality.

    -   Inheritance is useful for polymorphism and substitutability; so do inherit for interface reasons.

    -   However, rather than relying too much on inherited functionality, extract that functionality into reusable classes.

    -   This simplifies testing and changing behavior, and avoids the need to retest inherited behavior.

## API design 

### Use an `options` parameter for optional parameters 

When a function has optional parameters, make it optionally accept an object containing those parameters.

#### Why?

This allows you to avoid having to change the API later when you add more options, it makes it clear to callers what 'boolean' options do, and ensures that callers do not have to pass `undefined` for options that they do not want to override.

#### What does that look like?

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

### Enable compile-time errors if possible 

Try to make sure that invalid invocations are not allowed by your type signatures.

#### Why? 

Because developers using TypeScript will get a warning if they call your code incorrectly at build time, rather than at runtime.

##### What does that look like? 

Instead of:

```javascript
function showTypeAndGender(personOrCompany: { type: 'person' | 'company', name?: string }) {
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
function showTypeAndGender(personOrCompany: { type: 'person', name: string } | { type: 'company' }) {
    console.log(personOrCompany.type === 'person' ? 'This is a person' : 'This is a company');

    if (personOrCompany.type === 'person') {
        console.log('This person is called', personOrCompany.name);
    }
}
```

(This is somewhat contrived, but the point is that if you notice you\'re writing code that handles runtime errors, see if you can change the code so that there is no valid code path that leads to that error - at least when using TypeScript. You might still want to include the runtime check for library consumers not using an editor or IDE that supports TypeScript.)
