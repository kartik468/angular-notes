## Native deep cloning
It's called "structured cloning", works experimentally in Node 11 and later, and hopefully will land in browsers. See this answer for more details.

## Fast cloning with data loss - JSON.parse/stringify
If you do not use Dates, functions, undefined, Infinity, RegExps, Maps, Sets, Blobs, FileLists, ImageDatas, sparse Arrays, Typed Arrays or other complex types within your object, a very simple one liner to deep clone an object is:

JSON.parse(JSON.stringify(object))

``` javascript
const a = {
  string: 'string',
  number: 123,
  bool: false,
  nul: null,
  date: new Date(),  // stringified
  undef: undefined,  // lost
  inf: Infinity,  // forced to 'null'
  re: /.*/,  // lost
}
console.log(a);
console.log(typeof a.date);  // Date object
const clone = JSON.parse(JSON.stringify(a));
console.log(clone);
console.log(typeof clone.date);  // result of .toISOString()
```

## Reliable cloning using a library
Since cloning objects is not trivial (complex types, circular references, function etc.), most major libraries provide function to clone objects. Don't reinvent the wheel - if you're already using a library, check if it has an object cloning function. For example,

lodash - cloneDeep; can be imported separately via the lodash.clonedeep module and is probably your best choice if you're not already using a library that provides a deep cloning function
AngularJS - angular.copy
jQuery - jQuery.extend(true, { }, oldObject); .clone() only clones DOM elements

## ES6
For completeness, note that ES6 offers two shallow copy mechanisms: Object.assign() and the spread operator.
