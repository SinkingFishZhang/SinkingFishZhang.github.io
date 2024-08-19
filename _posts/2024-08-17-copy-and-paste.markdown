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

Cut can be seen as a variation of copy function. Unlike the copy function, after cutting, the related shape(s) will no longer be displayed, so we can simply save the shape(s) then discard. Wait! do you think that's all? Let's imagine that users will be sad when they want to regret cutting it, but their beloved shape has been thrown in the trash and can no longer be found. So, after cutting, we delete the shapes but save them to another functon called the this.getHistory(). This function will be highly relevant to the <a href="./undo-redo">undo</a> function, which I'll talk about in the comming article. Therefore, the code for the cut is

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

The paste method needs to be executed after copying or cutting, so the paste method first verifies that this.\_copied is null. To optimize the user experience, the pasted shape should be a clone of the original shape with a translation transformation, and the new shape should be selected, while the old shape will be deselected. 

TODO

