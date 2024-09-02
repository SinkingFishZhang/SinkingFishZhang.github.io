---
layout: post
title:  "Sheet Scale"
permalink: /sheetscale
---

SheetScale is a scale system for our drawing software and is an important tool. sheetscale is the relationship between the millimeter size of the drawing sheet and the real size of the model. Sheetscale is independent of real-world. That means, the real-world size changes, and as long as the relationship between the size of the drawing sheet and the size of the model does not change, the sheetscale does not change.

We have three parameters in the drawing paper, they are the sheetscale, the drawing model coordinates, and the drawing sheet millimeter coordinates. Among them, the coordinates of the drawing model are only related to the imported data, and the other two parameters are inverse-proportional. The relationship between them is:

drawing model length (m) = sheetscale * drawing sheet millimeter length (mm)

SheetScale is a utility class that contains methods for length conversions, which are typically used in the render() function of shapes. Using these methods allows the shape size change as the sheetscale changes.

```js
export const Unit = {
	m: 1000,
	mm: 1
}
export class SheetScale extend Tool{
	constructor(model, scale, unit=Unit.m){
		super();
		this._model = model;
		this._sheetScale = scale;
		this._unit = unit;
	}
	clone(){
		return new SheetScale(this._model, this._sheetScale, this._unit);
	}
	sheet2model(mm){
		if(mm instanceof Dimension){
			return new Dimension(mm.getWidth()/this._unit*this._sheetScale, 
								 mm.getHeight()/this._unit*this._sheetScale)
		}
		return mm/this._unit*this._sheetScale
	}
	model2sheet(m){
		if(m instanceof Dimension){
			return new Dimension(mm.getWidth()/this._sheetScale*this._unit, 
								 mm.getHeight()/this._sheetScale*this._unit)
		}
		return mm/this._sheetScale*this._unit
	}
}
```

In my overview post, I gave an example of drawing a rectangle with 4 lines, and describe the structure of the shape object class briefly. Now we have known the concept of sheetscale, then let's go back to that example. We may find that the whole shape is independent of the sheetscale, that is, if you change the sheetscale, the shape size on the sheet does not change with it. This stands against the common sense of geological maps. To solve this problem, we need to convert the length to millimeter length. Let's rewrite this codeblock:

We first write a static method in the utility class and use recursion method to find the sheet where the shape is located

```js
    //  util/Tool.js
    static getSheet(shape) {
        if (node instanceof Sheet) {
            return node;
        } else if (node.getParent() != null) {
            this.getContentGroup(node.getParent())
        } else {
            return null;
        }
    }
```

```js

// pseudo code
import {packages} from sources
export class Rectangle extends shape{
	constructor(x, y, width, height){
		this.x = x;
		this.y = y;
		this.width = width;
		this.height = height
	}
	render(context){
		//shape.setBound(leftbottom_x,leftbottom_y, righttop_x, righttop_y)

		//*****************************************************
		//Here, the input member variables of is millimeter length, and output is the length of the model after the sheetscale transformation with the unit m
		let modelWidth = Tool.getSheet(this).getSheetScale().sheet2model(this.width)
		let modelHeight = Tool.getSheet(this).getSheetScale().sheet2model(this.Height)
		//*****************************************************

		this.setBound(x, y, x + modelWidth, y + modelHeight)
		let leftLine = new Line().setCoordinates([x, x],[y, y + modelHeight])		//setCoordinate(xArr, yArr)
		let rightLine = new Line().setCoordinates([x + modelWidth, x + modelWidth],[y, y + modelHeight])
		let topLine = new Line().setCoordinates([x, x + modelWidth],[y + modelHeight, y + modelHeight])
		let bottomLine = new Line().setCoordinates([x, x + modelWidth],[y, y])
		leftLine.render(context)
		leftLine.dispose()
		rightLine.render(context)
		rightLine.dispose()
		topLine.render(context)
		topLine.dispose()
		bottomLine.render(context)
		bottomLine.dispose()
	}
	clone(){
		let r = new Rectangle(
			this.x,
			this.y,
			this.width,
			this.height
			);
		return r
	}
	setProperties(properties){		//Deserialization
		if(!properties) return;
		super.setProperties(properties);
		(Object.prototype.hasOwnProperty.call(properties, x))&&(this.x = properties.x)
		(Object.prototype.hasOwnProperty.call(properties, y))&&(this.x = properties.x)
		(Object.prototype.hasOwnProperty.call(properties, width))&&(this.x = properties.width)
		(Object.prototype.hasOwnProperty.call(properties, height))&&(this.x = properties.height)
		return this
	}
	getProperties(){	//Serialization
		let a = super.getProperties()
		a.x = this.x
		a.y = this.y
		a.width = this.width
		a.height = this.height
		return a
	}
}
package.setClassName("Rectangle")
```

In this improved render() method, when we modify the sheetscale, the sheet2model() method is called, and the shape can change in real time with the sheetscale. The output value at the end is a multiplicative relationship, hence, the sheetscale is proportional to the shape size.