---
layout: post
title:  "Stylesheet"
permalink: /stylesheet
---

Stylesheet, as the name suggests, is a sheet with its own style. In the traditional front-end development, a stylesheet is a set of CSS rules used to control the layout and design of a webpage or document. It can be placed either in a <code class="language-plaintext highlighter-rouge">&lt;style></code> element or inside a seperate .css file. Similarly, in our software, a stylesheet is a layer that contains many different shapes, each with its own style. Styles here are the different configurations of member variables that correspond to each shape object class. 

The application of the stylesheet has the following advantages:

<ol>
	<li>Convenience and efficiency</li>
	After the styles of the shapes have been configured in the <code class="language-plaintext highlighter-rouge">stylesheet</code>, when users want to draw the corresponding shape, they only need to clone the corresponding shape from it, instead of using the <code class="language-plaintext highlighter-rouge">constructor</code> to instantiate an object and configure all the member variables for this object. This realizes "Configure Once, Call 100 times", making the drawing process more convenient and efficient.
	<li>Good user interface interaction</li>
	Same with the regular drawing process, when users style each shape in the <code class="language-plaintext highlighter-rouge">stylesheet</code>, they draw the default style of each shape on the sheet, and then complete the configuration of the shape style by clicking the button on the property bar. After the configuration is complete, they simply save the drawing(serialization) and name it according to the specified rules. This is the process of creating a <code class="language-plaintext highlighter-rouge">stylesheet</code>. In this process, the user is able to visualize the style of the shapes, rather than instantiating each shape by using code line by line.
	<li>Security</li>
	Drawing software needs to provide APIs to meet the user's customization needs. In the API, it is more secure to draw shapes using <code class="language-plaintext highlighter-rouge">clone()</code> method from a <code class="language-plaintext highlighter-rouge">stylesheet</code>. This prevents the source code of the shape class from being found by hackers and copyright violation.
</ol>

Stylesheet is a separate class. Combined with the above design ideas, the following parts should be included in the style single class:

load(): 
The <code class="language-plaintext highlighter-rouge">load()</code> method is the core method. In the load method, we read the shapes in the <code class="language-plaintext highlighter-rouge">stylesheet</code> and save them as a hash table. The key in the hash table is the Type value of each shape, and the value is the shape object. Saving them in form of a hash table can improve the efficiency of finding complex shape objects. Note that the <code class="language-plaintext highlighter-rouge">load()</code> method is a static method, because the <code class="language-plaintext highlighter-rouge">stylesheet</code> itself is a tool, and the <code class="language-plaintext highlighter-rouge">load()</code> method belongs to the class, instead of the instantiated object.
```js
static _instance = null;
#_map = new map();
static getInstance(){
	return this._instance
}
static async load(name){
	this._instance = new StyleSheet()
	let context = Serializer.deserialize("stylesheet-"+name)	//deserialize the local stylesheet-name file
	let allShapes = await getObject(context)	//get all shapes array
	for(let i = 0; i < allShapes.length; i++){
		let shape = allShapes[i]
		let t = shape.Type
		if(!t){
			this._instance.#_map.set(t, shape)
		}
	}
}
```
clone(): 
The <code class="language-plaintext highlighter-rouge">clone()</code> method is called externally to clone the corresponding shape, and the cloning should be based on the <code class="language-plaintext highlighter-rouge">Type</code> of the shape. In a <code class="language-plaintext highlighter-rouge">stylesheet</code>, the <code class="language-plaintext highlighter-rouge">Type</code> of each shape needs to be set manually and is unique.
```js
clone(type){
	let shape = this.map.get(type)
	return shape? shape.clone():null
}
```

So, the full pseudo code of the <code class="language-plaintext highlighter-rouge">stylesheet</code> class is as follows:
```js
//pseudo code
import packages from sources
export class StyleSheet{
	static _instance = null;
	#_map = new map();		//private

	static getInstance(){
		return this._instance
	}

	getMap(){
		return this.#_map
	}

	static async load(name){
		this._instance = new StyleSheet()
		let context = Serializer.deserialize("stylesheet-"+name)	//deserialize the local stylesheet-name file
		let allShapes = await getObject(context)	//get all shapes array
		for(let i = 0; i < allShapes.length; i++){
			let shape = allShapes[i]
			let t = shape.Type
			if(!t){
				this._instance.#_map.set(t, shape)
			}
	}

	clone(type){
		let shape = this.map.get(type)
		return shape? shape.clone():null
	}

}
```


The pseudo code of the API interface to clone the shape in the <code class="language-plaintext highlighter-rouge">stylesheet</code> for drawing is as follows:

```js
//baseApp.js
drawShape(name, type, data)	
//name: file name stylesheet-${name}   type: type of the shape you want to clone in stylesheet-${name}
{
	StyleSheet.load(name)
	let clonedShape = StyleSheet.getInstance().clone()
	//Stylesheet can only be used to clone styles. For some charts, users need to import the data.
	clonedShape.setData()

	//After cloning, you can execute the follows if you want to fine-tune the style:
	//clonedShape.setHeight()
	//clonedShape.setWidth()
	//clonedShape.setColor()

	//set a layer for showing the shape
	let layer = this.addLayer()
	layer.addChild(clonedShape)
	this.update()
}
```
