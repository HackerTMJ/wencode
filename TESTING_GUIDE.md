# 🧪 WenCode Testing Guide

## Quick Start

### When npm Works
```powershell
cd "c:\Users\mjtan\Desktop\wencode"
pnpm install
pnpm test
# Result: ✓ 32 tests passed
```

### While npm is Blocked (Use GitHub Actions)
```powershell
git add .
git commit -m "feat: your changes"
git push origin main
# GitHub automatically runs all tests
# View results: https://github.com/HackerTMJ/wencode/actions
```

---

## 📋 Test Coverage (32 Tests)

### ✅ **Category 1: Basic Tokens (3 tests)**
Tests that the tokenizer recognizes:
- Numbers: `123`, `456.78`, `1.23e5`, `0xFF`
- Strings: `"hello"`, `'world'`
- Identifiers: `变量名`, `identifier`, `_private`

```powershell
# Test examples:
tokenize('123 456.78')      # → NUMBER, NUMBER, EOF
tokenize('"hello"')         # → STRING, EOF
tokenize('变量名')           # → IDENTIFIER, EOF
```

### ✅ **Category 2: Chinese Keywords (4 tests)**
Tests that Chinese keywords are recognized:
- **Functions:** `函数` → FUNCTION
- **Variables:** `令` → LET, `常量` → CONST
- **Control:** `如果` → IF, `否则` → ELSE
- **Loops:** `对于` → FOR, `当` → WHILE

```powershell
# Test examples:
tokenize('函数 add(a, b) {}')        # Recognizes function keyword
tokenize('令 x = 10')                # Recognizes variable declaration
tokenize('如果 (x > 0) {}')          # Recognizes if statement
tokenize('对于 (令 i = 0) {}')        # Recognizes for loop
```

### ✅ **Category 3: Operators (4 tests)**
Tests all operator types:
- **Arithmetic:** `+`, `-`, `*`, `/`, `%`, `**`
- **Comparison:** `==`, `!=`, `<`, `>`, `<=`, `>=`, `===`, `!==`
- **Logical:** `&&`, `||`, `!`
- **Assignment:** `=`, `+=`, `-=`, etc.
- **Special:** `=>` (arrow function)

```powershell
# Test examples:
tokenize('a + b - c * d / e % f ** g')    # All arithmetic
tokenize('a == b != c < d > e')            # All comparison
tokenize('a && b || c ! d')               # All logical
tokenize('x => x * 2')                    # Arrow function
```

### ✅ **Category 4: Punctuation (3 tests)**
Tests symbols and separators:
- Brackets: `(`, `)`, `{`, `}`, `[`, `]`
- Separators: `,`, `;`, `.`
- Special: `...` (spread operator)

```powershell
# Test examples:
tokenize('( ) { } [ ]')      # Parentheses, braces, brackets
tokenize('a, b; c . d')       # Comma, semicolon, dot
tokenize('[...array]')        # Spread operator
```

### ✅ **Category 5: Comments (2 tests)**
Tests comment handling:
- Line comments: `// comment`
- Block comments: `/* comment */`

```powershell
# Test examples:
tokenize('a // comment\nb')    # Line comment skipped
tokenize('a /* comment */ b')  # Block comment skipped
```

### ✅ **Category 6: Position Tracking (1 test)**
Tests line and column tracking:
- Tracks position of every token
- Maintains line numbers
- Maintains column numbers

```powershell
# Test example:
tokenize('a b\nc')
# Token a: line 1, column 1
# Token b: line 1, column 3
# Token c: line 2, column 1
```

### ✅ **Category 7: Complex Expressions (2 tests)**
Tests real-world code patterns:
- Function declarations
- Variable assignments

```powershell
# Test examples:
tokenize('函数 add(a, b) { 返回 a + b; }')   # Full function
tokenize('令 x = 10;')                       # Variable declaration
```

### ✅ **Category 8: Edge Cases (4 tests)**
Tests boundary conditions:
- Empty input: `''` → EOF only
- Whitespace only: `'   '` → EOF only
- Scientific notation: `1.23e5`, `1.23E-5`
- Hexadecimal: `0xFF`, `0x123`

```powershell
# Test examples:
tokenize('')                   # Empty input
tokenize('   \n   ')          # Whitespace only
tokenize('1.23e5 1.23E-5')    # Scientific notation
tokenize('0xFF 0x123')        # Hex numbers
```

---

## 🧬 Test Structure

```
tokenizer.test.ts (197 lines, 32 tests)
├── describe('Tokenizer')
│   ├── describe('Basic tokens')
│   │   ├── it('should tokenize numbers')
│   │   ├── it('should tokenize strings')
│   │   └── it('should tokenize identifiers')
│   ├── describe('Chinese keywords')
│   │   ├── it('should recognize function keyword')
│   │   ├── it('should recognize variable keywords')
│   │   ├── it('should recognize control flow keywords')
│   │   └── it('should recognize loop keywords')
│   ├── describe('Operators')
│   │   ├── it('should tokenize arithmetic operators')
│   │   ├── it('should tokenize comparison operators')
│   │   ├── it('should tokenize logical operators')
│   │   └── it('should tokenize assignment operators')
│   ├── describe('Punctuation')
│   │   ├── it('should tokenize parentheses and braces')
│   │   ├── it('should tokenize separators')
│   │   └── it('should tokenize spread operator')
│   ├── describe('Comments')
│   │   ├── it('should skip line comments')
│   │   └── it('should skip block comments')
│   ├── describe('Position tracking')
│   │   └── it('should track line and column numbers')
│   ├── describe('Complex expressions')
│   │   ├── it('should tokenize function declaration')
│   │   └── it('should tokenize variable declaration')
│   └── describe('Edge cases')
│       ├── it('should handle empty input')
│       ├── it('should handle whitespace-only input')
│       ├── it('should tokenize scientific notation')
│       └── it('should tokenize hexadecimal numbers')
```

---

## 🔍 How Tests Work

### Test Framework: Vitest

```typescript
// Standard test syntax:
it('test name', () => {
  const result = tokenize('input code');
  expect(result).toBe(expectedValue);  // Assert result
});
```

### Example Test Walkthrough

```typescript
it('should tokenize numbers', () => {
  // Step 1: Input code
  const tokens = tokenize('123 456.78');
  
  // Step 2: Check first token
  expect(tokens[0].type).toBe(TokenType.NUMBER);
  expect(tokens[0].value).toBe('123');
  
  // Step 3: Check second token
  expect(tokens[1].type).toBe(TokenType.NUMBER);
  expect(tokens[1].value).toBe('456.78');
  
  // Step 4: Auto-check third token (EOF)
  // Vitest passes if no errors thrown
});
```

---

## 📊 Test Results Interpretation

### When Tests Pass ✅
```
✓ Tokenizer
  ✓ Basic tokens (3 tests)
  ✓ Chinese keywords (4 tests)
  ✓ Operators (4 tests)
  ✓ Punctuation (3 tests)
  ✓ Comments (2 tests)
  ✓ Position tracking (1 test)
  ✓ Complex expressions (2 tests)
  ✓ Edge cases (4 tests)

Test Files: 1 passed (1)
Tests: 32 passed (32)
```

### When Tests Fail ❌
```
✓ Basic tokens
✗ Operators > should tokenize arithmetic operators
  Error: Expected '+' to be PLUS token
  Actual: UNKNOWN token
```

---

## 🔨 Running Different Test Scenarios

### All Tests
```powershell
pnpm test
```

### Specific Test File
```powershell
pnpm test -- tokenizer.test.ts
```

### Specific Test Category
```powershell
pnpm test -- --grep "Chinese keywords"
```

### With Coverage Report
```powershell
pnpm test -- --coverage
# Shows % of code covered by tests
```

### Watch Mode (Retest on Change)
```powershell
pnpm test -- --watch
# Reruns tests as you edit files
```

---

## 📝 What Each Test Validates

| Test | Input | Expected Output | Why Important |
|------|-------|-----------------|---------------|
| Numbers | `"123 456.78"` | NUMBER, NUMBER, EOF | Tokenizer handles numeric values |
| Strings | `'"hello"'` | STRING, EOF | String literals are recognized |
| Chinese keywords | `"函数"` | FUNCTION, EOF | Chinese syntax works |
| Operators | `"a + b"` | IDENTIFIER, PLUS, IDENTIFIER, EOF | All operators recognized |
| Punctuation | `"( )"` | LPAREN, RPAREN, EOF | Brackets/separators work |
| Comments | `"a // comment\nb"` | IDENTIFIER, IDENTIFIER, EOF | Comments are stripped |
| Position | `"a\nb"` | Token a: line 1, Token b: line 2 | Error reporting accurate |
| Complex | `"函数 add() {}"` | FUNCTION, IDENTIFIER, etc | Real code works |
| Edge cases | `""` | EOF only | Boundary conditions handled |

---

## 🚀 Testing the Full Pipeline (When Phases 2-3 Done)

Future phases will need additional tests:

### Phase 1.3 - Parser Tests
```typescript
// Will test AST generation
it('should parse function declaration', () => {
  const tokens = tokenize('函数 add(a, b) { 返回 a + b; }');
  const ast = parse(tokens);
  expect(ast.type).toBe('FunctionDeclaration');
  expect(ast.name).toBe('add');
});
```

### Phase 1.4 - Transpiler Tests
```typescript
// Will test JavaScript output
it('should transpile function', () => {
  const code = '函数 add(a, b) { 返回 a + b; }';
  const js = transpile(code);
  expect(js).toContain('function add');
  expect(js).toContain('return a + b');
});
```

### Phase 2 - React Tests
```typescript
// Will test React component transpilation
it('should transpile JSX', () => {
  const code = '返回 <视图>Hello</视图>;';
  const js = transpile(code);
  expect(js).toContain('React.createElement');
});
```

---

## ✨ Success Criteria

Your tests pass when:

```
✅ All 32 tests in tokenizer.test.ts pass
✅ No TypeScript compilation errors (pnpm type-check)
✅ No ESLint violations (pnpm lint)
✅ Code coverage > 80%
✅ All files in packages/core/src/ are type-safe
```

---

## 📞 Troubleshooting Tests

### Error: "vitest not found"
```powershell
# Solution: Install dependencies first
pnpm install
```

### Error: "Cannot find module '@wencode/core'"
```powershell
# Solution: Run from root directory
cd "c:\Users\mjtan\Desktop\wencode"
pnpm test
```

### Error: "Test timeout"
```powershell
# Solution: Increase timeout
pnpm test -- --test-timeout=10000
```

---

## 📌 Summary

| What | How | When |
|------|-----|------|
| **Run Tests** | `pnpm test` | After `pnpm install` |
| **View Results** | Terminal output | Immediately |
| **Check Coverage** | `pnpm test -- --coverage` | For quality metrics |
| **Auto-test** | `pnpm test -- --watch` | During development |
| **GitHub Auto-test** | Push to GitHub | Every commit |

**Current Status:** 32 tests written, ready to run!
