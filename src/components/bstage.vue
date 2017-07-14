<template lang="pug">
	div 
		canvas(id='renderCanvas')
</template>

<script>
	export default {
		data(){
			return {
				camera: null,
				scene: null,
				holder: null,
				pos: 0,
				tm: null,
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
				// camera.attachControl(canvas, true);
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
				});
			},
			addmart(scene){
				//http://marterialeditor.raananweber.com/
				var mart = new BABYLON.Standardmarterial('scene', scene);
				mart.alpha = 1;
				mart.backFaceCulling = true;
				mart.specularPower = 64;
				mart.useSpecularOverAlpha = true;
				mart.useAlphaFromDiffuseTexture = false;

				// diffuse definitions;

				mart.diffuseColor = new BABYLON.Color3(1.00, 1.00, 1.00);

				// emissive definitions;

				mart.emissiveColor = new BABYLON.Color3(0.00, 0.00, 0.00);

				// ambient definitions;

				mart.ambientColor = new BABYLON.Color3(0.00, 0.00, 0.00);
				//Texture Parameters ;
				//TODO change the filename to fit your needs!;
				var mart_ambientTexture = new BABYLON.Texture('textures/mart_ambient.jpg', myWonderfulScene);
				mart_ambientTexture.uScale = 1;
				mart_ambientTexture.vScale = 1;
				mart_ambientTexture.coordinatesMode = 0;
				mart_ambientTexture.uOffset = 0;
				mart_ambientTexture.vOffset = 0;
				mart_ambientTexture.uAng = 0;
				mart_ambientTexture.vAng = 0;
				mart_ambientTexture.level = 1;
				mart_ambientTexture.coordinatesIndex = 0;
				mart_ambientTexture.hasAlpha = false;
				mart_ambientTexture.getAlphaFromRGB = false;

				mart.ambientTexture = mart_ambientTexture;

				// specular definitions;

				mart.specularColor = new BABYLON.Color3(1.00, 1.00, 1.00);
				return mart;
			},
			createScene(canvas, engine) {
			    // This creates a basic Babylon Scene object (non-mesh)
			    var scene = new BABYLON.Scene(engine);

			    this.scene = scene;

			    // This creates and positions a free camera (non-mesh)
			    var camera = new BABYLON.FreeCamera("camera1", new BABYLON.Vector3(0, 5, -10), scene);

			    this.camera = camera;

			    // This targets the camera to scene origin
			    camera.setTarget(BABYLON.Vector3.Zero());

			    // This attaches the camera to the canvas
			    // camera.attachControl(canvas, true);

			    this.lookAt(canvas, camera, scene);

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

			    ////////////////////////////////////////////////////////////////////////////////////////
				var cone = BABYLON.MeshBuilder.CreateCylinder("cone", {diameterTop: 4, tessellation: 16}, scene);
				cone.position.y = 1;
				cone.position.x = 10;

				var plane = BABYLON.MeshBuilder.CreatePlane("plane", {width: 5}, scene);
				plane.position.y = 1;
				plane.position.x = -10;

				// var marterialSphere1 = new BABYLON.Standardmarterial("texture1", scene);
				// var mt = new BABYLON.Texture('../static/hq720.jpg', scene);
				// var mt = this.addmart(scene);
				// plane.marterial = mt;

			    ////////////////////////////////////////////////////////////////////////////////////////
				var holder = new BABYLON.Mesh.CreateBox("myparent", 1, scene);
				sphere.parent = holder;
				ground.parent = holder;
				cone.parent = holder;
				plane.parent = holder;
				this.holder = holder;

			    return scene;
			},
			resize(engine){
				window.addEventListener('resize', function() {
				    engine.resize();
				});
			},
			update(engine, scene){
				engine.runRenderLoop( () => {
				    scene.render();
				});
	            window.addEventListener('mousedown', this.mouseDown, false);
	            window.addEventListener('mouseup', this.mouseUp, false);
	            window.addEventListener('mousemove', this.mouseMove, false);
	            window.addEventListener('wheel', this.mouseWheel, false);
			},
			mouseWheel(e){
				if(!this.tm || ( this.tm && !this.tm.isActive() ) ){
					var e = window.event || e; // old IE support
					var delta = Math.max(-1, Math.min(1, (e.wheelDelta || -e.detail)));
					var sec = 1;
					var gutter = 10;
					var dx = gutter * delta;
					this.pos += dx;
					this.pos = (this.pos > 10)? 10: this.pos;
					this.pos = (this.pos < -10)? -10: this.pos;
					var degree = { d: 0 };
					var cx = this.camera.position.x;
					var cy = this.camera.position.z;
					console.log('started')
					console.log(this.camera.position.x, this.camera.position.z)	
					this.tm = TweenMax.to( degree, sec, { 
						d: 180,
						ease: Quart.easeIn,
						onUpdate: ()=>{
							var dis = 10;
							var px = Math.cos( Math.PI / 180 * degree.d ) * dis;
							var py = -0 + Math.sin( Math.PI / 180 * degree.d ) * dis;
							var ex = cx + (px * delta);
							var ey = cy + py;

							ex = ( ex > 10 )? 10: ex;
							ex = ( ex < -10 )?-10: ex;

							// ex = ex.toFixed(2);
							// ey = ey.toFixed(2);

							this.camera.position.x = ex;
							this.camera.position.z = ey;
							// console.log(ex, ey)
						},
						onComplete: ()=>{
							this.camera.position.x = Math.round(this.camera.position.x);
							this.camera.position.z = Math.round(this.camera.position.z);
							console.log('complete')
							console.log(this.camera.position.x, this.camera.position.z)	
						}
					} );
				}
			},
			mouseMove(e){
				let w = window.innerWidth;
				let h = window.innerHeight;
                let x = e.clientX;
                let y = e.clientY;
                let camera = this.camera;
                let scene = this.scene;
				let cx = x-(w/2);
				let cy = y-(h/2);
				let obstacleX = .0001;
				let obstacleY = .0001;
				let dx = cx * obstacleX;
				let dy = cy * obstacleY;
				let sec = 1;
				try{
					TweenMax.to(this.holder.rotation, sec, { z: dx, x: -dy });
				}catch(e){
					return new Error(e)
				}
			},
			mouseDown(e){
			},
			mouseUp(e){
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
    body
        // background-image: url('../assets/hq720.jpg')
</style>