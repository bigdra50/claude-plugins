# Unity Error Patterns and Solutions

This document provides common Unity error patterns and their solutions to assist in debugging.

## Compilation Errors

### CS0246: Type or namespace not found

**Pattern:**
```
error CS0246: The type or namespace name 'X' could not be found
```

**Common causes:**
- Missing using directive
- Missing assembly reference
- Typo in type name
- Type is in different assembly not referenced

**Solutions:**
1. Add appropriate `using` directive
2. Check Assembly Definition References
3. Verify type name spelling
4. Ensure required package/plugin is installed

### CS0103: Name does not exist in current context

**Pattern:**
```
error CS0103: The name 'X' does not exist in the current context
```

**Common causes:**
- Variable not declared
- Variable out of scope
- Typo in variable name
- Missing class member

**Solutions:**
1. Declare the variable
2. Check variable scope
3. Verify spelling
4. Add missing field/property

### CS1061: Type does not contain definition

**Pattern:**
```
error CS1061: 'Type' does not contain a definition for 'Method'
```

**Common causes:**
- Method name typo
- Wrong type being used
- Missing extension method using directive
- API changed in Unity version

**Solutions:**
1. Verify method name
2. Check if using correct type
3. Add using directive for extension methods
4. Check Unity documentation for API changes

### CS0029: Cannot implicitly convert type

**Pattern:**
```
error CS0029: Cannot implicitly convert type 'X' to 'Y'
```

**Common causes:**
- Type mismatch in assignment
- Incorrect return type
- Missing type cast

**Solutions:**
1. Use correct type
2. Add explicit cast if appropriate
3. Convert type properly

### Missing Assembly Reference

**Pattern:**
```
error CS0234: The type or namespace name 'X' does not exist in the namespace 'Y'
```

**Common causes:**
- Assembly Definition File not referencing required assembly
- Package not installed
- Platform-specific code without proper #if directives

**Solutions:**
1. Add assembly reference in .asmdef file
2. Install required package via Package Manager
3. Add platform-specific compilation directives

## Runtime Errors

### NullReferenceException

**Pattern:**
```
NullReferenceException: Object reference not set to an instance of an object
```

**Common causes:**
- Accessing unassigned component
- GameObject destroyed
- Serialized field not assigned in Inspector
- GetComponent returned null

**Solutions:**
1. Check null before accessing
2. Assign references in Inspector
3. Use proper initialization
4. Verify GetComponent success

### MissingReferenceException

**Pattern:**
```
MissingReferenceException: The object of type 'X' has been destroyed
```

**Common causes:**
- Accessing destroyed GameObject
- Component removed
- Scene unloaded

**Solutions:**
1. Check if object exists before accessing
2. Properly manage object lifetime
3. Clear references when objects destroyed

### IndexOutOfRangeException

**Pattern:**
```
IndexOutOfRangeException: Index was outside the bounds of the array
```

**Common causes:**
- Array access with invalid index
- List access beyond count
- Loop boundary error

**Solutions:**
1. Validate index before access
2. Check array/list length
3. Fix loop conditions

### ArgumentException

**Pattern:**
```
ArgumentException: Invalid argument passed to method
```

**Common causes:**
- Invalid parameter value
- Null passed when not allowed
- Out of range value

**Solutions:**
1. Validate arguments before method call
2. Check parameter constraints
3. Handle edge cases

## Asset Import Errors

### Script API compatibility

**Pattern:**
```
error: Assets/X.cs(Y,Z): error CS1061: Unity API 'Method' is obsolete
```

**Common causes:**
- Using deprecated Unity API
- Unity version changed

**Solutions:**
1. Check Unity upgrade guide
2. Replace with recommended alternative
3. Update to new API

### Meta file mismatch

**Pattern:**
```
warning: Meta file GUID conflict detected
```

**Common causes:**
- Duplicate asset GUIDs
- Version control merge conflict
- Manual file copying

**Solutions:**
1. Reimport asset
2. Resolve GUID conflict
3. Use proper asset duplication methods

### Shader compilation errors

**Pattern:**
```
Shader error in 'X': Parse error at line Y
```

**Common causes:**
- Shader syntax error
- Unsupported shader feature on platform
- Missing shader property

**Solutions:**
1. Fix shader syntax
2. Add platform-specific variants
3. Define required properties

## Performance Warnings

### Excessive GC allocations

**Pattern:**
```
warning: Excessive GC allocation detected in frame
```

**Common causes:**
- Allocating in Update/FixedUpdate
- String concatenation in loops
- Boxing value types

**Solutions:**
1. Cache allocations
2. Use StringBuilder
3. Avoid boxing
4. Object pooling

### Draw call batching warnings

**Pattern:**
```
warning: Mesh cannot be combined for batching
```

**Common causes:**
- Different materials
- Different shader properties
- Scale/rotation preventing batching

**Solutions:**
1. Share materials
2. Use material property blocks
3. Adjust transforms if possible

## Testing Errors

### Domain reload during test

**Pattern:**
```
error: Test failed due to domain reload
```

**Common causes:**
- Script compilation triggered during test
- Domain reload settings
- Edit Mode test causing recompilation

**Solutions:**
1. Wait for compilation before running tests
2. Adjust "Enter Play Mode Settings"
3. Avoid domain-reloading operations in tests

### Test timeout

**Pattern:**
```
error: Test exceeded timeout limit
```

**Common causes:**
- Infinite loop in test
- Async operation not completing
- Performance issue

**Solutions:**
1. Fix infinite loops
2. Ensure async operations complete
3. Increase timeout if legitimate
4. Optimize test code

## Common Resolution Strategies

### Script modification workflow
1. Edit script
2. Save file
3. Refresh assets (wait for completion)
4. Check console for errors
5. If errors exist, analyze and fix
6. Repeat until clean

### Dependency resolution
1. Identify missing dependencies from error
2. Check if package installed
3. Verify assembly references
4. Add required using directives

### Version migration
1. Check Unity upgrade guide
2. Replace obsolete APIs
3. Update deprecated patterns
4. Test thoroughly after changes

### Debugging approach
1. Read full error message and stack trace
2. Identify error type (compilation vs runtime)
3. Locate problematic file and line
4. Understand root cause
5. Apply appropriate fix
6. Verify fix resolves issue
