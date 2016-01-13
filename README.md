# V8-Crankshaft-bailout
A list of Crankshaft bailout reasons with examples


### Why

Late last year I wrote a couple of [blog](https://vhf.github.io/blog/) posts about optimizing [Babel](https://babeljs.io) ES2015 to ES5 transpilation for V8 and [fixed](https://github.com/babel/babel/commits?author=vhf) a few issues I had with it.

I'm far from being a V8 expert but I enjoy trying to understand how things work, and since I'm a JavaScript enthousiast I decided to take a stab at listing Crankshaft bailout reasons (together with examples and advices) in a public git repository where everyone could contribute.


## Index
### [Bailout reasons](#bailout-reasons)

* [Assignment to parameter in arguments object](#assignment-to-parameter-in-arguments-object)
* [Bad value context for arguments value](#bad-value-context-for-arguments-value)
* [Unsupported phi use of arguments](#unsupported-phi-use-of-arguments)

### [Misc](#misc)

* [Resources](#resources)
* [Template](#template)

## Bailout reasons
### Assignment to parameter in arguments object

* Simple reproduction(s)

```js
...
```

* Why

* Advices

* External examples

[mentionned here, need more research... http://vhf.github.io/blog/2015/11/02/javascript-performance-with-babel-and-node-js/]


### Bad value context for arguments value

* Simple reproduction(s)

```js
...
```

* Why

* Advices

* External examples
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


## Misc
### Resources

- [I-want-to-optimize-my-JS-application-on-V8 checklist](http://mrale.ph/blog/2011/12/18/v8-optimization-checklist.html)
- [Optimization killers](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
- [Performance Tips for JavaScript in V8](http://www.html5rocks.com/en/tutorials/speed/v8/)


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
