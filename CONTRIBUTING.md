# Contributing to AI Interview Simulator

Thank you for your interest in contributing to the AI Interview Simulator platform! This document provides guidelines and information for contributors.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [How to Contribute](#how-to-contribute)
- [Coding Standards](#coding-standards)
- [Commit Guidelines](#commit-guidelines)
- [Pull Request Process](#pull-request-process)
- [Issue Reporting](#issue-reporting)

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive experience for everyone. We expect all contributors to:

- Use welcoming and inclusive language
- Be respectful of differing viewpoints and experiences
- Gracefully accept constructive criticism
- Focus on what is best for the community
- Show empathy towards other community members

### Unacceptable Behavior

- Harassment, trolling, or discriminatory language
- Personal attacks or political arguments
- Publishing others' private information
- Any conduct which could reasonably be considered inappropriate

## Getting Started

### Prerequisites

Before contributing, ensure you have:

- Node.js 18+ and npm
- Python 3.10+
- Docker and Docker Compose
- PostgreSQL 14+
- Redis 7+
- Git

### Fork and Clone

1. Fork the repository on GitHub
2. Clone your fork locally:
   ```bash
   git clone https://github.com/YOUR_USERNAME/Coding-Behavioral-Interview-Simulator-AI.git
   cd Coding-Behavioral-Interview-Simulator-AI
   ```
3. Add the upstream repository:
   ```bash
   git remote add upstream https://github.com/k2pitel/Coding-Behavioral-Interview-Simulator-AI.git
   ```

## Development Setup

### Initial Setup

1. Install dependencies:
   ```bash
   npm install
   cd ai_service && pip install -r requirements.txt && cd ..
   ```

2. Set up environment variables:
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

3. Start development services:
   ```bash
   docker-compose up -d postgres redis
   ```

4. Run database migrations:
   ```bash
   npm run migrate
   ```

5. Start development servers:
   ```bash
   # Terminal 1: Frontend
   npm run dev

   # Terminal 2: Backend
   npm run server:dev

   # Terminal 3: AI Service
   cd ai_service && uvicorn main:app --reload
   ```

### Development Workflow

1. Create a new branch for your feature:
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Make your changes

3. Test your changes:
   ```bash
   npm run test
   npm run lint
   ```

4. Commit your changes (see [Commit Guidelines](#commit-guidelines))

5. Push to your fork:
   ```bash
   git push origin feature/your-feature-name
   ```

6. Open a Pull Request

## How to Contribute

### Ways to Contribute

1. **Code Contributions**
   - Implement new features
   - Fix bugs
   - Improve performance
   - Refactor code

2. **Content Contributions**
   - Add coding problems
   - Create behavioral questions
   - Write documentation
   - Create tutorials

3. **Design Contributions**
   - UI/UX improvements
   - Visual assets
   - User flow optimization

4. **Testing**
   - Write unit tests
   - Write integration tests
   - Report bugs
   - Verify bug fixes

5. **Documentation**
   - Improve existing docs
   - Write tutorials
   - Create examples
   - Fix typos

### Finding Issues to Work On

- Check the [Issues](https://github.com/k2pitel/Coding-Behavioral-Interview-Simulator-AI/issues) page
- Look for issues labeled `good first issue` for beginners
- Issues labeled `help wanted` are actively seeking contributors
- Comment on the issue to express interest before starting work

## Coding Standards

### TypeScript/JavaScript

- Use TypeScript for all new code
- Follow the [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- Use ESLint and Prettier (configured in the project)
- Write meaningful variable and function names
- Add JSDoc comments for public APIs

**Example:**
```typescript
/**
 * Analyzes code submission and generates AI feedback
 * @param code - The user's code submission
 * @param problemId - The ID of the coding problem
 * @returns Promise containing AI feedback
 */
async function analyzeCode(code: string, problemId: string): Promise<Feedback> {
  // Implementation
}
```

### Python

- Follow [PEP 8](https://pep8.org/) style guide
- Use type hints for all functions
- Use Black for code formatting
- Use pylint for linting
- Write docstrings for all functions

**Example:**
```python
def analyze_star_method(response: str) -> STARAnalysis:
    """
    Analyzes a behavioral response for STAR method components.
    
    Args:
        response: The user's behavioral interview response
        
    Returns:
        STARAnalysis object containing detected components and scores
    """
    # Implementation
```

### React Components

- Use functional components with hooks
- One component per file
- Use TypeScript interfaces for props
- Extract reusable logic into custom hooks
- Keep components focused and single-purpose

**Example:**
```typescript
interface CodeEditorProps {
  initialCode: string;
  language: string;
  onChange: (code: string) => void;
}

export const CodeEditor: React.FC<CodeEditorProps> = ({
  initialCode,
  language,
  onChange,
}) => {
  // Implementation
};
```

### Testing

- Write tests for all new features
- Maintain test coverage above 80%
- Use Vitest for unit tests
- Use Playwright for E2E tests
- Follow the AAA pattern (Arrange, Act, Assert)

**Example:**
```typescript
describe('CodeAnalyzer', () => {
  it('should detect time complexity correctly', async () => {
    // Arrange
    const code = 'for (let i = 0; i < n; i++) { ... }';
    
    // Act
    const result = await analyzeComplexity(code);
    
    // Assert
    expect(result.timeComplexity).toBe('O(n)');
  });
});
```

## Commit Guidelines

### Commit Message Format

Follow the [Conventional Commits](https://www.conventionalcommits.org/) specification:

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation only changes
- `style`: Code style changes (formatting, missing semicolons, etc.)
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `perf`: Performance improvement
- `test`: Adding or updating tests
- `chore`: Changes to build process or auxiliary tools

### Examples

```
feat(coding): add Python syntax highlighting

Implemented syntax highlighting for Python code in the Monaco editor.
Includes support for Python 3.10 syntax and common libraries.

Closes #123
```

```
fix(auth): resolve JWT token expiration issue

Fixed bug where JWT tokens were not being refreshed properly,
causing users to be logged out prematurely.

Fixes #456
```

### Commit Best Practices

- Keep commits atomic (one logical change per commit)
- Write clear, descriptive commit messages
- Reference issue numbers when applicable
- Avoid committing commented-out code
- Don't commit secrets or sensitive data

## Pull Request Process

### Before Submitting

1. **Update your branch** with the latest changes from main:
   ```bash
   git fetch upstream
   git rebase upstream/main
   ```

2. **Run all tests** and ensure they pass:
   ```bash
   npm run test
   npm run lint
   npm run build
   ```

3. **Update documentation** if needed

4. **Add tests** for new features

### PR Template

When creating a PR, please include:

```markdown
## Description
Brief description of the changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
Describe the tests you ran and how to reproduce them

## Checklist
- [ ] My code follows the style guidelines
- [ ] I have performed a self-review
- [ ] I have commented my code, particularly in hard-to-understand areas
- [ ] I have made corresponding changes to the documentation
- [ ] My changes generate no new warnings
- [ ] I have added tests that prove my fix is effective or that my feature works
- [ ] New and existing unit tests pass locally with my changes

## Screenshots (if applicable)
Add screenshots to help explain your changes

## Related Issues
Closes #issue_number
```

### Review Process

1. At least one maintainer must review and approve the PR
2. All CI checks must pass
3. Address all review comments
4. Keep the PR focused and reasonably sized (< 500 lines if possible)
5. Be responsive to feedback

### After Approval

- Maintainers will merge your PR
- Your contribution will be credited in the release notes
- Thank you for your contribution! 🎉

## Issue Reporting

### Bug Reports

When reporting a bug, please include:

1. **Clear title** describing the issue
2. **Steps to reproduce** the bug
3. **Expected behavior**
4. **Actual behavior**
5. **Screenshots** (if applicable)
6. **Environment details**:
   - OS and version
   - Browser and version
   - Node.js version
   - Python version

**Template:**
```markdown
**Describe the bug**
A clear and concise description of what the bug is.

**To Reproduce**
Steps to reproduce the behavior:
1. Go to '...'
2. Click on '....'
3. Scroll down to '....'
4. See error

**Expected behavior**
A clear and concise description of what you expected to happen.

**Screenshots**
If applicable, add screenshots to help explain your problem.

**Environment:**
 - OS: [e.g. macOS 13.0]
 - Browser: [e.g. Chrome 119]
 - Version: [e.g. 1.0.0]

**Additional context**
Add any other context about the problem here.
```

### Feature Requests

When requesting a feature, please include:

1. **Problem statement**: What problem does this solve?
2. **Proposed solution**: How should it work?
3. **Alternatives considered**: Other approaches you've thought of
4. **Additional context**: Any other relevant information

## Development Tips

### Debugging

- Use the browser DevTools for frontend debugging
- Use `console.log` sparingly; prefer proper logging
- Use debugger breakpoints in VS Code
- Check the browser console for errors
- Check server logs for backend issues

### Performance

- Run performance profiling before and after changes
- Use React DevTools Profiler for React components
- Monitor database query performance
- Use caching appropriately
- Avoid premature optimization

### Security

- Never commit API keys, secrets, or credentials
- Use environment variables for sensitive data
- Validate all user inputs
- Sanitize data before database operations
- Follow OWASP guidelines

## Getting Help

If you need help:

1. Check the [documentation](docs/)
2. Search existing [issues](https://github.com/k2pitel/Coding-Behavioral-Interview-Simulator-AI/issues)
3. Ask in [discussions](https://github.com/k2pitel/Coding-Behavioral-Interview-Simulator-AI/discussions)
4. Reach out to maintainers (be patient, we're volunteers)

## Recognition

Contributors will be:

- Listed in the project README
- Mentioned in release notes
- Invited to the contributors team (active contributors)
- Given credit for their work

## License

By contributing, you agree that your contributions will be licensed under the same license as the project (MIT License).

---

Thank you for contributing to AI Interview Simulator! Your efforts help job seekers around the world prepare for their dream careers. 🚀
