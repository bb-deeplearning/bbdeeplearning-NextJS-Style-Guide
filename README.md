# bbdeeplearning.systems NextJS Style Guide

Opinionated style guide and configuration for NextJS projects at bbdeeplearning.systems. This guide enforces strict TypeScript usage, functional programming patterns, and maintainable React practices.

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

Follow the CLI prompts with these recommended settings:
- TypeScript: Yes
- ESLint: Yes
- Tailwind CSS: Yes
- `src/` directory: Yes
- App Router: Yes
- Import alias: Yes

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