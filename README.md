# Prisma GraphQL Generator

[![npm version](https://badge.fury.io/js/prisma-graphql-module-generator.svg)](https://badge.fury.io/js/prisma-graphql-module-generator)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Node.js](https://img.shields.io/badge/Node.js->=18.0-green.svg)](https://nodejs.org/)
[![CI](https://github.com/mhoppehh/prisma-graphql-module-generator/workflows/CI%2FCD%20Pipeline/badge.svg)](https://github.com/mhoppehh/prisma-graphql-module-generator/actions)
[![codecov](https://codecov.io/gh/mhoppehh/prisma-graphql-module-generator/branch/master/graph/badge.svg)](https://codecov.io/gh/mhoppehh/prisma-graphql-module-generator)
[![npm downloads](https://img.shields.io/npm/dm/prisma-graphql-module-generator.svg)](https://npmjs.org/package/prisma-graphql-module-generator)
[![TypeScript](https://img.shields.io/badge/TypeScript-Ready-blue.svg)](https://www.typescriptlang.org/)

A powerful and extensible Prisma generator that creates GraphQL schemas and TypeScript resolvers from your Prisma data models. This tool bridges the gap between your database schema and GraphQL API, providing type-safe, customizable code generation with a robust plugin system.

## 🌟 Features

- **🔄 Automatic GraphQL Schema Generation**: Convert Prisma models to GraphQL types, inputs, queries, and mutations
- **📝 TypeScript Resolver Generation**: Generate type-safe resolvers with proper Prisma client integration
- **🔌 Extensible Plugin System**: Built-in plugins for logging, formatting, validation, and field transformations
- **⚙️ Highly Configurable**: Flexible configuration via JSON files or TypeScript configs
- **🎨 Handlebars Templates**: Customizable output templates for both GraphQL schemas and resolvers
- **🔍 Smart Type Mapping**: Automatic conversion between Prisma and GraphQL types
- **📦 Custom Pluralization**: Support for custom plural forms and naming conventions
- **🎯 Interactive CLI**: User-friendly command-line interface with preset management

## 📦 Installation

```bash
npm install prisma-graphql-module-generator
# or
yarn add prisma-graphql-module-generator
# or
pnpm add prisma-graphql-module-generator
```

**Peer Dependencies:**
This package requires Prisma to be installed in your project:

```bash
npm install prisma @prisma/client
```

## 🚀 Quick Start

### 1. Add Generator to Prisma Schema

Add the generator to your `prisma/schema.prisma` file:

```prisma
generator graphql_typedef {
  provider = "prisma-graphql-module-generator"
  output   = "./generated"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  posts     Post[]
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}

model Post {
  id       Int    @id @default(autoincrement())
  title    String
  content  String?
  author   User   @relation(fields: [authorId], references: [id])
  authorId Int
}
```

### 2. Use the Interactive CLI

The easiest way to generate GraphQL files is using the interactive CLI:

```bash
npx prisma-gql-cli
```

Select your model, choose which operations to generate (queries, mutations, SDL), and the tool will guide you through the process.

### 3. Generate GraphQL Files

After using the CLI tool, run Prisma generate:

```bash
npx prisma generate
```

This will generate:

- `./generated/user.graphql` - GraphQL schema definitions
- `./generated/user.resolver.ts` - TypeScript resolvers

## 🎯 Interactive CLI

For a more user-friendly experience, use the interactive CLI tool that guides you through the generation process:

### Installation

```bash
# Install as a dev dependency
npm install -D prisma-graphql-module-generator
# or
pnpm add -D prisma-graphql-module-generator
```

### Usage

#### Interactive Mode

Launch the interactive CLI to be guided through module generation:

```bash
npx prisma-gql-cli
```

This will present you with options to:

- 🆕 **Create new GraphQL module** - Full configuration with all options
- 📋 **Use saved preset** - Quickly reuse previously saved configurations
- ⚡ **Quick generate** - Generate basic CRUD operations with minimal input

#### Command Line Mode

Use presets or flags directly from the command line:

```bash
# List all available presets
npx prisma-gql-cli --list

# Show help
npx prisma-gql-cli --help

# Use a specific preset
npx prisma-gql-cli my-preset-name
```

### Interactive Features

**Model Selection**

- Automatically detects Prisma models from your schema
- Allows manual input if schema is not accessible

**Operation Selection**

- Choose which components to generate:
  - SDL (Schema Definition Language)
  - Queries (findUnique, findMany, count, aggregate, groupBy)
  - Mutations (create, update, delete, createMany, updateMany, deleteMany, upsert)

**Existing File Detection**

- Scans for existing GraphQL modules
- Warns you about potential overwrites
- Smart defaults based on existing files

**Preset Management**

- Save your configurations as reusable presets
- List all saved presets with `--list`
- Execute presets directly by name

### Example Workflow

```bash
# Run the interactive CLI
npx prisma-gql-cli

# Select "Create new GraphQL module"
# Choose your model (e.g., "User")
# Select SDL, Queries, and Mutations
# Choose specific queries: findUnique, findMany
# Choose specific mutations: create, update, delete
# Save as preset: "user-basic-crud"

# Later, reuse the preset
npx prisma-gql-cli user-basic-crud
```

### Quick Generate

For rapid prototyping, use the quick generate option:

```bash
npx prisma-gql-cli
# Select "Quick generate (basic CRUD)"
# Choose your model
# Done! Generates SDL + findUnique/findMany queries + create/update/delete mutations
```

## 📚 Configuration

### Configuration Methods

The generator supports multiple configuration methods (in order of priority):

1. **JSON Configuration Files** (highest priority)
2. **TypeScript Configuration Files** (for advanced use)
3. **Default Values** (lowest priority)

### JSON Configuration

Create a `generator.config.json` file in your project root:

```json
{
  "generator": {
    "prettyName": "Prisma GraphQL Generator",
    "defaultOutput": "../generated"
  },
  "files": {
    "extensions": {
      "graphql": ".graphql",
      "resolver": ".resolver.ts"
    },
    "templates": {
      "graphqlTemplate": "templates/handlebars/module.graphql.hbs",
      "resolverTemplate": "templates/handlebars/module.resolver.ts.hbs"
    }
  },
  "content": {
    "resolverImplementation": {
      "dataSourceMethod": "context.dataSources.prisma()",
      "errorMessageTemplate": "{operationName} resolver not implemented"
    }
  },
  "typeMappings": {
    "prismaToGraphQL": {
      "Int": "Int",
      "String": "String",
      "Boolean": "Boolean",
      "DateTime": "DateTime",
      "Json": "JSON"
    }
  },
  "plugins": [
    {
      "name": "logging",
      "config": {}
    },
    {
      "name": "formatting",
      "config": {}
    }
  ]
}
```

## 🎨 Templates

### GraphQL Schema Template

The generator uses Handlebars templates for flexible output generation:

```handlebars
{{#if hasInputTypes}}
  {{#each inputTypes}}
    input
    {{name}}
    {
    {{#each fields}}
      {{name}}:
      {{type}}{{#if required}}!{{/if}}
    {{/each}}
    }

  {{/each}}
{{/if}}

{{#if hasOutputTypes}}
  {{#each outputTypes}}
    type
    {{name}}
    {
    {{#each fields}}
      {{name}}:
      {{type}}{{#if required}}!{{/if}}
    {{/each}}
    }

  {{/each}}
{{/if}}

{{#if hasQueries}}
  extend type Query {
  {{#each queries}}
    {{name}}{{#if args}}({{args}}){{/if}}:
    {{returnType}}
  {{/each}}
  }
{{/if}}

{{#if hasMutations}}
  extend type Mutation {
  {{#each mutations}}
    {{name}}{{#if args}}({{args}}){{/if}}:
    {{returnType}}
  {{/each}}
  }
{{/if}}
```

### Resolver Template

```handlebars
import { {{pascalCase modelName}}Module } from "./module-types"

export const resolvers{{pascalCase modelName}}: {{pascalCase modelName}}Module.Resolvers = {
{{#if hasQueries}}
  Query: {
{{#each queries}}
    {{name}}: async (_parent, args, context) => {
      {{#if (eq operationType "findUnique")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.findUnique({
        where: args.where,
      });
      {{else if (eq operationType "findFirst")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.findFirst({
        where: args.where,
      });
      {{else if (eq operationType "findMany")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.findMany({
        where: args.where,
        orderBy: args.orderBy,
        take: args.take,
        skip: args.skip,
      });
      {{else if (eq operationType "count")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.count({
        where: args.where,
      });
      {{else if (eq operationType "aggregate")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.aggregate({
        where: args.where,
        _count: args._count,
        _avg: args._avg,
        _sum: args._sum,
        _min: args._min,
        _max: args._max,
      });
      {{else if (eq operationType "groupBy")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.groupBy({
        by: args.by,
        where: args.where,
        having: args.having,
        orderBy: args.orderBy,
        take: args.take,
        skip: args.skip,
      });
      {{else}}
      throw new Error('{{../errorMessageTemplate}}'.replace('{operationName}', '{{name}}'));
      {{/if}}
    },
{{/each}}
  },
{{/if}}
{{#if hasMutations}}
  Mutation: {
{{#each mutations}}
    {{name}}: async (_parent, args, context) => {
      {{#if (eq operationType "create")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.create({
        data: args.data,
      });
      {{else if (eq operationType "createMany")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.createMany({
        data: args.data,
        skipDuplicates: args.skipDuplicates,
      });
      {{else if (eq operationType "update")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.update({
        where: args.where,
        data: args.data,
      });
      {{else if (eq operationType "updateMany")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.updateMany({
        where: args.where,
        data: args.data,
      });
      {{else if (eq operationType "upsert")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.upsert({
        where: args.where,
        create: args.create,
        update: args.update,
      });
      {{else if (eq operationType "delete")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.delete({
        where: args.where,
      });
      {{else if (eq operationType "deleteMany")}}
      return {{../dataSourceMethod}}.{{../modelNameLower}}.deleteMany({
        where: args.where,
      });
      {{else}}
      throw new Error('{{../errorMessageTemplate}}'.replace('{operationName}', '{{name}}'));
      {{/if}}
    },
{{/each}}
  },
{{/if}}
};
```

## 🔌 Plugin System

The generator features a plugin system for extending functionality.

### Built-in Plugins

- **Logging Plugin**: Provides detailed generation logs
- **Formatting Plugin**: Automatically formats generated code
- **Validation Plugin**: Validates generated schemas
- **Field Transform Plugin**: Transforms field names and types

### Creating Custom Plugins

Plugins can be configured in your `generator.config.json` file. For advanced use cases, you can create custom plugins by implementing the plugin interface defined in `src/plugins/types.ts`.

**Example plugin configuration:**

```json
{
  "plugins": [
    {
      "name": "logging",
      "config": {
        "level": "debug"
      }
    },
    {
      "name": "formatting",
      "config": {
        "prettier": true
      }
    }
  ]
}
```

## 🛠️ Development

### Prerequisites

- Node.js >= 18.0
- pnpm (recommended) or npm/yarn

### Setup

```bash
# Clone the repository
git clone https://github.com/mhoppehh/prisma-graphql-module-generator.git
cd prisma-graphql-module-generator

# Install dependencies
pnpm install

# Build the project
pnpm build

# Run tests
pnpm test

# Start development mode
pnpm dev
```

### Project Structure

```
prisma-graphql-module-generator/
├── src/
│   ├── bin.ts                 # Prisma generator entry point
│   ├── bin-cli.ts             # Interactive CLI entry point
│   ├── cli.ts                 # Interactive CLI implementation
│   ├── generator.ts           # Main generator logic
│   ├── helpers.ts             # Helper functions
│   ├── constants.ts           # Constants and defaults
│   ├── optionsLoader.ts       # Options loading utilities
│   ├── config/                # Configuration system
│   │   ├── config.ts         # Configuration loader
│   │   ├── config.default.ts # Default configuration
│   │   └── config.utils.ts   # Configuration utilities
│   ├── plugins/               # Plugin system
│   │   ├── manager.ts        # Plugin manager
│   │   ├── loader.ts         # Plugin loader
│   │   └── builtin/          # Built-in plugins
│   ├── templates/             # Handlebars templates
│   │   └── handlebars/
│   ├── types/                 # TypeScript type definitions
│   ├── utils/                 # Utility functions
│   └── tests/                 # Test files
├── docs/                      # Documentation
└── prisma/                    # Example Prisma schema
    └── presets.json          # Saved CLI presets
```

### Available Scripts

- `pnpm build` - Build the TypeScript project
- `pnpm dev` - Start development mode with watch
- `pnpm test` - Run the test suite
- `pnpm test:watch` - Run tests in watch mode
- `pnpm test:coverage` - Run tests with coverage report
- `pnpm start` - Run the compiled generator
- `pnpm type-check` - Run TypeScript type checking
- `pnpm lint` - Lint the codebase
- `pnpm lint:fix` - Lint and automatically fix issues
- `pnpm format` - Format code with Prettier
- `pnpm format:check` - Check code formatting

## 🧪 Testing

The project includes comprehensive tests using Jest:

```bash
# Run all tests
pnpm test

# Run tests in watch mode
pnpm test --watch

# Run tests with coverage
pnpm test --coverage
```

Test categories:

- **Unit Tests**: Individual function and component testing
- **Integration Tests**: End-to-end generation testing
- **Cartesian Tests**: Comprehensive scenario testing
- **Snapshot Tests**: Output verification testing

## 📖 Examples

### Basic Usage with CLI

```bash
# Run the interactive CLI
npx prisma-gql-cli

# Select your model (e.g., Post)
# Choose operations: SDL, Queries, Mutations
# Select specific queries: findUnique, findMany
# Select specific mutations: create, update, delete

# After CLI configuration, generate the files
npx prisma generate
```

### Using Saved Presets

```bash
# List available presets
npx prisma-gql-cli --list

# Use a specific preset
npx prisma-gql-cli my-preset-name
```

### Advanced Configuration

**Note:** For advanced customization, you can create a `generator.config.json` file or TypeScript configuration in your project root.

**JSON Configuration Example:**

```json
{
  "generator": {
    "prettyName": "My Blog GraphQL Generator",
    "defaultOutput": "./src/generated"
  },
  "files": {
    "extensions": {
      "graphql": ".gql",
      "resolver": ".resolvers.ts"
    }
  },
  "content": {
    "resolverImplementation": {
      "dataSourceMethod": "context.dataSources.prisma()",
      "errorMessageTemplate": "{operationName} resolver not implemented"
    }
  },
  "plugins": [
    { "name": "logging", "config": {} },
    { "name": "formatting", "config": {} }
  ]
}
```

**TypeScript Configuration Example:**

For advanced TypeScript-based configuration, you can reference the configuration interfaces in the source code.

```typescript
// generator.config.ts
const config = {
  generator: {
    prettyName: 'My Blog GraphQL Generator',
    defaultOutput: './src/generated',
  },
  files: {
    extensions: {
      graphql: '.gql',
      resolver: '.resolvers.ts',
    },
  },
  content: {
    resolverImplementation: {
      dataSourceMethod: 'context.dataSources.prisma()',
      errorMessageTemplate: '{operationName} resolver not implemented',
    },
  },
  plugins: [
    { name: 'logging', config: {} },
    { name: 'formatting', config: {} },
  ],
}

export default config
```

## 📊 Performance & Benchmarks

The generator has been optimized for performance and can handle large schemas efficiently:

- **Small projects** (1-10 models): ~100ms generation time
- **Medium projects** (10-50 models): ~500ms generation time
- **Large projects** (50+ models): ~2s generation time

## 🔧 Troubleshooting

### Common Issues

**Problem**: Generator not found

```bash
Error: Generator "graphql_typedef" not found
```

**Solution**: Make sure the package is installed and the provider name matches exactly.

**Problem**: TypeScript compilation errors
**Solution**: Ensure your TypeScript version is compatible (>=5.0) and check your `tsconfig.json` configuration.

**Problem**: Custom templates not loading
**Solution**: Verify the template paths are correct and the files exist in your project structure.

### Debug Mode

Enable debug logging:

```json
{
  "plugins": [
    {
      "name": "logging",
      "config": {}
    }
  ]
}
```

## 📈 Roadmap

- [ ] Support for GraphQL Federation
- [ ] Enhanced subscription support
- [ ] Real-time schema updates
- [ ] Visual schema explorer
- [ ] Performance monitoring dashboard
- [ ] Custom scalar type support
- [ ] Advanced caching strategies

## 🤝 Contributing

We welcome contributions! Please follow these steps:

### Development Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/mhoppehh/prisma-graphql-module-generator.git
   cd prisma-graphql-module-generator
   ```

2. **Install dependencies**

   ```bash
   pnpm install
   ```

3. **Run tests**

   ```bash
   pnpm test
   ```

4. **Build the project**
   ```bash
   pnpm build
   ```

### Development Workflow

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-new-feature`
3. Make your changes and add tests
4. Run the test suite: `pnpm test`
5. Run linting: `pnpm lint`
6. Format code: `pnpm format`
7. Commit your changes: `git commit -am 'feat: add some feature'`
8. Push to the branch: `git push origin feature/my-new-feature`
9. Submit a pull request

### Commit Convention

We use [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` new features
- `fix:` bug fixes
- `docs:` documentation changes
- `test:` test-related changes
- `chore:` maintenance tasks

## 📋 Changelog

See [CHANGELOG.md](CHANGELOG.md) for a detailed history of changes.

## 🆘 Support & Resources

- 📖 [Configuration Documentation](./docs/CONFIGURATION.md)
- 🐛 [Issue Tracker](https://github.com/mhoppehh/prisma-graphql-module-generator/issues)
- 💬 [Discussions](https://github.com/mhoppehh/prisma-graphql-module-generator/discussions)

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Prisma](https://prisma.io) for the excellent database toolkit
- [GraphQL](https://graphql.org) for the query language and runtime
- [Handlebars](https://handlebarsjs.com) for the templating system
- All contributors who have helped improve this project

---

<div align="center">
  <p>Made with ❤️ by the Prisma GraphQL Generator team</p>
  <p>
    <a href="https://github.com/mhoppehh/prisma-graphql-module-generator">GitHub</a> •
    <a href="https://npmjs.com/package/prisma-graphql-module-generator">NPM</a> •
    <a href="CHANGELOG.md">Changelog</a>
  </p>
</div>
