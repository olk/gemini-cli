# Feature Implementation Plan: Fix Extension Custom Commands

## 📋 Todo Checklist
- [ ] Add `setExtensions` method to `Config` class.
- [ ] Modify `useSlashCommandProcessor` to load extensions.
- [ ] Update tests to reflect the changes.
- [ ] Final Review and Testing

## 🔍 Analysis & Investigation

### Codebase Structure
The relevant files for this feature are:
- `packages/cli/src/ui/hooks/slashCommandProcessor.ts`: This file contains the logic for processing slash commands and instantiating the `FileCommandLoader`.
- `packages/cli/src/services/FileCommandLoader.ts`: This file contains the logic for loading commands from the file system.
- `packages/cli/src/config/extension.ts`: This file contains the logic for loading extension configurations.
- `packages/core/src/config/config.ts`: This file contains the `Config` class.
- `docs/extension.md`: This file contains the documentation for the extensions feature.

### Current Architecture
The current architecture for loading commands is as follows:
1. The `useSlashCommandProcessor` hook is called with a `Config` object.
2. The `useEffect` hook in `useSlashCommandProcessor` creates a `CommandService` and a `FileCommandLoader`.
3. The `FileCommandLoader` is instantiated with the `Config` object.
4. The `FileCommandLoader.loadCommands()` method is called to load all commands.
5. The `loadCommands()` method loads commands from the user's command directory, the project's command directory, and from extensions.

The problem is that the `Config` object that is passed to the `FileCommandLoader` does not contain the loaded extensions. The extension loading logic is in `packages/cli/src/config/extension.ts`, but it is not being called before the `FileCommandLoader` is created.

### Dependencies & Integration Points
The `FileCommandLoader` depends on the `Config` object to get the list of active extensions. The `useSlashCommandProcessor` hook is responsible for creating the `Config` object and the `FileCommandLoader`.

### Considerations & Challenges
The main challenge is to modify the `useSlashCommandProcessor` hook to load extensions without introducing any breaking changes. The changes should be self-contained within the hook and should not require any changes to other parts of the application.

## 📝 Implementation Plan

### Prerequisites
No prerequisites are required for this implementation.

### Step-by-Step Implementation
1. **Step 1**: Add `setExtensions` method to `Config` class.
   - Files to modify: `packages/core/src/config/config.ts`
   - Changes needed:
     - Add a private field `extensions` to the `Config` class.
     - Add a public method `setExtensions(extensions: Extension[])` to the `Config` class that sets the `extensions` field.
     - The `getExtensions()` method should be updated to return the value of the `extensions` field.

2. **Step 2**: Modify `useSlashCommandProcessor` to load extensions.
   - Files to modify: `packages/cli/src/ui/hooks/slashCommandProcessor.ts`
   - Changes needed:
     - Import the `loadExtensions` function from `packages/cli/src/config/extension.ts`.
     - In the `useEffect` hook, before creating the `CommandService`, call the `loadExtensions` function to get the list of all extensions.
     - Call the `config.setExtensions()` method to store the loaded extensions in the `Config` object.

3. **Step 3**: Update tests to reflect the changes.
   - Files to modify: `packages/cli/src/ui/hooks/slashCommandProcessor.test.ts`
   - Changes needed:
     - Update the existing tests to mock the `loadExtensions` function and return a list of mock extensions.
     - Add new tests to verify that the `useSlashCommandProcessor` hook correctly loads extensions.

### Testing Strategy
The testing strategy is as follows:
- **Unit Tests**: The existing unit tests in `packages/cli/src/ui/hooks/slashCommandProcessor.test.ts` will be updated to cover the new functionality.
- **Integration Tests**: The existing integration tests for custom commands will be run to ensure that the changes have not introduced any regressions.

## 🎯 Success Criteria
The feature will be considered complete when:
- The `FileCommandLoader` correctly loads custom commands from extensions.
- The loaded commands are available in the CLI and can be executed.
- All tests pass.