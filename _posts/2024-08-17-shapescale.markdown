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