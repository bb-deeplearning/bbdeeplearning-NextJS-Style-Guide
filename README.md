# bbdeeplearning.systems NextJS Style Guide

An opinionated and deliberately strict style guide for NextJS projects at bbdeeplearning.systems. This guide enforces rigorous TypeScript usage, functional programming patterns, and maintainable React practices.

> **Note**: This guide specifically covers NextJS projects. Companion style guides for other technologies we use (NestJS, Go, Django) are in development and will follow similar principles of strict typing, consistent patterns, and maintainable practices.

## Why So Strict?

This style guide was developed in response to real challenges we faced with large-scale React applications:

1. **Learning from Experience**: Our work on the [feynChat](https://feyn.chat) repository (32,000+ lines) taught us valuable lessons about technical debt. As the codebase grew, inconsistent patterns and code duplication made it increasingly difficult to:
   - Implement new features
   - Reuse components effectively
   - Onboard new developers
   - Maintain code quality

2. **Professional Development**: These strict standards serve two purposes:
   - Ensure consistently high-quality, maintainable code across all projects
   - Help developers internalize professional best practices early in their careers

3. **Future-Proofing**: While these standards may seem extreme, they:
   - Prevent common scalability issues before they occur
   - Reduce technical debt accumulation
   - Make large codebases more manageable
   - Enforce consistency across teams

4. **Quality Assurance**: The strict rules around component structure, typing, and testing ensure:
   - Better code reusability
   - Easier debugging
   - More reliable applications
   - Simplified maintenance

Remember: It's easier to maintain high standards from the start than to refactor problematic code later.

## Features

- Strict TypeScript configuration
- Functional programming patterns
- React best practices
- Consistent code formatting
- Accessibility requirements
- Performance guidelines

## Key Files

- [Contributing Guide](CONTRIBUTING.md) - Detailed coding standards and guidelines
- [ESLint Configuration](.eslintrc.json) - Strict linting rules
- [Prettier Configuration](.prettierrc) - Code formatting rules

## Getting Started

### 1. Initialize Next.js Project
```bash
bunx create-next-app@latest
```

Configure your project with these required settings:
✓ Would you like to use TypeScript? Yes
✓ Would you like to use ESLint? Yes
✓ Would you like to use Tailwind CSS? Yes
✓ Would you like to use `src/` directory? No
✓ Would you like to use App Router? Yes
✓ Would you like to customize the default import alias (@/*)? No

### 2. Code Quality Tools Setup

#### 1. Prettier

```bash
bun add --dev --exact prettier
```

Copy the `.prettierrc` configuration from this repository to your project.

Configured with strict formatting rules including:
- 80 character line limit
- Tab indentation (4 spaces)
- Single quotes
- Single attribute per line in JSX

#### 2. ESLint
```bash
bun add --dev eslint-plugin-react-compiler@beta
bun add --dev eslint-config-prettier
bun add --dev eslint-plugin-import eslint-import-resolver-typescript
bun add --dev eslint-plugin-functional@4.4.1
```

Copy the contents of `.eslintrc.json` from this repository into the `.eslintrc.json` file in your project.

ESLint configuration includes:
- React Compiler rules
- Strict TypeScript checks
- Functional programming patterns
- Import organization
- Accessibility standards

Note: eslint-plugin-functional@4.4.1 was specifically chosen for compatibility reasons to prevent linting errors.


## License

MIT © bbdeeplearning.systems
