# V8 bailout reasons

A list of Crankshaft bailout reasons with examples, explanations and advices.

Unless otherwise specified, the following are Crankshaft bailouts.

[Your contribution is very welcome!](/CONTRIBUTING.md)


### What this is about

In order to keep this section short and allow people to get to the primary content of this repo faster, here is what it's all about and why you (probably) should care: [Chromium, Chrome, Node.js, V8, Crankshaft and bailout reasons](https://vhf.github.io/blog/2016/01/22/chromium-chrome-v8-crankshaft-bailout-reasons/).


## Index
### [Bailout reasons](#bailout-reasons-1)

* [Assignment to parameter in arguments object](#assignment-to-parameter-in-arguments-object)
* [Bad value context for arguments value](#bad-value-context-for-arguments-value)
* [ForInStatement with non-local each variable](#forinstatement-with-non-local-each-variable)
* [Object literal with complex property](#object-literal-with-complex-property)
* [Optimized too many times](#optimized-too-many-times)
* [Reference to a variable which requires dynamic lookup](#reference-to-a-variable-which-requires-dynamic-lookup)
* [Rest parameters](#rest-parameters)
* [Too many parameters](#too-many-parameters)
* [TryCatchStatement](#trycatchstatement)
* [TryFinallyStatement](#tryfinallystatement)
* [Unsupported phi use of arguments](#unsupported-phi-use-of-arguments)
* [Yield](#yield)

### [References](#references-1)

* [Resources](#resources)
* [All bailout reasons](#all-bailout-reasons)


## Bailout reasons
### Assignment to parameter in arguments object

Only happens if you reassign to a parameter while also mentionning `arguments` in the function. [More info](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#31-reassigning-a-defined-parameter-while-also-mentioning-arguments-in-the-body-in-sloppy-mode-only-typical-example).

* Simple reproduction(s)

```js
// sloppy mode only
function test(a) {
  if (arguments.length < 2) {
    a = 0;
  }
}
```

* Why
  * No idea. The fact that this bailout does not happen in strict mode is surprising.

* Advices
  * In the above example, you could assign `a` to a new variable.
  * You should use strict mode anyway.
  * It seems this will be optimized by TurboFan [#1][1].

* External examples


### Bad value context for arguments value

* Simple reproduction(s)

```js
// strict & sloppy modes
function test1() {
  arguments[0] = 0;
}

// strict & sloppy modes
function test2() {
  arguments.length = 0;
}

// strict & sloppy modes
function test3() {
  return arguments;
}

// strict & sloppy modes
function test4() {
  var args = [].slice.call(arguments);
}

// strict & sloppy modes
function test5() {
  var a = arguments;
  return function() {
    return a;
  };
}
```

* Why
  * It requires rematerialization of the `arguments` array.

* Advices
  * Read this: https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#3-managing-arguments
  * You could loop over `arguments` to build a new array, but it's not recommended. See [Unsupported phi use of arguments](#unsupported-phi-use-of-arguments)
  * Usages of `arguments` as shown above are very rarely legitimate.
  * [More about this bailout reason][7]
  * It seems this will be optimized by TurboFan [#1][1].

* External examples
  * https://github.com/bevry/taskgroup/issues/12
  * https://github.com/babel/babel/pull/3249


### ForInStatement with non-local each variable

* Simple reproduction(s)

```js
// strict & sloppy modes
function test1() {
  var obj = {};
  for(key in obj);
}

// strict & sloppy modes
function key() {
  return 'a';
}
function test2() {
  var obj = {};
  for(key() in obj);
}
```

* Why

* Advices
  * Only use pure (i.e. non-computed) local variable in a for...in.
  * https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#5-for-in

* External examples
  * https://github.com/mbostock/d3/pull/2686


### Object literal with complex property

* Simple reproduction(s)

```js
// strict & sloppy modes
function test() {
  return {
    __proto__: 3
  };
}
```

* Why

* Advices

* External examples


### Optimized too many times

* Simple reproduction(s)

```js
// strict & sloppy modes
// No known canonical reproduction
```

* Why
  * Optimization failed so many times that Crankshaft gave up.
  * "In reality this very often actually means a bug in V8 - there is some optimization which is too optimistic so the generated code deopts all the time." - @mraleph [#5][5]
  * [More about this bailout reason][6]

* Advices
  * "Just use IRHydra and look at the deoptimization reasons - the picture should become clear immediately." - @mraleph [#5][5]

* External examples


### Reference to a variable which requires dynamic lookup

* Simple reproduction(s)

```js
// sloppy mode only
function test() {
  with ({x:1}) {
    return x;
  }
}
```

* Why
  * "Variable lookup fails at compile time, Crankshaft needs to resort to dynamic lookup at runtime." - Yang Guo [#3][3]

* Advices
  * "Refactor to remove the dependency on runtime-information to resolve the lookup." - Paul Irish [#4][4]
  * **No bailout with TurboFan.**

* External examples


### Rest parameters

* Simple reproduction(s)

```js
// strict & sloppy modes
function test(...rest) {
  return rest[0];
}
```

* Why
  * Probably because it requires materializing the `arguments` array.

* Advices
  * Avoid rest parameters or use Babel's [transform-es2015-parameters](http://babeljs.io/docs/plugins/transform-es2015-parameters/) until TurboFan is able to optimize them [#1][1], [#2][2].

* External examples


### Too many parameters

* Simple reproduction(s)

```js
// strict & sloppy modes
function test(p1, p2, p3, ..., p65535) {
}
```

* Why
  * Setting limits.

* Advices
  * If you write functions with more than 65535 parameters, you probably don't worry about optimizing your code for V8 anyway.

* External examples
  * Obviously nobody ever did that. Hopefully nobody will ever do that. Zero google result on this bailout reason.
  * [V8 code source](https://chromium.googlesource.com/v8/v8/+/fe0fe20e8f094d5688256583abc5695243c6759d%5E%21/#F2)


### TryCatchStatement

* Simple reproduction(s)

```js
// strict & sloppy modes OR // sloppy mode only
function test() {
  return 3;
  try {} catch(e) {}
}
```

* Why
  * Try/catch makes the control flow jump virtually anywhere. It's hardly optimizable because the caught exception is potentially only known at runtime.

* Advices
  * Don't put try/catch inside computationally intensive functions.
  * You could `try { test() } catch`

* External examples


### TryFinallyStatement

* Simple reproduction(s)

```js
// strict & sloppy modes OR // sloppy mode only
function test() {
  return 3;
  try {} finally {}
}
```

* Why
  * See [TryCatchStatement](#trycatchstatement)

* Advices
  * See [TryCatchStatement](#trycatchstatement)

* External example


### Unsupported phi use of arguments

* Simple reproduction(s)

```js
// strict & sloppy modes
function test1() {
  var _arguments = arguments;
  if (0 === 0) { // anything evaluating to true, except a number or `true`
    _arguments = [0]; // Unsupported phi use of arguments
  }
}

// strict & sloppy modes
function test2() {
  var _arguments = arguments;
  for (var i = 0; i < 1; i++) {
    _arguments = [0]; // Unsupported phi use of arguments
  }
}

// strict & sloppy modes
function test3() {
  var _arguments = arguments;
  var again = true;
  while (again) {
    _arguments = [0]; // Unsupported phi use of arguments
    again = false;
  }
}
```

* Why
  * Crankshaft is unable to guess whether `_arguments` should be an object or an array. It cannot dematerialize `_arguments` and gives up.
  * [In-depth explaination](http://mrale.ph/blog/2015/11/02/crankshaft-vs-arguments-object.html)

* Advices
  * There is no good workaround except splitting your function into smaller ones that don't manipulate a copy of `arguments`.
  * Don't try to fool V8 by looping over `arguments` to create a new array out of it: "Allocating array (and hope it will get handled by some optimization pass in the V8) is a bad idea." - [@mraleph](https://github.com/mraleph) ([source](https://vhf.github.io/blog/2015/11/02/javascript-performance-with-babel-and-node-js/))
  * It seems this will be optimized by TurboFan [#1][1].

* External examples


### Yield

* Simple reproduction(s)

```js
// strict & sloppy modes
function* test() {
  yield 0;
}
```

* Why

* Advices

* External examples

---

[1]: https://chromium.googlesource.com/v8/v8/+/d3f074b23195a2426d14298dca30c4cf9183f203%5E%21/src/bailout-reason.h
[2]: https://codereview.chromium.org/1272673003
[3]: https://groups.google.com/forum/#!msg/google-chrome-developer-tools/Y0J2XQ9iiqU/H60qqZNlQa8J
[4]: https://github.com/GoogleChrome/devtools-docs/issues/53#issuecomment-37269998
[5]: https://github.com/GoogleChrome/devtools-docs/issues/53#issuecomment-140030617
[6]: https://github.com/GoogleChrome/devtools-docs/issues/53#issuecomment-145192013
[7]: https://github.com/GoogleChrome/devtools-docs/issues/53#issuecomment-147569505


## References
### Resources

- [All bailout reasons in Chromium codebase](https://code.google.com/p/chromium/codesearch#chromium/src/v8/src/bailout-reason.h)
- [Bad value context for arguments value](https://gist.github.com/Hypercubed/89808f3051101a1a97f3)
- [I-want-to-optimize-my-JS-application-on-V8 checklist](http://mrale.ph/blog/2011/12/18/v8-optimization-checklist.html)
- [JavaScript: Performance loss on incorrect arguments using](http://techblog.dorogin.com/2015/05/performance-loss-on-incorrect-arguments-using.html)
- [Optimization killers](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
- [OptimizationKillers](https://github.com/zhangchiqing/OptimizationKillers)
- [Performance Tips for JavaScript in V8](http://www.html5rocks.com/en/tutorials/speed/v8/)
- [thlorenz/v8-perf](https://github.com/thlorenz/v8-perf/blob/master/compiler.md)


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
- ~~Assignment to parameter in arguments object~~
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
- ~~ForInStatement with non-local each variable~~
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
- ~~Object literal with complex property~~
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
- ~~Optimized too many times~~
- Out of virtual registers while trying to allocate temp register
- Parse/scope error
- Possible direct call to eval
- Received invalid return address
- ~~Reference to a variable which requires dynamic lookup~~
- Reference to global lexical variable
- Reference to uninitialized variable
- Register did not match expected root
- Register was clobbered
- Remembered set pointer is in new space
- ~~Rest parameters~~
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
- ~~Too many parameters~~
- Too many parameters/locals
- Too many spill slots needed for OSR
- ToOperand IsDoubleRegister unimplemented
- ToOperand Unsupported double immediate
- ToOperand32 unsupported immediate.
- ~~TryCatchStatement~~
- ~~TryFinallyStatement~~
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
