<template lang="pug">
	div 
		canvas(id='renderCanvas')
</template>

<script>
	export default {
		data(){
			return {

			};
		},
		methods:{
			init(){
				var canvas = document.getElementById('renderCanvas');
				var engine = new BABYLON.Engine(canvas, true);
				var scene = this.createScene( canvas, engine );
				this.update(engine, scene);
				this.resize(engine);
			},
			lookAt(canvas, camera, scene){
				camera.speed = 0.5;
				camera.angularSensibility = 500;
				// This attaches the camera to the canvas
				camera.attachControl(canvas, true);
				scene.collisionsEnabled = true;
				camera.checkCollisions = true;
				//controlling the camera up's and down's
				scene.registerBeforeRender(()=>{
					if (camera.rotation.x < 0) {
						camera.rotation.x = 0;
					}
					if (camera.rotation.x > 0.5) {
						camera.rotation.x = 0.5;
					}
					if (camera.position.y > 5) {
						camera.position.y = 5.2;
					}
					if (camera.position.y < 5) {
						camera.position.y = 5;
					}
						if (camera.position.z < -10) {
						camera.position.z = -10;
					}
				})
			},
			createScene(canvas, engine) {
			    // This creates a basic Babylon Scene object (non-mesh)
			    var scene = new BABYLON.Scene(engine);

			    // This creates and positions a free camera (non-mesh)
			    var camera = new BABYLON.FreeCamera("camera1", new BABYLON.Vector3(0, 5, -10), scene);

			    // This targets the camera to scene origin
			    camera.setTarget(BABYLON.Vector3.Zero());

			    // This attaches the camera to the canvas
			    camera.attachControl(canvas, true);

			    // this.lookAt(canvas, camera, scene);

			    // This creates a light, aiming 0,1,0 - to the sky (non-mesh)
			    var light = new BABYLON.HemisphericLight("light1", new BABYLON.Vector3(0, 1, 0), scene);

			    // Default intensity is 1. Let's dim the light a small amount
			    light.intensity = 0.7;

			    // Our built-in 'sphere' shape. Params: name, subdivs, size, scene
			    var sphere = BABYLON.Mesh.CreateSphere("sphere1", 16, 2, scene);

			    // Move the sphere upward 1/2 its height
			    sphere.position.y = 1;

			    // Our built-in 'ground' shape. Params: name, width, depth, subdivs, scene
			    var ground = BABYLON.Mesh.CreateGround("ground1", 6, 6, 2, scene);

			    return scene;
			},
			update(engine, scene){
				engine.runRenderLoop(function() {
				    scene.render();
				});
			},
			resize(engine){
				window.addEventListener('resize', function() {
				    engine.resize();
				});
			},
		},
		mounted(){
			// window.addEventListener('DOMContentLoaded', function() {
		        // your code here
		    // });
			this.init();
		},
	}
</script>

<style lang="sass">
    div
        width: 100%
        height: 100%
    #renderCanvas
        width: 100%
        height: 100%
        touch-action: none
</style>