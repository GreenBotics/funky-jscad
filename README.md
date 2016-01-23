## funky-jscad

[![GitHub version](https://badge.fury.io/gh/GreenBotics%2Ffunky-jscad.svg)](https://badge.fury.io/gh/GreenBotics%2Ffunky-jscad)

A set of helpers + tools to create *modular*, *parametric* 3d models using [OpenJSCad](https://github.com/Spiritdude/OpenJSCAD.org) in a more functional manner, using es6 (es2015), [Ramda](http://ramdajs.com/)


##Notes:

Still early experimental stage ! Not running yet ! 

>Q : Why the weird name ?
- A : "funky" like "functional"... or something


#### Setup:

```
  npm install funky-jscad
```


##Using:



```
  npm run watch
```


##Coding:

Very verbose example : 


### Define a part (a super duper, tube cap !)

```js

import {of, head, flatten} from 'ramda'
import {makeParams} from 'funky-jscad/utils'
import {makeWraps} from 'funky-jscad/wrappers'

export default function tubeCap(options){
  const {
    cube, sphere, cylinder, 
    square, circle, 
    hull, chain_hull, union, difference, 
    translate, rotate, mirror} = makeWraps()//hack for now, this NEEDS to be done in the context of this function , otherwise the origin "sphere, cylinder etc are not defined"

  //defaults are either static parameters or show any parameters passed in via options
  const DEFAULTS = {
      length:12
    , wallsThickness:3
    , endThickness:2

    , tubeDia:16
    , tubeClearance:0.1
  }
  
  //this is how you defined computed paramters vs 
  function addComputedParams(options){
    let computed   = {
        tubeOd:options.tubeDia + options.wallsThickness * 2
     }
    return computed
  }

  //get our resulting params
  let {
      length
    , wallsThickness
    , endThickness

    , tubeDia
    , tubeOd
    , tubeClearance

  } =  makeParams(options, DEFAULTS, addComputedParams)

  //////////////////
  //parts
  const capHole    = cylinder({d:tubeDia+tubeClearance, h:length, fn:64})
    .map(translate([0,0,endThickness])) //you can map, reduce etc, all basic and complex shapes

  const capTube   = cylinder({d:tubeOd, h:length, fn:64})
    
  const result = union( capTube )
    .map(r=>difference(r, capHole))//using es6 "fat arrow" syntax

  return flatten( result )  
}

```

### And then use it (in your main file in this case)

```js
import tubeCap                  from './tubeCap'


function main() {
  let res = 50

  let tubeCap1 = tubeCap({tubeDia:12, length:24})

  return tubeCap1
}

//special hack for now
if(typeof module !== 'undefined'){
  module.exports = main
}
```


## LICENSE

[The MIT License (MIT)](https://github.com/GreenBotics/funky-jscad/blob/master/LICENSE)

- - -

[![experimental](http://badges.github.io/stability-badges/dist/experimental.svg)](http://github.com/badges/stability-badges)
