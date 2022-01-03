#### 资源 VR & AR

### A-Frame

 https://aframe.io/

[A-Frame](https://aframe.io/) is a web framework for building virtual reality experiences. Make WebVR with HTML and Entity-Component. Works on Vive, Rift, desktop, mobile platforms.

While the HTML layer looks basic, HTML and the DOM are only the outermost abstraction layer of A-Frame. Underneath, A-Frame is an entity-component framework for three.js that is exposed declaratively.

A-Frame provides a handful of elements such as `<a-box>` or `<a-sky>` called *primitives* that wrap the entity-component pattern to make it appealing for beginners. 

WebVR is a JavaScript API for creating immersive 3D, virtual reality experiences in your browser. 

A-Frame uses the WebVR API to gain access to VR headset sensor data (position, orientation) to transform the camera and to render content directly to VR headsets. Note that WebVR, which provides data, should not be confused nor conflated with WebGL, which provides graphics and rendering.

For up-to-date and detailed information about WebVR, [visit `https://webvr.rocks`](https://webvr.rocks/).

#### Install:



Components are curated and collected into the [A-Frame Registry](https://aframe.io/aframe-registry) This is similar to the collection of components and modules on the Unity Asset Store, but free and open source. 

https://aframe.io/aframe-registry/, Once a component is in the Registry, the component can be easily searchable and installable from multiple channels (e.g., [angle](https://github.com/aframevr/angle/)).



#### Entities:

In A-Frame, entities are inherently attached with the [position](https://aframe.io/docs/0.5.0/components/position.html), [rotation](https://aframe.io/docs/0.5.0/components/rotation.html), and [scale](https://aframe.io/docs/0.5.0/components/scale.html)components.

**Retrieve an entity using DOM APIs**

```javascript
var el = document.querySelector('#mario'); //id
document.querySelector("p"); //<p>
//限制搜索范围在a-scene下面
var sceneEl = document.querySelector('a-scene');
console.log(sceneEl.querySelector('#redBox'));

console.log(sceneEl.querySelectorAll('[light]')); // containing an attribute
```

| [.*class*](https://www.runoob.com/cssref/sel-class.html)    | .intro     | 选择所有class="intro"的元素  | 1    |
| ----------------------------------------------------------- | ---------- | ---------------------------- | ---- |
| [#*id*](https://www.runoob.com/cssref/sel-id.html)          | #firstname | 选择所有id="firstname"的元素 | 1    |
| [*](https://www.runoob.com/cssref/sel-all.html)             | *          | 选择所有元素                 | 2    |
| *[element](https://www.runoob.com/cssref/sel-element.html)* | p          | 选择所有<p>元素              | 1    |

`<a-entity>.components` is an object of components attached to the entity. 

```javascript
var camera = document.querySelector('a-entity[camera]').components.camera.camera;
var material = document.querySelector('a-entity[material]').components.material.material;
document.querySelector('a-entity[sound]').components.sound.pause();
```

A-Frame 注册&使用 Component 的例子

```javascript
AFRAME.registerComponent('log', {
  schema: {type: 'string'},

  init: function () {
    var stringToLog = this.data;
    console.log(stringToLog);
  }
});

<a-scene log="Hello, Scene!">
  <a-box log="Hello, Box!"></a-box>
</a-scene>
```

#### Component

##### Register a Component (name, definition)

```javascript
// Registering component in foo-component.js
AFRAME.registerComponent('foo', {
  //An object that defines and describes the property or properties of the component.      
  schema: {},     
  init: function () {},
  update: function () {},
  tick: function () {},
  remove: function () {},
  pause: function () {},
  play: function () {}
});
```



```html

<!-- Usage of `foo` component. -->
<html>
  <head>
    <script src="aframe.min.js"></script>
    <script src="foo-component.js"></script>
  </head>
  <body>
    <a-scene>
      <a-entity foo></a-entity>
    </a-scene>
  </body>
</html>
```

We can also define our own property type by providing a `parse` and `stringify`function in place of a `type`, `parse` is called when component properties are updated by `setAttribute` method.`stringify` is called when DOM is updated by [flushToDom](https://aframe.io/docs/1.2.0/core/component.html#flushtodom) method.

```javascript
AFRAME.registerComponent('bar', {
  //be initialized left-to-right before initializing the current component.
  dependencies: ['a', 'b'];
  multiple: true; //the component can have multiple instances:
  schema: {
    color: {default: '#FFF'},
    size: {type: 'int', default: 5}
	update: function () {
    console.log('This component instance has the ID', this.id); //多实例时不同
  }
  }
}
```

```html
<a-scene>
  <a-entity 
            bar="color: red; size: 20"
            bar__beep="color: green; size: 20" //this.id would be beep
            bar__boop="color: black; size: 20" //this.attrName would be bar__boop
            ></a-entity>
</a-scene>
```

A single-property component’s schema contains `type` and/or `default` keys, and the schema’s values are plain values rather than objects:

```javascript
AFRAME.registerComponent('foo', {
  schema: {type: 'int', default: 5}
});
```



```html
<a-scene>
  <a-entity foo="20"></a-entity>
</a-scene>
```

##### Accessing a Component’s Members and Methods

```javascript
//假设 'foo' 是component的名字，bar 为 一个属性，qux（）为方法 
var fooComponent = document.querySelector('[foo]').components.foo;
console.log(fooComponent.bar);
fooComponent.qux();
```



##### Component-to-DOM Serialization

By default, for performance reasons, A-Frame does not update the DOM with component data. This also means mutation observers will not fire. If we open the browser’s DOM inspector, we will see only the component names (and not the values) are visible.

```html
<a-entity geometry material position rotation></a-entity>
```

A-Frame stores the component data in memory. Updating the DOM takes CPU time for converting internal component data to strings. If we want to see the DOM update for debugging purposes, we can attach the `debug` component to the scene. Components will check for an enabled `debug` component before trying to serialize to the DOM. Then we will be able to view component data in the DOM:

```html
<a-entity geometry="primitive: box" material="color: red" position="1 2 3" rotation="0 180 0"></a-entity>
```

Make sure that this component is not active in production.

###### Manually Serializing to DOM

To manually serialize to DOM, use [`Entity.flushToDOM`](https://aframe.io/docs/1.2.0/core/entity.html#flushtodom-recursive) or[`Component.flushToDOM`](https://aframe.io/docs/1.2.0/core/component.html#flushtodom):

```javascript
document.querySelector('a-entity').components.position.flushToDOM();  // Flush a component.
document.querySelector('a-entity').flushToDOM();  // Flush an entity.
document.querySelector('a-entity').flushToDOM(true);  // Flush an entity and its children.
document.querySelector('a-scene').flushToDOM(true);  // Flush every entity.
```



##### Entity加载成功与调用参数查询的时序问题

`.appendChild()` is an *asynchronous* operation in the browser. Until the entity has finished appending to the DOM, we can’t do many operations on the entity (such as calling `.getAttribute()`). If we need to query an attribute on an entity that has just been appended, we can listen to the `loaded` event on the entity, or place logic in an A-Frame component so that it is executed once it is ready:

After the entity is attached and initialized, it will initialize its components. 

```javascript
var sceneEl = document.querySelector('a-scene');

AFRAME.registerComponent('do-something-once-loaded', {
  //init: called once when the component is first plugged into its entity.
  init: function () {  
    //called after the entity has properly attached and loaded.
    console.log('I am ready!'); 
  }
});

//通过 JS DOM 使用 component
var entityEl = document.createElement('a-entity');
entityEl.setAttribute('do-something-once-loaded', '');
sceneEl.appendChild(entityEl);

```

"I am ready!" will be logged once after the scene has started running and the entity has attached.

```html
<!--借 HTML 使用 component-->
<a-scene>
  <a-entity hello-world></a-entity>
</a-scene>
```

##### 传参数定义component

How to pass data to components by defining configurable properties via the **schema**.

 The `.update()` handler is also called right after `.init()` when the component is attached. Sometimes, we have most of our logic in the `.update()`handler so we can initialize *and* handle updates all at once without repeating code.

//...事件名变化后，update里面的handler逻辑？LISA 07/12/2021

//removeEventListener 对 第二个参数的影响？

#####  multiple `log` components attached to the same entity

```javascript
AFRAME.registerComponent('log', {
  schema: {
    event: {type: 'string', default: ''},
    message: {type: 'string', default: 'Hello, World!'}
  },

  multiple: true, //设置flag

  // ...
});

//使用方式1
var el = document.querySelector('a-entity');
el.setAttribute('log__helloworld', {message: 'Hello, World!'}); //对名字进行加工 log__ID
el.setAttribute('log__metaverse', {message: 'Hello, Metaverse!'});
```



```html
<!--使用方式2-->
<a-scene>
  <a-entity log__helloworld="message: Hello, World!"
            log__metaverse="message: Hello, Metaverse!"></a-entity>
</a-scene>
```

Given `log__helloworld`, `this.id` would be `helloworld` and `this.attrName` would be the full `log__helloworld`.



##### 设置修改component

for better performance, memory, and access to utilities, we recommend modifying `position`, `rotation`, `scale`, and `visible` directly at the three.js level via the entity’s [Object3D](https://threejs.org/docs/#api/core/Object3D) rather than via `.setAttribute`:

```javascript
// Examples for position.
entityEl.object3D.position.set(1, 2, 3);
entityEl.object3D.position.x += 5;
entityEl.object3D.position.multiplyScalar(5);

// Examples for rotation.
entityEl.object3D.rotation.y = THREE.Math.degToRad(45);
entityEl.object3D.rotation.divideScalar(2);

// Examples for scale.
entityEl.object3D.scale.set(2, 2, 2);
entityEl.object3D.scale.z += 1.5;

// Examples for visible.
entityEl.object3D.visible = false;
entityEl.object3D.visible = true;
```

#####  Events and Event listeners.

an easy way for entities and components to communicate with one another.

```javascript
entityEl.emit('physicscollided', {collidingEntity: anotherEntityEl}, false); //Emit
entityEl.addEventListener('physicscollided', function (event) {        //捕获并处理
  console.log('Entity collided with', event.detail.collidingEntity);
});

// We have to define this function with a name if we later remove it.
function collisionHandler (event) {
  console.log('Entity collided with', event.detail.collidingEntity);
}

entityEl.addEventListener('physicscollided', collisionHandler);
entityEl.removeEventListener('physicscollided', collisionHandler);  //解除监听
```

//...事件的发出和接收处理都是以 实体对象 作为载体？怎么实现同样类型对象之间的通用事件？ LISA 07/12/2021

##### glTF 

(GL Transmission Format) is an open project by Khronos providing a common, extensible format for 3D assets that is both efficient and highly interoperable with modern web technologies.

The `gltf-model` component loads a 3D model using a glTF (`.gltf` or `.glb`) file.

```html
<a-scene>
  <a-assets>
    <a-asset-item id="tree" src="/path/to/tree.gltf"></a-asset-item>
  </a-assets>

  <a-entity gltf-model="#tree"></a-entity>
</a-scene>
```

#####  The  entity’s `object3DMap`

The event-set component makes basic event handlers declarative. The API for the event-set component looks like:

```html
<a-entity event-set__makevisible="_event: mouseenter; visible: true">
```

##### 3D Models

Models come in the format of plain text files containing vertices, faces, UVs, textures, materials, and animations. They also come with images for textures, usually alongside the model file. three.js loaders parse these files to render them within a three.js scene as meshes. A-Frame model components wrap these three.js loaders.

##### Optimizing Complex Models

A quick way to reduce the number of faces on a model is with the **decimate modifier** in Blender.

#### A-Frame && Three.js

Being a framework based on [three.js](http://threejs.org/), A-Frame provides full access to the three.js API. We’ll go over how to access the underlying three.js scene, objects, and API that lay underneath A-Frame.

three.js is available as a global object on the window:

```
console.log(THREE);
```

##### "follow" 构件 计算位置的算法：//...13/12/2021 LISA

```
 var factor = this.data.speed / distance;
    ['x', 'y', 'z'].forEach(function (axis) {
      directionVec3[axis] *= factor * (timeDelta / 1000);
    });

    // Translate the entity in the direction towards the target.
    this.el.setAttribute('position', {
      x: currentPosition.x + directionVec3.x,
      y: currentPosition.y + directionVec3.y,
      z: currentPosition.z + directionVec3.z
    });
```

Here are a few places to look:

- [A-Frame core components](https://github.com/aframevr/aframe/tree/master/src/components) - Source code of A-Frame’s standard components.
- [A-Painter components](https://github.com/aframevr/a-painter/tree/master/src/components) - Application-specific components for A-Painter.
- [**A Week of A-Frame** Weekly Series](https://aframe.io/blog/)
- [Official Site](https://aframe.io/)
- [Community](https://aframe.io/community/)
- [Components on npm](https://www.npmjs.com/search?q=keywords:aframe&page=1&ranking=optimal)
- [Twitter](https://twitter.com/aframevr/)

`angle` is a command-line interface for A-Frame; one of its features is to <u>set up a component template</u> for publishing to GitHub and npm and also to be consistent with all the other components in the ecosystem. To install the template:

```
npm install -g angle && angle initcomponent
```

`initcomponent` will ask for some information like the component name to get the template set up. Write some code, examples, and documentation, and publish to GitHub and npm!

#### Three.js

https://threejs.org/docs/

为了真正能够让你的场景借助three.js来进行显示，需要以下几个对象：场景、相机和渲染器，这样我们就能透过摄像机渲染出场景。

```javascript
<html>
	<head>
		<meta charset="utf-8">
		<title>My first three.js app</title>
		<style>
			body { margin: 0; }
		</style>
	</head>
	<body>
		<script src="js/three.js"></script>
		<script>
			const scene = new THREE.Scene();
			const camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );

			const renderer = new THREE.WebGLRenderer();
			renderer.setSize( window.innerWidth, window.innerHeight );
			document.body.appendChild( renderer.domElement );

			const geometry = new THREE.BoxGeometry();
			const material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
			const cube = new THREE.Mesh( geometry, material );
			scene.add( cube );

			camera.position.z = 5;

			const animate = function () {
				requestAnimationFrame( animate );

				cube.rotation.x += 0.01;
				cube.rotation.y += 0.01;

				renderer.render( scene, camera );
			};

			animate();
		</script>
	</body>
</html>
```



Object3D && object3DMap??? //...07/12/2021 LISA

`object3D` will be a `THREE.Group` object that may contain different types of `THREE.Object3D`s such as cameras, meshes, lights, or sounds.

An entity’s `object3DMap` is an object that gives access to the different types of `THREE.Object3D`s (e.g., camera, meshes, lights, sounds) that components have set.

```javascript
var sceneEl = document.querySelector('a-scene'); //根据标签名称
sceneEl.querySelector('#redBox');                //#根据ID
sceneEl.querySelectorAll('.clickable');          //.根据类名
sceneEl.querySelectorAll('[light]');             //[]包含某属性

```



#### Publishing a Site

There are many free services to deploy and host a site. We’ll go over some of the more easy or popular options, but there are certainly other options such as **AWS, Heroku,** or self-hosting. An important note is that these sites should be served with **SSL/HTTPS** due to a common security restriction of the browser’s **WebVR API.** All the options below serve with SSL/HTTPS.

补充：

SSL : Secure Sockets Layer;

TLS (Transport Layer Security) : an updated, more secure, version of SSL. 

#### Best Practise:

The fewer number of entities and lights in the scene, the better.

Each geometry, object, model without optimization is generally **a draw call**. Try to keep under 300 maximum. [Merge](https://www.npmjs.com/package/aframe-geometry-merger-component) together all static meshes if possible.

Bake your lights into textures rather than relying on real-time lighting and shadows.

Limit the number of faces and vertices on models.

#### APIs

##### Entity 属性：

Components	hasLoaded	 isPlaying	object3D	object3DMap	sceneEl

改属性的方式：

```javascript
entityEl.setAttribute('material', 'color', 'red');

entityEl.setAttribute('light', {color: '#ACC', intensity: 0.75});

//position, rotation, scale, and visible at the three.js level 
// Examples for position.
entityEl.object3D.position.set(1, 2, 3);
entityEl.object3D.position.x += 5;
entityEl.object3D.position.multiplyScalar(5);

// Examples for rotation.
entityEl.object3D.rotation.y = THREE.Math.degToRad(45);
entityEl.object3D.rotation.divideScalar(2);

// Examples for scale.
entityEl.object3D.scale.set(2, 2, 2);
entityEl.object3D.scale.z += 1.5;

// Examples for visible.
entityEl.object3D.visible = false;
entityEl.object3D.visible = true;
```



#### Componet示例

##### stats

The stats component displays a UI with performance-related metrics. The stats component applies only to the <a-scene> element. (FPS, vertex and face count, geometry and material count, draw calls, number of entities) //...

```
<a-scene stats></a-scene>
```

- **fps**: frames per second, framerate. Aim for stable 90 fps with the WebVR 1.0 API.
- **requestAnimationFrame** (raf): Latency.
- **Textures**: number of three.js textures in the scene. A lower count means the scene is using less memory and sending less data to the GPU.
