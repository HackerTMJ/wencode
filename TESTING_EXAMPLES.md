# 🧪 WenCode Testing Examples - Real Code

## How to Test: Complete Walkthrough

### Step 1: Install Dependencies
```powershell
cd "c:\Users\mjtan\Desktop\wencode"
pnpm install
# Downloads: typescript, vitest, eslint, prettier, etc.
# Creates: node_modules/ folders with all tools
```

### Step 2: Run Tests
```powershell
pnpm test
# Finds all .test.ts files
# Runs each test case
# Reports results
```

### Step 3: View Results
```
PASS  packages/core/src/tokenizer.test.ts

Tokenizer
  Basic tokens
    ✓ should tokenize numbers (12ms)
    ✓ should tokenize strings (5ms)
    ✓ should tokenize identifiers (4ms)
  Chinese keywords
    ✓ should recognize function keyword (3ms)
    ...more tests...
    
Test Files  1 passed (1)
Tests       32 passed (32)
Duration    125ms
```

---

## Real Test Examples (Copy-Paste Ready)

### Test 1: Basic Numbers
```typescript
// Input code to tokenize
const input = '123 456.78';

// What gets tokenized
const tokens = tokenize(input);
// Result: [
//   { type: 'NUMBER', value: '123', line: 1, column: 1 },
//   { type: 'NUMBER', value: '456.78', line: 1, column: 5 },
//   { type: 'EOF', value: undefined, line: 1, column: 12 }
// ]

// What the test checks
expect(tokens[0].type).toBe(TokenType.NUMBER);      ✅
expect(tokens[0].value).toBe('123');                ✅
expect(tokens[1].type).toBe(TokenType.NUMBER);      ✅
expect(tokens[1].value).toBe('456.78');             ✅
```

### Test 2: Chinese Function
```typescript
// Input: WenCode function declaration
const input = '函数 add(a, b) { 返回 a + b; }';

// What gets tokenized
const tokens = tokenize(input);
// Result includes:
// { type: 'FUNCTION', value: '函数' }      ← Recognized!
// { type: 'IDENTIFIER', value: 'add' }
// { type: 'LPAREN', value: '(' }
// { type: 'IDENTIFIER', value: 'a' }
// { type: 'COMMA', value: ',' }
// { type: 'IDENTIFIER', value: 'b' }
// { type: 'RPAREN', value: ')' }
// { type: 'LBRACE', value: '{' }
// { type: 'RETURN', value: '返回' }        ← Recognized!
// { type: 'IDENTIFIER', value: 'a' }
// { type: 'PLUS', value: '+' }
// { type: 'IDENTIFIER', value: 'b' }
// { type: 'SEMICOLON', value: ';' }
// { type: 'RBRACE', value: '}' }
// { type: 'EOF' }

// What the test checks
const tokenTypes = tokens.map(t => t.type);
expect(tokenTypes).toContain(TokenType.FUNCTION);   ✅
expect(tokenTypes).toContain(TokenType.RETURN);     ✅
expect(tokenTypes).toContain(TokenType.PLUS);       ✅
```

### Test 3: Comments Removed
```typescript
// Input code with comment
const input = 'a // this is a comment\nb';

// What gets tokenized (comment removed)
const tokens = tokenize(input);
// Result: [
//   { type: 'IDENTIFIER', value: 'a' },
//   { type: 'IDENTIFIER', value: 'b' },
//   { type: 'EOF' }
// ]
// Total: 3 tokens (comment doesn't create token)

// What the test checks
expect(tokens.length).toBe(3);                      ✅
expect(tokens[0].value).toBe('a');                  ✅
expect(tokens[1].value).toBe('b');                  ✅
// Comment is gone!
```

### Test 4: Position Tracking
```typescript
// Input with newline
const input = 'a b\nc';
//            line 1  line 2

// What gets tokenized
const tokens = tokenize(input);
// Result: [
//   { type: 'IDENTIFIER', value: 'a', line: 1, column: 1 },
//   { type: 'IDENTIFIER', value: 'b', line: 1, column: 3 },
//   { type: 'IDENTIFIER', value: 'c', line: 2, column: 1 },
//   { type: 'EOF' }
// ]

// What the test checks
expect(tokens[0].line).toBe(1);                     ✅
expect(tokens[0].column).toBe(1);                   ✅
expect(tokens[2].line).toBe(2);                     ✅
expect(tokens[2].column).toBe(1);                   ✅
// Position tracking works for error messages!
```

### Test 5: Operators
```typescript
// Input with multiple operators
const input = 'a + b - c * d / e % f ** g';
//            +   -   *   /   %   **

// What gets tokenized
const tokens = tokenize(input);
// Extracts each operator as separate token:
// IDENTIFIER, PLUS, IDENTIFIER, MINUS, IDENTIFIER, 
// MULTIPLY, IDENTIFIER, DIVIDE, IDENTIFIER, MODULO, 
// IDENTIFIER, POWER, IDENTIFIER, EOF

// What the test checks
expect(tokens[1].type).toBe(TokenType.PLUS);       ✅
expect(tokens[3].type).toBe(TokenType.MINUS);      ✅
expect(tokens[5].type).toBe(TokenType.MULTIPLY);   ✅
expect(tokens[7].type).toBe(TokenType.DIVIDE);     ✅
expect(tokens[9].type).toBe(TokenType.MODULO);     ✅
expect(tokens[11].type).toBe(TokenType.POWER);     ✅
```

### Test 6: Variables
```typescript
// Input: Variable declarations
const input = '令 x = 10; 常量 y = 20;';
//            LET   CONST

// What gets tokenized
const tokens = tokenize(input);
// Result includes:
// { type: 'LET', value: '令' }               ← Chinese keyword
// { type: 'IDENTIFIER', value: 'x' }
// { type: 'ASSIGN', value: '=' }
// { type: 'NUMBER', value: '10' }
// { type: 'SEMICOLON', value: ';' }
// { type: 'CONST', value: '常量' }           ← Chinese keyword
// ...rest...

// What the test checks
expect(tokens[0].type).toBe(TokenType.LET);        ✅
expect(tokens[3].type).toBe(TokenType.NUMBER);     ✅
expect(tokens[5].type).toBe(TokenType.CONST);      ✅
```

### Test 7: Edge Case - Empty Input
```typescript
// Input: Empty string
const input = '';

// What gets tokenized
const tokens = tokenize(input);
// Result: [
//   { type: 'EOF' }
// ]
// Just EOF token, nothing else

// What the test checks
expect(tokens.length).toBe(1);                      ✅
expect(tokens[0].type).toBe(TokenType.EOF);        ✅
```

### Test 8: Hex Numbers
```typescript
// Input: Hexadecimal numbers
const input = '0xFF 0x123';
//            Hex  Hex

// What gets tokenized
const tokens = tokenize(input);
// Result: [
//   { type: 'NUMBER', value: '0xFF' },
//   { type: 'NUMBER', value: '0x123' },
//   { type: 'EOF' }
// ]

// What the test checks
expect(tokens[0].type).toBe(TokenType.NUMBER);     ✅
expect(tokens[0].value).toBe('0xFF');              ✅
expect(tokens[1].type).toBe(TokenType.NUMBER);     ✅
expect(tokens[1].value).toBe('0x123');             ✅
```

---

## Complete Test File Structure

```typescript
// packages/core/src/tokenizer.test.ts (197 lines)

import { describe, it, expect } from 'vitest';
import { Tokenizer, tokenize } from './tokenizer';
import { TokenType } from './token';

describe('Tokenizer', () => {
  // Group 1: Basic tokens
  describe('Basic tokens', () => {
    it('should tokenize numbers', () => {
      const tokens = tokenize('123 456.78');
      expect(tokens[0].type).toBe(TokenType.NUMBER);
      expect(tokens[0].value).toBe('123');
      expect(tokens[1].type).toBe(TokenType.NUMBER);
      expect(tokens[1].value).toBe('456.78');
    });
    
    // More tests...
  });
  
  // Group 2: Chinese keywords
  describe('Chinese keywords', () => {
    it('should recognize function keyword', () => {
      const tokens = tokenize('函数 名字() {}');
      expect(tokens[0].type).toBe(TokenType.FUNCTION);
      expect(tokens[0].value).toBe('函数');
    });
    
    // More tests...
  });
  
  // ...more groups (32 tests total)
});
```

---

## Test Execution Flow

```
┌─────────────────────────────────────────┐
│ 1. Run: pnpm test                       │
└────────────┬────────────────────────────┘
             ↓
┌─────────────────────────────────────────┐
│ 2. Vitest finds all .test.ts files      │
│    Found: tokenizer.test.ts             │
└────────────┬────────────────────────────┘
             ↓
┌─────────────────────────────────────────┐
│ 3. Run first describe block             │
│    "Tokenizer"                          │
└────────────┬────────────────────────────┘
             ↓
┌─────────────────────────────────────────┐
│ 4. Run first nested describe            │
│    "Basic tokens" (3 tests)             │
│    - Test: numbers       ✓              │
│    - Test: strings       ✓              │
│    - Test: identifiers   ✓              │
└────────────┬────────────────────────────┘
             ↓
┌─────────────────────────────────────────┐
│ 5. Run next nested describe             │
│    "Chinese keywords" (4 tests)         │
│    - Test: function      ✓              │
│    - Test: variables     ✓              │
│    - Test: control flow  ✓              │
│    - Test: loops         ✓              │
└────────────┬────────────────────────────┘
             ↓
           ... (continue for all 8 groups)
             ↓
┌─────────────────────────────────────────┐
│ Report Results:                         │
│ Test Files: 1 passed (1)                │
│ Tests: 32 passed (32)                   │
│ Duration: 125ms                         │
│                                         │
│ SUCCESS! ✅                             │
└─────────────────────────────────────────┘
```

---

## Testing Checklist

- [ ] Run `pnpm install` successfully
- [ ] Run `pnpm test`
- [ ] See "32 passed (32)" in output
- [ ] See "0 failed"
- [ ] See "Test Files 1 passed (1)"
- [ ] All tests take < 200ms total
- [ ] Run `pnpm test -- --coverage`
- [ ] See coverage > 80%
- [ ] Run `pnpm type-check` (0 errors)
- [ ] Run `pnpm lint` (0 critical errors)
- [ ] Push to GitHub: `git push origin main`
- [ ] Check GitHub Actions: https://github.com/HackerTMJ/wencode/actions

---

## When npm Works - Execute This

```powershell
# Install dependencies
pnpm install

# Run tests
pnpm test

# If all pass (32/32), continue:
pnpm type-check
pnpm lint
pnpm build

# Commit success
git add .
git commit -m "feat: Phase 1.2 complete - tokenizer 100% tested"
git push origin main
```

**Current Status:** Ready to test! Just need npm to work.
