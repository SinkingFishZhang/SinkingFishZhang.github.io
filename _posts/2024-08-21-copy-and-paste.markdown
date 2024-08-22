---
layout: post
title:  "Implementation of cut, copy and paste of shape(s)"
permalink: /function/cut-copy-paste
---

In drawing software, copying, cutting, and pasting are common functions. Today I'd like to talk about how these three basic functions are implemented.

<h2>Copy</h2>

When we want to manipulate a shape, we first perform a copy action. As in Windows, after pressing Ctrl+C on the keyboard, the shapes do not change in any way, while in the background they are saved. Because the action is global, we store it in a member variable of the baseApp.

```js
//baseApp.js
this._copied = null
copy(){
	let shapes = this.getShapes();	//return an array of shape(s) which was(were) selected
	if(shapes.length){
		this._copied = shapes
	}
}
```

<h2>Cut</h2>

Cut can be seen as a variation of copy function. Unlike the copy function, after cutting, the related shape(s) will no longer be displayed, so we can simply save the shape(s) then discard. Wait! do you think that's all? Let's imagine that users will be sad when they want to regret cutting it, but their beloved shape has been thrown in the trash and can no longer be found. So, after cutting, we delete the shapes but save them to another functon called the this.getHistory(). This function will be highly relevant to the undo(ctrl+z). Therefore, the code for the cut is

```js
//baseApp.js
this._copied = null
this.getHistory = []
cut(){
	let shapes = this.getShapes();	//return an array of shape(s) which was(were) selected
	if(shapes.length){
		this._copied = shapes
		this.getHistory.push({cmd:"cut",shapes:shapes})
	}
}
```

<h2>Paste</h2>

The paste method needs to be executed after copying or cutting, so the paste method first verifies that this.\_copied is not null. To optimize the user experience, the pasted shape should be a clone of the original shape with a translation transformation, and the new shape should be selected, while the old shape will be deselected. However, this is not enough. We know that the new shape is cloned and translated from the old shape, so the new shape contains local transform. In practice, we consider the copied and pasted shape as a new one, and should not contain any kind of transform, otherwise the position of the shape may be biased later. Therefore, we need to set the local transform of the graph as null while keep the position of it.

```js
//baseApp.js
paste(){
	let clones = this._copied.map((node)=>{

	})
	let _trash = this.getHistory.push({cmd:"paste",shapes:clones})
	this._paint.editNode(clones)	//set new shape as selected mode

	//strat dealing with the local transform of pasted shape
	let shapes = this.getShapes()
	shapes.forEach((node)=>{
		if(!node) return
		let transform = node.getLocalTransform()
		if(transform){
			if(node instanceof LineShape){
				{
					let xArr = [], yArr = []
					for(let i = 0; i < node.length; i++){
						//compute real x and y with local transform
						let point = transform.transform(new Point(node.getPointX()[i], node.getPointY()[i]))
						xArr.push(point.x)
						yArr.push(point.y)
					}
					node.setLocalTransform(null)
					node.setCoordinates(xArr, yArr)
				}
			}
			else if(node instanceof SymbolShape && node instanceof TextShape && node instanceof PolygonShape){
				node.setBound(transform.transfrom(node.getBound().clone()))	//compute
				node.setLocalTransform(null)
			}
			//If there are other types that have special cases, add them here
			//else if(node instanceof BlablablaShape){

			//}
		}
	})

	this.copy()		//multi paste keeps shifting
}
```
