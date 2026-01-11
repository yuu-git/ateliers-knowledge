# CLAUDE.md - AI Assistant Guide for ateliers-ai-assistants

**Last Updated**: 2025-11-23
**Repository**: https://github.com/yuu-git/ateliers-ai-assistants
**Primary Language**: Japanese (日本語) with English documentation

---

## 📋 Table of Contents

- [Repository Overview](#-repository-overview)
- [Quick Start for AI Assistants](#-quick-start-for-ai-assistants)
- [Directory Structure](#-directory-structure)
- [Key Conventions](#-key-conventions)
- [Development Workflows](#-development-workflows)
- [Code Generation Guidelines](#-code-generation-guidelines)
- [Testing Standards](#-testing-standards)
- [Maintenance Procedures](#-maintenance-procedures)
- [Common Tasks](#-common-tasks)

---

## 🎯 Repository Overview

### Purpose

This repository provides **text-based guidelines and samples** for AI-assisted code generation and testing. It is **not an executable codebase** but a collection of:

- 📚 **Generation Guidelines**: Rules for AI tools to generate code, tests, and documentation
- 🎓 **Training Samples**: Example code patterns for AI tools to learn from
- 🛠️ **Workflow Scripts**: Automation for setup and updates
- 🔧 **Tool-Specific Configurations**: Settings for different AI tools

### Target AI Tools

- GitHub Copilot
- Cursor
- Cline
- Claude (you!)
- Other LLM-based coding assistants

### Primary Technology Stack

- **Language**: C# (with support for PowerShell, YAML, Lua in progress)
- **Test Framework**: xUnit
- **Design Approach**: Domain-Driven Design (DDD) compatible
- **Distribution Method**: Git Submodule

---

## 🚀 Quick Start for AI Assistants

### Understanding the Repository Type

**IMPORTANT**: This is a **text-based repository** containing guidelines, NOT executable code.

- ✅ DO: Read and apply guidelines when generating code
- ✅ DO: Reference training samples for code patterns
- ✅ DO: Follow naming conventions and testing standards
- ❌ DON'T: Expect to run code directly from this repository
- ❌ DON'T: Look for `.sln`, `.csproj`, or executable projects here

### Key Reference Files

When assisting with code generation or review, consult these files:

1. **[llms.txt](llms.txt)**: Overview and index of all guidelines
2. **[xUnit Test Guidelines](ai-generation-guidelines/by-language/csharp/test-generation/xunit.md)**: Comprehensive test generation rules
3. **[Common Patterns](ai-training-samples/codes/csharp/common-patterns.md)**: C# coding patterns
4. **[LINQ Patterns](ai-training-samples/codes/csharp/linq-patterns.md)**: LINQ best practices
5. **[Code Quality Principles](ai-generation-guidelines/fundamentals/code-quality-principles.md)**: Universal quality standards

---

## 📂 Directory Structure

```
ateliers-ai-assistants/
├─ scripts/                              # Setup & Update Automation
│  ├─ init-for-project.sh               # Initial setup (one-liner installer)
│  └─ update-ai-guidelines.sh           # Manual update script
│
├─ .github/workflows/                    # GitHub Actions
│  └─ update-ai-guidelines.yml          # Weekly auto-update (Mon 9:00 JST)
│
├─ ai-generation-guidelines/             # AI Code Generation Guidelines
│  ├─ fundamentals/                     # Language-Agnostic Principles
│  │  ├─ naming-conventions.md          # (Planned)
│  │  ├─ documentation-standards.md     # (Planned)
│  │  └─ code-quality-principles.md     # Universal quality standards
│  │
│  ├─ by-language/                      # Language-Specific Guidelines
│  │  ├─ csharp/                       # C# Guidelines ⭐ PRIMARY
│  │  │  ├─ test-generation/
│  │  │  │  └─ xunit.md               # 🔴 CRITICAL: xUnit test creation rules
│  │  │  ├─ code-review/
│  │  │  │  └─ value-object.md        # DDD Value Object review criteria
│  │  │  └─ patterns/                  # (Planned)
│  │  │
│  │  ├─ powershell/                   # (Planned)
│  │  ├─ yaml/                         # (Planned)
│  │  └─ lua/                          # (Planned)
│  │
│  ├─ by-framework/                     # Framework-Specific Guidelines
│  │  ├─ dotnet/                       # (Planned)
│  │  ├─ react/                        # (Planned)
│  │  └─ vue/                          # (Planned)
│  │
│  ├─ by-tool/                          # Tool-Specific Guidelines
│  │  ├─ git/                          # (Planned)
│  │  ├─ docker/                       # (Planned)
│  │  └─ ci-cd/                        # (Planned)
│  │
│  └─ meta/                             # Meta-Guidelines
│     └─ llms-txt/
│        └─ maintenance.md             # llms.txt maintenance procedures
│
├─ ai-training-samples/                  # AI Training Samples
│  ├─ codes/                            # Code Pattern Examples
│  │  ├─ csharp/
│  │  │  ├─ common-patterns.md         # Basic C# patterns
│  │  │  ├─ linq-patterns.md           # LINQ best practices
│  │  │  └─ datetime-extensions.md     # Complete implementation example
│  │  ├─ powershell/                   # (Planned)
│  │  ├─ yaml/                         # (Planned)
│  │  └─ lua/                          # (Planned)
│  │
│  ├─ configs/                          # Configuration Examples (Planned)
│  ├─ documents/                        # Document Examples (Planned)
│  └─ data/                             # Data Examples (Planned)
│
├─ tool-specific/                        # Tool-Specific Configurations
│  ├─ github-copilot/                  # GitHub Copilot settings
│  ├─ cursor/                          # Cursor settings
│  └─ claude/                          # Claude-specific settings
│
├─ docs/                                 # Project Documentation
│  ├─ project-info.md                  # Detailed project structure
│  └─ alternatives/                    # Alternative approaches
│
├─ README.md                             # User-facing documentation (Japanese)
├─ llms.txt                              # AI assistant index file
├─ CLAUDE.md                             # 👈 This file
├─ LICENSE.txt                           # MIT License
└─ .gitignore                            # Git ignore rules
```

### Directory Purpose Summary

| Directory | Purpose | For AI Assistants |
|-----------|---------|-------------------|
| `ai-generation-guidelines/` | Rules for generating code | **READ FIRST** when generating code |
| `ai-training-samples/` | Example code patterns | Reference for coding style |
| `scripts/` | Automation scripts | Understand setup/update workflow |
| `.github/workflows/` | CI/CD automation | Understand maintenance automation |
| `docs/` | Project documentation | Context about project structure |
| `tool-specific/` | Tool configs | Tool-specific optimizations |

---

## 🔑 Key Conventions

### C# Coding Standards

#### 1. Null Checking

**ALWAYS use `is null` pattern:**

```csharp
// ❌ BAD
if (list == null)

// ✅ GOOD
if (list is null)
```

#### 2. LINQ Patterns

**Use `Enumerable.Empty<T>()` instead of empty list:**

```csharp
// ❌ BAD
return new List<string>();

// ✅ GOOD
return Enumerable.Empty<string>();
```

**Use `Any()` instead of `Count > 0`:**

```csharp
// ❌ BAD
if (list.Count > 0)

// ✅ GOOD
if (list.Any())
```

#### 3. Test Naming Convention

**Critical**: Tests use a unique `TESTNAME_XXX_XXXXX` pattern:

```csharp
// Constant definition (XXX = major group, XXXXX = minor number)
public const string TESTNAME_001_00100 =
    nameof(StringService) + "." +
    nameof(StringService.TestMethod) + "_" +
    "期待される動作の説明";

// Test method
[Fact(DisplayName = TESTNAME_001_00100)]
public void TEST_001_00100()
{
    // Test implementation
}
```

**Numbering Rules**:
- Major number (XXX): Group by target class, increment by 1
- Minor number (XXXXX): Group by target method, increment by 100
- Format: 3 digits for major, 5 digits for minor (e.g., `001_00100`)

#### 4. Partial Classes for AI-Generated Tests

**Always use partial classes** to separate AI-generated and manual tests:

```csharp
// File: SampleClassTest.ai-gen.cs
// Line 1-2: AI generation notice
// テスト対象のクラス名.SampleClassTest.ai-gen.cs - このテストは GitHub Copilot によって自動生成されました。
// 手動によるテストの追加が必要な場合は テスト対象のクラス名.cs を作成し、partialクラスでテストを追加してください。

namespace SampleNamespace.UnitTests
{
    public partial class SampleClassTest
    {
        // AI-generated tests
    }
}
```

**Manual tests** go in a separate file without `.ai-gen.cs` suffix:

```csharp
// File: SampleClassTest.cs
namespace SampleNamespace.UnitTests
{
    public partial class SampleClassTest
    {
        // Manual tests
    }
}
```

#### 5. Namespace Convention

Test namespace = Source namespace + `.UnitTests`:

```csharp
// Source: namespace MyProject.Services
// Test:   namespace MyProject.Services.UnitTests
```

---

## 🔄 Development Workflows

### Installation Workflow

Users install this repository as a **Git submodule**:

```bash
# One-liner (recommended)
curl -fsSL https://raw.githubusercontent.com/yuu-git/ateliers-ai-assistants/master/scripts/init-for-project.sh | bash

# Manual installation
git submodule add https://github.com/yuu-git/ateliers-ai-assistants.git .submodules/ateliers-ai-assistants
git submodule update --init --recursive
cd .submodules/ateliers-ai-assistants
git checkout master
```

### Update Workflow

**Automated** (GitHub Actions):
- Runs every Monday at 9:00 AM JST
- Auto-commits submodule updates
- Workflow: `.github/workflows/update-ai-guidelines.yml`

**Manual** (Script):
```bash
./scripts/update-ai-guidelines.sh
```

**Manual** (Direct):
```bash
cd .submodules/ateliers-ai-assistants
git checkout master
git pull origin master
```

### Branch Strategy

- **`master`**: Stable version (recommended for production use)
- **`develop`**: Development version (experimental features)

**AI Assistant Note**: When referencing guidelines, always prefer `master` branch URLs.

---

## 📝 Code Generation Guidelines

### When to Generate Tests

Generate tests for:
- ✅ `public` methods
- ✅ `internal` methods (with `InternalsVisibleTo` attribute)
- ❌ `protected` methods (do NOT test)
- ❌ `private` methods (do NOT test)

### Test Coverage Goals

**Target: 100% coverage** (excluding protected/private methods)

Coverage checklist:
1. ✅ All exception cases (argument validation)
2. ✅ All boundary conditions (min, max, edge cases)
3. ✅ All return value patterns
4. ✅ String edge cases: `null`, empty, whitespace, max length
5. ✅ Numeric edge cases: 0, 1, max, max+1, overflow
6. ✅ State verification (for `void` methods)

### Test File Naming

**Pattern**: `{TargetClassName}Test.ai-gen.cs`

Examples:
- Target: `StringService.cs` → Test: `StringServiceTest.ai-gen.cs`
- Target: `MyClassTest.cs` → Test: `MyClassTest.ai-gen.cs` (don't duplicate "Test")

### Test Header Template

**ALWAYS include this header** in AI-generated test files:

```csharp
// {TargetClassName}Test.ai-gen.cs - このテストは GitHub Copilot によって自動生成されました。
// 手動によるテストの追加が必要な場合は {TargetClassName}Test.cs を作成し、partialクラスでテストを追加してください。

using Xunit;
// ... other usings ...

namespace OriginalNamespace.UnitTests
{
    public partial class {TargetClassName}Test
    {
        // Tests here
    }
}
```

---

## 🧪 Testing Standards

### xUnit Test Structure

**Complete example** from [xunit.md](ai-generation-guidelines/by-language/csharp/test-generation/xunit.md):

```csharp
public partial class StringServiceTest
{
    // Test name constants (using nameof for refactoring safety)
    public const string TESTNAME_001_00100 =
        nameof(StringService) + "." +
        nameof(StringService.TestTargetMethod1) + "_" +
        "引数がnullの場合にArgumentNullExceptionが発生すること";

    public const string TESTNAME_001_00200 =
        nameof(StringService) + "." +
        nameof(StringService.TestTargetMethod1) + "_" +
        "正常な引数で正しい結果が返ること";

    [Fact(DisplayName = TESTNAME_001_00100)]
    public void TEST_001_00100()
    {
        // Arrange
        var service = new StringService();

        // Act & Assert
        Assert.Throws<ArgumentNullException>(() =>
            service.TestTargetMethod1(null));
    }

    [Fact(DisplayName = TESTNAME_001_00200)]
    public void TEST_001_00200()
    {
        // Arrange
        var service = new StringService();
        var input = "test";

        // Act
        var result = service.TestTargetMethod1(input);

        // Assert
        Assert.Equal("expected", result);
    }
}
```

### Test Patterns

#### String Testing

Cover all cases:
- `null`
- Empty string (`""`)
- Whitespace only (half-width space, full-width space)
- Single character
- Maximum length
- Maximum length + 1

#### Numeric Testing

Cover all cases:
- Zero (0)
- One (1)
- Minimum value
- Maximum value
- Maximum value + 1
- Overflow (when possible)

#### Collection Testing

Cover all cases:
- `null` collection
- Empty collection
- Single element
- Multiple elements
- Maximum capacity

---

## 🔧 Maintenance Procedures

### When to Update llms.txt

**MUST update** when:
- ✅ Directory structure changes (add/remove/move)
- ✅ Major guidelines added or removed
- ✅ File paths change
- ✅ Repository purpose changes

**SHOULD update** when:
- 📝 New language/framework support added
- 📝 New AI tool support added
- 📝 Contact information changes

**NO update needed** when:
- ❌ Minor content fixes in existing guidelines
- ❌ Sample code improvements (no structure change)
- ❌ README clarifications (not affecting llms.txt)

### llms.txt Update Procedure

1. Edit `llms.txt`
2. Update affected sections:
   - Directory Structure
   - Core Guidelines
   - Training Samples
   - Last Updated date
3. Verify:
   - [ ] All file paths are correct
   - [ ] No broken links
   - [ ] New guidelines are listed
   - [ ] Removed guidelines are deleted
   - [ ] Date is current
4. Commit:
   ```bash
   git add llms.txt
   git commit -m "docs: update llms.txt - [description]"
   ```

See [maintenance.md](ai-generation-guidelines/meta/llms-txt/maintenance.md) for full checklist.

---

## 📋 Common Tasks

### Task 1: Generate xUnit Tests for a C# Class

**Steps**:

1. **Read the guidelines**:
   ```
   Read: ai-generation-guidelines/by-language/csharp/test-generation/xunit.md
   ```

2. **Understand the target class**:
   - Identify all `public` and `internal` methods
   - Note parameter types and return types
   - Identify edge cases and error conditions

3. **Generate test constants**:
   - Major number: Group by target class (increment by 1)
   - Minor number: Group by target method (increment by 100)
   - Use `nameof()` for class and method names

4. **Write test methods**:
   - Match constant names with method names
   - Use `[Fact(DisplayName = TESTNAME_XXX_XXXXX)]`
   - Follow Arrange-Act-Assert pattern

5. **Add file header**:
   ```csharp
   // {ClassName}Test.ai-gen.cs - このテストは GitHub Copilot によって自動生成されました。
   // 手動によるテストの追加が必要な場合は {ClassName}Test.cs を作成し、partialクラスでテストを追加してください。
   ```

6. **Verify coverage**:
   - All public/internal methods tested
   - All exception cases covered
   - All boundary conditions covered

### Task 2: Review C# Code Quality

**Steps**:

1. **Read quality principles**:
   ```
   Read: ai-generation-guidelines/fundamentals/code-quality-principles.md
   ```

2. **Check coding patterns**:
   ```
   Read: ai-training-samples/codes/csharp/common-patterns.md
   Read: ai-training-samples/codes/csharp/linq-patterns.md
   ```

3. **Verify**:
   - [ ] Null checks use `is null`
   - [ ] Empty collections use `Enumerable.Empty<T>()`
   - [ ] Collection checks use `.Any()` instead of `.Count > 0`
   - [ ] Code is readable and efficient
   - [ ] Naming is clear and consistent

### Task 3: Add New Language Support

**Steps**:

1. **Create directory structure**:
   ```
   ai-generation-guidelines/by-language/{new-language}/
     ├─ test-generation/
     ├─ code-review/
     ├─ patterns/
     └─ style-guide.md

   ai-training-samples/codes/{new-language}/
     └─ common-patterns.md
   ```

2. **Create guidelines**:
   - Test generation rules
   - Code review criteria
   - Common patterns

3. **Add training samples**:
   - Example code patterns
   - Best practices

4. **Update llms.txt**:
   - Add new sections
   - Update directory structure
   - Update "Last Updated" date

5. **Update README.md**:
   - List new language support
   - Add usage examples

### Task 4: Update Submodule in Parent Project

**Context**: When a user project uses this as a submodule.

**Steps**:

1. **Navigate to submodule**:
   ```bash
   cd .submodules/ateliers-ai-assistants
   ```

2. **Update to latest**:
   ```bash
   git checkout master
   git pull origin master
   ```

3. **Return to parent**:
   ```bash
   cd ../..
   ```

4. **Commit submodule update**:
   ```bash
   git add .submodules/ateliers-ai-assistants
   git commit -m "chore: update AI guidelines submodule"
   ```

---

## 🎓 Best Practices for AI Assistants

### General Principles

1. **Always read guidelines before generating**:
   - Don't assume standard patterns
   - This project has specific conventions
   - Guidelines override general knowledge

2. **Prioritize refactoring safety**:
   - Use `nameof()` for class/method references
   - Avoid hardcoded strings
   - Support IDE refactoring tools

3. **Maintain clear separation**:
   - AI-generated: `.ai-gen.cs` suffix
   - Manual additions: separate partial class file
   - Never mix in the same file

4. **Focus on 100% coverage**:
   - Test all public/internal methods
   - Cover all exception cases
   - Cover all boundary conditions
   - Document untestable scenarios

5. **Keep documentation in Japanese**:
   - Guidelines are primarily in Japanese
   - Test descriptions should be in Japanese
   - Comments should match original language

### Common Pitfalls to Avoid

❌ **Don't**:
- Generate tests for private/protected methods
- Use hardcoded class/method names in test constants
- Mix AI-generated and manual tests in same file
- Skip boundary condition testing
- Use `== null` instead of `is null`
- Create `new List<T>()` when returning empty collections

✅ **Do**:
- Use `nameof()` for refactoring safety
- Separate AI and manual tests with partial classes
- Test all public/internal method paths
- Use `is null` for null checks
- Return `Enumerable.Empty<T>()` for empty collections

---

## 📞 Support & Contact

### Questions About This Repository

- **GitHub**: [@yuu-git](https://github.com/yuu-git)
- **Repository**: https://github.com/yuu-git/ateliers-ai-assistants
- **Issues**: https://github.com/yuu-git/ateliers-ai-assistants/issues

### Questions About Usage

Consult these files:
- [README.md](README.md): Installation and basic usage
- [docs/project-info.md](docs/project-info.md): Detailed project structure
- [llms.txt](llms.txt): Quick reference for AI tools

---

## 📄 License

MIT License - see [LICENSE.txt](LICENSE.txt)

---

## 🔄 Document Version

- **Created**: 2025-11-23
- **Last Updated**: 2025-11-23
- **Target Revision**: Compatible with repository state as of 2025-11-15 restructure
- **Maintained By**: AI assistants (with human oversight)

---

## 🎯 Quick Reference Card

### Essential Files for AI Assistants

| Task | Reference File |
|------|---------------|
| Generate xUnit tests | [xunit.md](ai-generation-guidelines/by-language/csharp/test-generation/xunit.md) |
| C# coding patterns | [common-patterns.md](ai-training-samples/codes/csharp/common-patterns.md) |
| LINQ best practices | [linq-patterns.md](ai-training-samples/codes/csharp/linq-patterns.md) |
| Code quality review | [code-quality-principles.md](ai-generation-guidelines/fundamentals/code-quality-principles.md) |
| Update llms.txt | [maintenance.md](ai-generation-guidelines/meta/llms-txt/maintenance.md) |
| Full index | [llms.txt](llms.txt) |

### Critical Conventions

| Convention | Rule |
|------------|------|
| Null check | `if (x is null)` |
| Empty collection | `Enumerable.Empty<T>()` |
| Collection check | `.Any()` not `.Count > 0` |
| Test constant | `TESTNAME_XXX_XXXXX = nameof(...) + "." + nameof(...) + "_" + "説明"` |
| Test method | `[Fact(DisplayName = TESTNAME_XXX_XXXXX)] public void TEST_XXX_XXXXX()` |
| AI test file | `{ClassName}Test.ai-gen.cs` (partial class) |
| Manual test file | `{ClassName}Test.cs` (partial class) |
| Test namespace | `{SourceNamespace}.UnitTests` |

---

**END OF CLAUDE.MD**
