# Context of Node v14 and v16

If you have a Node.js on v14, you should know that in node v16 module.parent will be deprecated.

And our solution on Entria, is refactor all places that use module.parent, to require.main. And we do it with codemods.

This comemod will refactor

````js
if(!module.parent) {

}
````

to

````js
if(require.main === module){

}
````

# How ?

We used (https://github.com/facebook/jscodeshift)\[jscodeshift\] to codemod.

First you should add jscodeshift in your node_modules with

````npm
npm install -g jscodeshift
````

After this, you should create a archive that contain our codemod, in this case `replace-module-parent.js`.

And we will build our codemod together.

First we should create a function that is used in all files of folder that we select, and pass two arguments, `fileInfo` and `api`.

The `fileInfo` argument represents information of currently processed file, and `api` is object that exposes the `jscodeshift` library and helper functions from the runner.

````js
// replace-module-parent.js
function transform (fileInfo, api) {
  
};

module.exports = transform;
````

Now we want get jscodeshift helpers, from `api.jscodeshift` and transform our code to AST (Abstract System Types).
And you can explore more of our AST here [AST Explorer](https://astexplorer.net/#/gist/a1bb66ce659eaa0b728b75f92773cb64/fe344255725c8510a90943c62d6a58ab1a9506ac).

````js
const j = api.jscodeshift;

const root = j(fileInfo.source)
````

Now, we want find all ocurrences of `if(!module.parent)`, and replace to `if(require.main)`

````js
// finding all ocurrences of if(!module.parent)
root
  .find(j.IfStatement, {
      type : 'IfStatement',
      test : {
        type : 'UnaryExpression',
        operator : '!',
        argument : {
          type: 'MemberExpression',
          object: {
            type: 'Identifier',
            name: 'module'
          },
          property: {
            type: 'Identifier',
            name: 'parent'
        }
      }
    }
  })
  .filter((path) => {
    if (path.node.test.type !== 'UnaryExpression') {
      return false;
    }
    return true;
  })
````

Replacing all to `require.main`

````js
.forEach((path) => {
    const requireMain = j.ifStatement(
      j.binaryExpression(
        '===',
        j.memberExpression(
          j.identifier('require'),
          j.identifier('main')
        ),
        j.identifier('module')
      ),
      path.node.consequent,
      path.node.alternate
    )
    j(path).replaceWith(requireMain)
  });
  return root.toSource();
````

And on final our codemod is 

````js
function transform (fileInfo, api) {
  const j = api.jscodeshift;

  const root = j(fileInfo.source)

  root
  .find(j.IfStatement, {
      type : 'IfStatement',
      test : {
        type : 'UnaryExpression',
        operator : '!',
        argument : {
          type: 'MemberExpression',
          object: {
            type: 'Identifier',
            name: 'module'
          },
          property: {
            type: 'Identifier',
            name: 'parent'
        }
      }
    }
  })
  .filter((path) => {
    if (path.node.test.type !== 'UnaryExpression') {
      return false;
    }
    return true;
  })
  .forEach((path) => {
    const requireMain = j.ifStatement(
      j.binaryExpression(
        '===',
        j.memberExpression(
          j.identifier('require'),
          j.identifier('main')
        ),
        j.identifier('module')
      ),
      path.node.consequent,
      path.node.alternate
    )
    j(path).replaceWith(requireMain)
  });
  return root.toSource();
};

module.exports = transform;
module.exports.parser = 'ts';
````

To run this code you can use this on your terminal:

````terminal
jscodeshift -t replace-module-parent.js [inputA] [inputB] -d -p
````
