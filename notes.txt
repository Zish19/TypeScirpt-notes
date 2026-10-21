What is TypeScript?
>> TypeScript being a Syntactic Superset means it shares the same base syntax as JavaScript, but adds something to it.

Why should I use TypeScript?
>> TypeScript uses compile time type checking.
>> Which means it checks if the specified types match before running the code, not while running the code.

TypeScript Compiler
>> TypeScript being converted into JavaScript means it runs anywhere that JavaScript runs!

Note: If you're using the tsconfig.json configuration mentioned earlier, you would place your TypeScript files in the src directory and the compiled JavaScript will appear in the build directory.

Best Practice: Use explicit types for function parameters and return types to make your code more maintainable and self-documenting.

Note: Type inference works best when variables are initialized at declaration.
Uninitialized variables have type 'any' by default unless you enable strictNullChecks in your tsconfig.json.

Watch Out: While type inference is convenient, being explicit with types can make your code more maintainable, especially in larger codebases or public APIs.

TypeScript Special Types
>> These special types are part of TypeScript's type system and help make your code more type-safe and self-documenting.

When to use any:
- When migrating JavaScript code to TypeScript
- When working with dynamic content where the type is unknown
- When you need to opt out of type checking for a specific case

any can be a useful way to get past errors since it disables type checking, but TypeScript will not be able to provide type safety, and tools which rely on type data, such as auto completion, will not work.
Remember, it should be avoided at "any" cost...

Key differences between unknown and any:
- unknown must be type-checked before use
- You can't access properties on an unknown type without type assertion
- You can't call or construct values of type unknown

When to use unknown:
- When working with data from external sources (APIs, user input, etc.)
- When you want to ensure type safety while still allowing flexibility
- When migrating from JavaScript to TypeScript in a type-safe way

Type narrowing with unknown:
>> You can narrow down the type of an unknown value using type guards.

Common use cases for never:
- Functions that never return (always throw an error or enter an infinite loop)
- Type guards that never pass type checking
- Exhaustiveness checking in discriminated unions

When to use never:
- For functions that will never return a value
- In type guards that should never match
- For exhaustive type checking in switch statements
- In generic types to indicate certain cases are impossible

Key points about undefined and null:
- undefined means a variable has been declared but not assigned a value
- null is an explicit assignment that represents no value or no object
- In TypeScript, both have their own types: undefined and null respectively
- With strictNullChecks enabled, you must explicitly handle these types

Important: These types are most useful when strictNullChecks is enabled in your tsconfig.json file.
This ensures that null and undefined are only assignable to themselves and any.
To enable strict null checks, add this to your tsconfig.json:
>>
{
  "compilerOptions": {
    "strictNullChecks": true
  }
}

Typed Arrays
>> Even though we have a boolean, string, and number the order matters in our tuple and will throw an error.

Readonly Tuple
>> If you have ever used React before you have worked with tuples more than likely.
useState returns a tuple of the value and a setter function.
const [firstName, setFirstName] = useState('Dylan') is a common example.
Because of the structure we know our first value in our list will be a certain value type in this case a string and the second value a function.

Named Tuples
>> Named tuples provide more context for what our index values represent.

Index Signatures
>> Index signatures can also be expressed with utility types like Record<string, number>.

String Enums
>> Technically, you can mix and match string and numeric enum values, but it is recommended not to do so.

Return Type
>> If no return type is defined, TypeScript will attempt to infer it through the types of the variables or expressions returned.

Parameters
>> If no parameter type is defined, TypeScript will default to using any, unless additional type information is available as shown in the Default Parameters and Type Alias sections below.

Casting with as
>> Casting doesn't actually change the type of the data within the variable, for example the following code will not work as expected since the variable x still holds a number.
        let x: unknown = 4;
        console.log((x as string).length); // prints undefined since numbers don't have a length
>> TypeScript will still attempt to typecheck casts to prevent casts that don't seem correct, for example the following will throw a type error since TypeScript knows casting a string to a number doesn't make sense without converting the data:
        console.log((4 as string).length); // Error: Conversion of type 'number' to type 'string' may be a mistake because neither type sufficiently overlaps with the other. If          this was intentional, convert the expression to 'unknown' first.
The Force casting section below covers how to override this.

Casting with <>
>> This type of casting will not work with TSX, such as when working on React files.

Members: Visibility
>> There are three main visibility modifiers in TypeScript.
- public - (default) allows access to the class member from anywhere
- private - only allows access to the class member from within the class
- protected - allows access to the class member from itself and any classes that inherit it.

>> The this keyword in a class usually refers to the instance of the class.

Inheritance: Implements
>> A class can implement multiple interfaces by listing each one after implements, separated by a comma like so: class Rectangle implements Shape, Colored {

Override
>> By default the override keyword is optional when overriding a method, and only helps to prevent accidentally overriding a method that does not exist.
>> Use the setting noImplicitOverride to force it to be used when overriding.

Abstract Classes
>> Abstract classes cannot be directly instantiated, as they do not have all their members implemented.

Functions
>> TypeScript can also infer the type of the generic parameter from the function parameters.

Classes
>> TypeScript can also infer the type of the generic parameter if it's used in a constructor parameter.

Type Aliases
>> This also works with interfaces with the following syntax: interface Wrapped<T> {

Extends
>> This can be combined with a default value.

Record
>> Record<string, number> is equivalent to { [key: string]: number }

Readonly
>> Keep in mind TypeScript will prevent this at compile time, but in theory since it is compiled down to JavaScript you can still override a readonly property.

TypeScript Null & Undefined
>> By default null and undefined handling is disabled, and can be enabled by setting strictNullChecks to true.
>> The rest of this page applies for when strictNullChecks is enabled.

Types
>> When strictNullChecks is enabled, TypeScript requires values to be set unless undefined is explicitly added to the type.

Null Assertion
>> Just like casting, this can be unsafe and should be used with care.

Using non-typed NPM packages in TypeScript
>> Editors such as Visual Studio Code will often suggest installing packages like these when types are missing.

Index Signature Labels
>> 5.x also now supports native JavaScript private fields.
>> The TypeScript 'private' still works.

Prerequisites: Install a recent Node.js LTS (v18+ recommended) and npm.
Verify with node -v and npm -v.

Note: You write TypeScript (.ts) during development and compile it to JavaScript (.js) for Node.js to run in production.

What these do:
- typescript adds the TypeScript compiler (tsc)
- @types/node provides Node.js type definitions
- npx tsc --init creates a tsconfig.json config file

Option highlights:
- rootDir/outDir: keeps source (src) separate from build output (dist).
- strict: enables the safest type checking.
- esModuleInterop: smoother interop with CommonJS/ES modules.
- sourceMap: generate maps for debugging compiled code.

CommonJS vs ESM: This guide uses module: "commonjs".
If you use ESM (type: "module" in package.json), set module: "nodenext" or node16, and use import/export consistently.

Warning: Use ts-node and nodemon only for development.
For production, compile with tsc and run Node on the JS output.

What TypeScript adds here:
- Typed Request, Response, and NextFunction for Express handlers.
- A User interface to guarantee the shape of user data.
- Safer refactoring and better autocompletion with typed route params and bodies.

TypeScript with Database
>> Before you start:
- Install packages: npm install typeorm reflect-metadata pg (use pg for PostgreSQL).
- Enable in tsconfig.json when using decorators:
         { "compilerOptions": { "experimentalDecorators": true, "emitDecoratorMetadata": true } }
- Import reflect-metadata once at app startup.

Note: The test script is optional and assumes Jest is set up.
If you are not using Jest, you can omit it.

Tip: Most IDEs (including VS Code) support TypeScript debugging with breakpoints when source maps are enabled.

Vite TypeScript types: Add Vite's ambient types to avoid missing definitions.
Example
// src/vite-env.d.ts
/// <reference types="vite/client" />

Alternatively, add to tsconfig.json:
Example
{
  "compilerOptions": {
    "types": ["vite/client"]
  }
}

About React.FC: Prefer directly typed function components.
React.FC is optional; it implicitly adds children but isn't required.

Optional baseUrl and paths: These can simplify imports if supported by your bundler.
Example
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]

    }
  }
}
Configure only if your tooling (e.g., Vite, tsconfig-paths) is set up for path aliases.

Note: While namespaces are still fully supported in TypeScript, modern applications typically use ES modules (import/export) for better modularity and tree-shaking support.
However, understanding namespaces is valuable for maintaining legacy codebases and certain library development scenarios.

Note: In JavaScript, all object keys are stored as strings, even numeric ones.
However, TypeScript makes a distinction to help catch logical errors when using arrays vs objects.

Important: When combining explicit properties with an index signature, the types of explicit properties must be assignable to the index signature's value type.

Promises in TypeScript
- Promise<T> - Generic type where T is the type of the resolved value
- Promise<void> - For Promises that don't return a value
- Promise<never> - For Promises that never resolve (rare)

Promise States and Type Flow
>> Promise State Flow:
- pending → fulfilled (with value: T) // Success case
- pending → rejected (with reason: any) // Error case
>>TypeScript tracks these states through the type system, ensuring you handle both success and error cases properly.

Key Benefits of Async/Await
- Readability: Sequential code that's easier to follow
- Error Handling: Use try/catch for both sync and async errors
- Debugging: Easier to debug with synchronous-like stack traces
- Type Safety: Full TypeScript type inference and checking

Async Function Return Types
>> All async functions in TypeScript return a Promise.
The return type is automatically wrapped in a Promise:
         async function getString(): string { } // Error: must return Promise
         async function getString(): Promise<string> { } // Correct

Note: All async functions in TypeScript return a Promise.
The type parameter of the Promise corresponds to the return type you declare after the Promise keyword.

Promise Combination Methods
- Promise.all() - Waits for all promises to resolve
- Promise.race() - Returns the first settled promise
- Promise.allSettled() - Waits for all to settle (success or failure)
- Promise.any() - Returns the first fulfilled promise

Warning: When using Promise.all with an array of promises that have different types, TypeScript will infer the result type as an array of the union of all possible types.
For more precise typing, you may need to use type assertions or define the expected structure.

Error Handling Strategies
- Try/Catch Blocks: For handling errors in async/await
- Error Boundaries: For React components
- Result Types: Functional approach with success/failure
- Error Subclassing: For domain-specific errors

Error Handling Patterns
>> Consider these patterns for robust error handling:
- Error Boundaries in React for component-level error handling
- Result Objects instead of throwing exceptions for expected cases
- Global Error Handler for uncaught exceptions
- Error Logging to capture and report errors

Note: The emitDecoratorMetadata option enables experimental support for emitting type metadata for decorators, which is used by libraries like TypeORM and class-validator.

TypeScript in JavaScript Projects (JSDoc)
>> JSDoc with TypeScript allows you to add type checking to JavaScript files without converting them to .ts.
This is perfect for gradual migration or when you want type safety in JavaScript projects.

Watch out for these common issues:
- Missing // @ts-check: Type checking won't work without it
- Incorrect JSDoc syntax: A single typo can disable type checking
- Type conflicts: When types from different sources don't match
- Inference issues: Sometimes TypeScript can't infer types correctly
- Performance: Large JavaScript files with complex types can be slow to check

Remember: While JSDoc provides excellent type checking, for new projects or complete migrations, consider using .ts files for the best TypeScript experience.

Important Note
>> For large projects, I strongly recommend the gradual migration approach to minimize disruption and make the process more manageable.

TypeScript 4.0+ Note
>> In TypeScript 4.0 and later, the unknown type is the default type for catch variables. Always narrow the type before accessing properties.

Common Pitfalls
>> Not Handling Promise Rejections
Always handle promise rejections to prevent unhandled promise rejection warnings:
// Bad: Unhandled promise rejection
fetchData();
// Good: Handle the rejection
fetchData().catch(console.error);

>> Catching Without Proper Type Narrowing
In TypeScript 4.0+, caught errors are of type unknown:
// Bad: Error is of type 'unknown'
try { /* ... */ } catch (error) {
  console.log(error.message); // Error: Property 'message' does not exist on type 'unknown'
}
// Good: Narrow the type
try { /* ... */ } catch (error) {
  if (error instanceof Error) {
    console.log(error.message); // OK
  }
}

>> Swallowing Errors
Avoid silently catching and ignoring errors without proper handling:
// Bad: Error is silently ignored
function saveData(data: Data) {
  try {
    database.save(data);
  } catch {
    // Ignore
  }
}
// Better: Log the error and/or notify the user
function saveData(data: Data) {
  try {
    database.save(data);
  } catch (error) {
    console.error('Failed to save data:', error);
    showError('Failed to save data. Please try again.');
  }
}

