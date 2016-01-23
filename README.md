## funky-jscad

[![GitHub version](https://badge.fury.io/gh/GreenBotics%2Ffunky-jscad.svg)](https://badge.fury.io/gh/GreenBotics%2Ffunky-jscad)

A set of helpers + tools to create *modular*, *parametric* 3d models using :
 - [OpenJSCad](https://github.com/Spiritdude/OpenJSCAD.org) in a more functional manner
 - using es6 (es2015) syntax using [Babel](https://babeljs.io)
 - [Ramda](http://ramdajs.com/) for the functional helpers 
 - [Browserify](http://browserify.org/) + plugins to generate the final code (under the hood)

This means:
  - all the es6 features (using Babel)
  - import/export useage for a part/3d model ecosystem (see examples below) ! no more "coding in your corner"
  - all the javascript npm module ecosystem at your disposal !

##Notes:

Still early experimental stage ! Not running yet ! 

>Q : Why the weird name ?
- A : "funky" like "functional"... or something


#### Setup:

```
  npm install GreenBotics/funky-jscad
```


##Using:



```
  npm run watch
```


##Coding:


Minimal part definition:

```js

import {of, head, flatten} from 'ramda'
import {makeParams} from 'funky-jscad/utils'
import {makeWraps} from 'funky-jscad/wrappers'

export default function tubeCap(options)//cool , I can export my part definition !
{
  const { cylinder, translate, rotate, mirror} = makeWraps()//needs to be here within the part's function scope

  //defaults are either static "internal" parameters or are shadowed by any parameters passed in via options
  const DEFAULTS = {length:12, tubeDia:16}
  
  //get our resulting params
  let { length  , tubeDia } =  makeParams(options, DEFAULTS)

  //////////////////
  //parts
  const part    = cylinder({d:tubeDia, h:length, fn:64})
    .map(translate([0,0,endThickness])) //you can map, reduce etc, all basic and complex shapes
    .map(rotate([0,20,15]))

  return flatten( part ) //flatten as in "flatten array" not "flatten a 3d part into a 2d one"
}

```


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

  //defaults are either static "internal" parameters or are shadowed by any parameters passed in via options
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
