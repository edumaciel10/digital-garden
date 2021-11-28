# Codemod

We use jscodeshift created by Facebook.

And our goal is transform the input to output like

## Input

````js
//console.input.js
function add(a,b) {
	console.log(`a+b = ${a+b}`);
	return a+b
}
````

## Output

````js
//console.output.js
function add(a,b) {
	return a+b
}
````

In this case, we remove the `console.log ` from input.

We can transform this with `jscodeshift` using this code

````js
// remove-consoles.js
export default (fileInfo, api) => {
  const j = api.jscodeshift;

  return j(fileInfo.source)
    .find(j.CallExpression, {
        callee: {
          type: 'MemberExpression',
          object: { type: 'Identifier', name: 'console' },
        },
      }
    )
    .remove()
    .toSource();
};
````

The `fileInfo` and `api` parameters, come from `jscodeshift CLI`

````js
  const j = api.jscodeshift;

  return j(fileInfo.source)
````

We get `jscodeshift` from `api` and get our all code on `fileInfo.source`, this represents code of `console.input.ts`

````js
.find(j.CallExpression, {
        callee: {
          type: 'MemberExpression',
          object: { type: 'Identifier', name: 'console' },
        },
      }
    )
````

We use `root.find` to find all sources that contains the infos of object.

In this case, `console` have `MemberExpression` type and object contains type `Indentifer`, and name `console`.

 > 
 > It is like a SELECT * FROM code WHERE code.callee.type = "MemberExpression" AND code.callee.object.type = "Identifier" AND code.callee.object.name = ''console"

````js
    .remove()
    .toSource();
````

And then we type `remove()` to remove all AST that contains a `console`, after we type `toSource();` to AST to be Code.
