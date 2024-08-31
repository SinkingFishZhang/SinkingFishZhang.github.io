---
layout: post
title:  "GroupShape"
permalink: /groupshape
---

Groupshape is one of the most common shapes in our drawing software. GroupShape allows users to freely combine other types of shapes into a group, making the structure of the entire drawing clearer. In addition, GroupShape still allows the user to freely select, drag and modify the properties of the grouped basic shape. If you encapsulate them in one class as a new kind of basic shape, users and shapes don't have interaction anymore. Therefore, groupshape is very important and there are a lot of tricks in it.

Same as textshape, lineshape, polygonshape etc., groupshape is a kind of shape which is displayed directly on the drawing and are all children of layer. The difference is that other shapes are usually leaf nodes without children, while children of a groupshape are these shapes. In other words, groupshape is the same as other shapes in terms of the overall structure, they are all shapes. But for a single groupshape, it's the parent of the other shapes. Let us use the first code block in the overview to explain the hierarchy of GroupShape in the overall structure

```js
// Overview of the graphics architecture with pseudo code
let baseApp = new BaseApp(params of baseApp);
let layer = new Layer(params of layer);
let textShape = new Text(params of textShape);
let symbolShape = new Symbol(params of symbolShape);
let groupshape = new Group(params of groupshape)

groupshape.addChild([symbolShape, textShape])
layer.addChild([groupShape, lineShape]);
baseApp.sheet.addChild(layer);

console.log(layer.children[0])		//groupShape
console.log(layer.children[1])		//lineShape
console.log(groupShape.children[0])		//symbolShape
console.log(groupShape.children[1])		//textShape
console.log(lineShape.children[0])		//null      lineShape is a leaf node without children.
```