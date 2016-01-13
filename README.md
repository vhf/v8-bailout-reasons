# V8 Crankshaft bailout
A list of Crankshaft bailout reasons with examples


### Why

Late last year I wrote a couple of [blog](https://vhf.github.io/blog/) posts about optimizing [Babel](https://babeljs.io) ES2015 to ES5 transpilation for V8 and [fixed](https://github.com/babel/babel/commits?author=vhf) a few issues I had with it.

I'm far from being a V8 expert but I enjoy trying to understand how things work, and since I'm a JavaScript enthousiast I decided to take a stab at listing Crankshaft bailout reasons (together with examples and advices) in a public git repository where everyone could contribute.


## Index
### [Bailout reasons](#bailout-reasons-1)

* [Bad value context for arguments value](#bad-value-context-for-arguments-value)
* [Unsupported phi use of arguments](#unsupported-phi-use-of-arguments)
* [Yield](#yield)

### [Misc](#misc-1)

* [Resources](#resources)
* [Template](#template)

## Bailout reasons
### Bad value context for arguments value

* Simple reproduction(s)

```js
function test1() {
  arguments[0] = 0;
}

function test2() {
  arguments.length = 0;
}
```

* Why

It requires rematerialization of the `arguments` array.

* Advices

You never really need to do this.

* External examples
  * https://github.com/bevry/taskgroup/issues/12
  * https://github.com/babel/babel/pull/3249


### Unsupported phi use of arguments

* Simple reproduction(s)

```js
...
```

* Why
  * Because ...
  * [In-depth explaination](http://mrale.ph/blog/2015/11/02/crankshaft-vs-arguments-object.html)

* Advices

* External examples


### Yield

* Simple reproduction(s)

```js
function* test() {
  yield 0;
}
```

* Why

* Advices

* External examples


## Misc
### Resources

- [I-want-to-optimize-my-JS-application-on-V8 checklist](http://mrale.ph/blog/2011/12/18/v8-optimization-checklist.html)
- [Optimization killers](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
- [Performance Tips for JavaScript in V8](http://www.html5rocks.com/en/tutorials/speed/v8/)
- [All bailout reasons in Chromium codebase](https://code.google.com/p/chromium/codesearch#chromium/src/v8/src/bailout-reason.h)
- [thlorenz/v8-perf](https://github.com/thlorenz/v8-perf/blob/master/compiler.md)
- [Bad value context for arguments value](https://gist.github.com/Hypercubed/89808f3051101a1a97f3)
- [JavaScript: Performance loss on incorrect arguments using](http://techblog.dorogin.com/2015/05/performance-loss-on-incorrect-arguments-using.html)


### Template

(Use the following template to add a bailout reason.)

* Simple reproduction(s)

```js
...
```

* Why

* Advices

* External examples
  - [example](http://example.com)


### All bailout reasons

- 32 bit value in register is not zero-extended
- Alignment marker expected
- Allocation is not double aligned
- API call returned invalid object
- Arguments object value in a test context
- Array boilerplate creation failed
- Array index constant value too big
- Assignment to arguments
- Assignment to let variable before initialization
- Assignment to LOOKUP variable
- Assignment to parameter in arguments object
- Assignment to parameter, function uses arguments object
- Bad value context for arguments object value
- ~~Bad value context for arguments value~~
- Bailed out due to dependency change
- Bailout was not prepared
- Both registers were smis in SelectNonSmi
- Call to a JavaScript runtime function
- Class literal
- Code generation failed
- Code object not properly patched
- Compound assignment to lookup slot
- Computed property name
- Context-allocated arguments
- Copy buffers overlap
- Could not generate +0.0
- Could not generate -0.0
- DebuggerStatement
- Declaration in catch context
- Declaration in with context
- Default NaN mode not set
- Delete with global variable
- Delete with non-global variable
- Destination of copy not aligned
- Do expression encountered
- DontDelete cells can't contain the hole
- DoPushArgument not implemented for double type
- Eliminated bounds check failed
- EmitLoadRegister: Unsupported double immediate
- eval
- Expected +0.0
- Expected alignment marker
- Expected allocation site
- Expected function object in register
- Expected HeapNumber
- Expected native context
- Expected new space object
- Expected non-identical objects
- Expected non-null context
- Expected undefined or cell in register
- Expecting alignment for CopyBytes
- Export declaration
- External string expected, but not found
- ForInStatement optimization is disabled
- ForInStatement with non-local each variable
- ForOfStatement
- Frame is expected to be aligned
- Function calls eval
- Function is being debugged
- Function with illegal redeclaration
- Generated code is too large
- Generator
- Generator failed to resume
- Global functions must have initial map
- HeapNumberMap register clobbered
- Import declaration
- Index is negative
- Index is too large
- Inlined runtime function: FastOneByteArrayJoin
- Inlining bailed out
- Input GPR is expected to have upper32 cleared
- Input string too long
- Integer32ToSmiField writing to non-smi location
- Invalid capture referenced
- Invalid ElementsKind for InternalArray or InternalPackedArray
- invalid full-codegen state
- Invalid HandleScope level
- Invalid left-hand side in assignment
- Invalid lhs in compound assignment
- Invalid lhs in count operation
- Invalid min_length
- JSGlobalObject::native_context should be a native context
- JSGlobalProxy::context() should not be null
- JSObject with fast elements map has slow elements
- Let binding re-initialization
- Live Bytes Count overflow chunk size
- LiveEdit
- Lookup variable in count operation
- Map became deprecated
- Map became unstable
- Native function literal
- Need a Smi literal here
- No cases left
- No empty arrays here in EmitFastOneByteArrayJoin
- Non-initializer assignment to const
- Non-object value
- Non-smi index
- Non-smi key in array literal
- Non-smi value
- Not enough spill slots for OSR
- Not enough virtual registers (regalloc)
- Not enough virtual registers for values
- Object found in smi-only array
- Object literal with complex property
- Offset out of range
- Operand is a smi
- Operand is a smi and not a bound function
- Operand is a smi and not a function
- Operand is a smi and not a name
- Operand is a smi and not a string
- Operand is not a bound function
- Operand is not a date
- Operand is not a function
- Operand is not a name
- Operand is not a number
- Operand is not a smi
- Operand is not a string
- Operand is not smi
- Operand not a number
- Optimization disabled by filter
- Optimization is disabled
- Optimized too many times
- Out of virtual registers while trying to allocate temp register
- Parse/scope error
- Possible direct call to eval
- Received invalid return address
- Reference to a variable which requires dynamic lookup
- Reference to global lexical variable
- Reference to uninitialized variable
- Register did not match expected root
- Register was clobbered
- Remembered set pointer is in new space
- Rest parameters
- Return address not found in frame
- Should not directly enter OSR-compiled function
- Sloppy function expects JSReceiver as receiver.
- Smi addition overflow
- Smi subtraction overflow
- Spread in array literal
- Stack access below stack pointer
- Stack frame types must match
- Super reference
- The current stack pointer is below csp
- The function_data field should be a BytecodeArray on interpreter entry
- The object is not tagged
- The object is tagged
- The source and destination are the same
- The stack pointer is not the expected value
- The stack was corrupted by MacroAssembler::Call()
- Too many parameters
- Too many parameters/locals
- Too many spill slots needed for OSR
- ToOperand IsDoubleRegister unimplemented
- ToOperand Unsupported double immediate
- ToOperand32 unsupported immediate.
- TryCatchStatement
- TryFinallyStatement
- Unaligned allocation in new space
- Unaligned cell in write barrier
- Unexpected allocation top
- Unexpected color bit pattern found
- Unexpected ElementsKind in array constructor
- Unexpected fall-through from string comparison
- Unexpected fallthrough from CharCodeAt slow case
- Unexpected fallthrough from CharFromCode slow case
- Unexpected fallthrough to CharCodeAt slow case
- Unexpected fallthrough to CharFromCode slow case
- Unexpected FPCR mode.
- Unexpected FPU stack depth after instruction
- Unexpected initial map for Array function
- Unexpected initial map for Array function (1)
- Unexpected initial map for Array function (2)
- Unexpected initial map for InternalArray function
- Unexpected level after return from api call
- Unexpected negative value
- Unexpected number of pre-allocated property fields
- Unexpected smi value
- Unexpected string type
- Unexpected type for RegExp data, FixedArray expected
- Unexpected value
- Unexpectedly returned from a throw
- Unsupported const compound assignment
- Unsupported count operation with const
- Unsupported double immediate
- Unsupported let compound assignment
- Unsupported lookup slot in declaration
- Unsupported non-primitive compare
- ~~Unsupported phi use of arguments~~~~
- Unsupported phi use of const variable
- Unsupported switch statement
- Unsupported tagged immediate
- Variable resolved to with context
- We should not have an empty lexical context
- WithStatement
- Wrong address or value passed to RecordWrite
- Wrong context passed to function
- ~~Yield~~
