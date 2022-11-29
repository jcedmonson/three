<template>
  <v-container class="pa-0 ma-0">
    <v-fade-transition>
      <v-card
        dark
        rounded
        style="position: absolute; z-index: 1; top: 10px; left: 10px"
        v-if="showDetails"
        width="285"
      >
        <v-card-title>
          <v-avatar class="mr-3">
            <v-img v-if="name == 'ArcCorp'" src="@/assets/arccorp.jpg"></v-img>
            <v-img v-else-if="name == 'Hurston'" src="@/assets/hurston.jpg"></v-img>
            <v-img v-else-if="name == 'Crusader'" src="@/assets/crusader.jpg"></v-img>
            <v-img v-else-if="name == 'microTech'" src="@/assets/microTech.png"></v-img>
            <v-img v-else src="@/assets/UEE.png"></v-img>
          </v-avatar>
          {{ name }} 
        </v-card-title>
        <v-card-text style="font-size: .65em;"> {{planetDescription}} </v-card-text>
      </v-card>
    </v-fade-transition>
    <div id="demo"></div>
    <v-card
      style="
        position: absolute;
        z-index: 1;
        top: 10px;
        right: 10px;
        width: 250px;
      "
    >
      <v-card-text>
        <h3 class="white--text font-weight-light">Orbit Speed</h3>
        <v-slider class="mt-5" dense max="3" min="1" :label="`${orbitSpeed}`" v-model="orbitSpeed"></v-slider>
      </v-card-text>
      <v-card-actions>
        <v-switch
          class="pl-3"
          dark
          dense
          inset
          v-model="orbit"
          label="Orbit"
        ></v-switch>
        <v-spacer></v-spacer>
        <v-switch
          class="pr-3"
          dark
          dense
          inset
          v-model="grid"
          label="Grid"
        ></v-switch>
      </v-card-actions>
    </v-card>
  </v-container>
</template>

<script>
import * as THREE from "three";
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";

export default {
  name: "HelloWorld",

  data: () => ({
    showDetails: false,
    planetSelected: false,
    selected: "",
    name: "",
    orbit: true,
    grid: false,
    scene: null,
    gridHelper: null,
    orbitSpeed: "",
    hurstonLore: "Hurston (Stanton I) is a terrestrial Super-Earth and Earth analog in the Stanton System. According to Chris Roberts, it has a diameter of 2000 km in-game, which equates to 12,000 km in the Lore. Hurston is home to its namesake, Hurston Dynamics, an aristocratic family-run weapons manufacturer that has bled the planet dry.",
    crusaderLore: "Crusader is a low-mass gas giant that has an atmosphere which is breathable at high altitudes. It has a diameter of 14900km plus 1200km of atmosphere. The portion of the world available to visitors is considered by many to be the nicest port in the system. The shipyards themselves are eerily beautiful, with huge transport ships suspended in mid-atmosphere surrounded by a lighted webbing of Crusader Industries facilities.",
    arccorpLore: "ArcCorp (Stanton III) is a Super-Earth in the Stanton System owned and terraformed by the ArcCorp corporation. In just 80 years all of the terrain of the planet has been sculpted, zoned, and built upon, leaving very little left for nature",
    microtechLore: "microTech (Stanton IV) is a large and generally cold planet that is home to the microTech Corporation. The temperature is the result of an error during the UEE terraforming process, which lead to unusually dense cloud production. microTech produces mobiGlas here, a now-standard piece of digital assistive technology used by nearly everyone in the Empire. ",
    stantonLore: "The Stanton system is a single star system, and one of two fully colonized systems in the UEE. It is the only system with a green zone reaching four planets, making it suitable for human life. All worlds have undergone limited terraforming and have flora and fauna with divergent ecologies. They are owned by corporate entities within the United Empire of Earth."
  }),

  computed: {
    planetDescription(){
      let lore = "";
      switch (this.name){
        case "Hurston":
          lore = this.hurstonLore;
          break;
        case "Crusader":
          lore = this.crusaderLore;
          break;
        case "microTech":
          lore = this.microtechLore;
          break;
        case "ArcCorp":
          lore = this.arccorpLore;
          break;
        case "Stanton":
          lore = this.stantonLore;
          break;
      }
      return lore;
    },

    planetImage(){
      let img = "";
      switch (this.name){
        case "Hurston":
          img = "@/assets/arccorp.jpg"
          break;
        case "Crusader":
          img = "@/assets/arccorp.jpg";
          break;
        case "microTech":
          img = "@/assets/arccorp.jpg"
          break;
        case "ArcCorp":
          img = "@/assets/arccorp.jpg"
          break;
        case "Stanton":
          img = "@/assets/arccorp.jpg"
          break;
      }
      return img;
    }
  },

  methods: {
    addGrid() {
      console.log("Adding Grid");
      if (this.gridHelper == null) {
        this.gridHelper = new THREE.GridHelper(200, 50);
      }

      if (this.grid) {
        this.scene.add(this.gridHelper);
      } else {
        this.scene.remove(this.gridHelper);
      }
    },

    calculateOrbitSpeed(speed){
      let modifiedSpeed = 0;
      switch (speed) {    
        case 2:
          modifiedSpeed = 0.1;
          break;
        case 3:
          modifiedSpeed = 0.2;
          break;
      }
      return modifiedSpeed;
    }
  },

  mounted() {
    var mouse, raycaster;

    var planets = {
      crusader: null,
      microtech: null,
      hurston: null,
      arccorp: null,
    };

    // this.scene
    this.scene = new THREE.Scene();

    // camera
    const camera = new THREE.PerspectiveCamera(
      75,
      window.innerWidth / window.innerHeight,
      0.1,
      1000
    );
    // renderer
    const renderer = new THREE.WebGLRenderer();
    renderer.setSize(window.innerWidth, window.innerHeight);
    document.getElementById("demo").appendChild(renderer.domElement);
    renderer.setPixelRatio(window.devicePixelRatio);
    camera.position.setZ(65);
    camera.position.setX(0);
    camera.position.setY(25);

    // Ray Caster Setup
    mouse = new THREE.Vector2();
    raycaster = new THREE.Raycaster();

    // Object Creation
    const sunGeometry = new THREE.SphereGeometry(5, 25, 25);
    const sunMaterial = new THREE.MeshBasicMaterial({ color: 0xffff00 });
    const sun = new THREE.Mesh(sunGeometry, sunMaterial);
    sun.name = "Stanton";
    this.scene.add(sun);

    const createPlanet = (r, x, y, z, color, name, image) => {
      const createPath = (
        radius,
        tube,
        radialSegments,
        tubularSegments,
        arc,
        color
      ) => {
        const geometry = new THREE.TorusGeometry(
          radius,
          tube,
          radialSegments,
          arc
        );
        const material = new THREE.MeshBasicMaterial({ color: color });
        const torus = new THREE.Mesh(geometry, material);
        torus.rotation.x = 300;
        return torus;
      };

      const planetGeometry = new THREE.SphereGeometry(r, 25, 25);
      const planetMaterial = new THREE.MeshStandardMaterial({ color: color });
      const planet = new THREE.Mesh(planetGeometry, planetMaterial);
      planet.name = name;
      planet.position.set(x, y, z);

      const planetObject = new THREE.Object3D();
      planetObject.add(planet);
      planetObject.add(createPath(x + 1, 0.1, 10, 10, 200, 0x7393b3));
      return planetObject;
    };

    planets.hurston = createPlanet(2.3, 15, 0, 0, 0xff6347, "Hurston");
    planets.crusader = createPlanet(3, 25, 0, 10, 0xe69b00, "Crusader");
    planets.arccorp = createPlanet(2, 35, 0, 10, 0xff2547, "ArcCorp");
    planets.microtech = createPlanet(1.5, 45, 0, 10, 0xffffff, "microTech");

    Object.keys(planets).forEach((k) => {
      this.scene.add(planets[k]);
    });

    // light
    const pointLight = new THREE.PointLight(0xffffff);
    pointLight.position.set(0, 0, 0);
    this.scene.add(pointLight);


    // orbit controls
    const controls = new OrbitControls(camera, renderer.domElement);

    function onMouseMove(event) {
      mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
      mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;
    }

    window.addEventListener("mousemove", onMouseMove, false);

    const pickPlanet = () => {
      raycaster.setFromCamera(mouse, camera);
      const intersects = raycaster.intersectObjects(this.scene.children);
      if (intersects.length > 0 && intersects[0].object.name != '') {

        this.showDetails = true;
        this.name = intersects[0].object.name;
        this.selected = intersects[0].object;
      }
      // intersects.forEach((e) => {
      //   this.planetSelected = true;
      //   this.showDetails = true;
      //   setTimeout(() => {
      //     this.showDetails = false;
      //   }, 1000);
      // });
    };

    this.scene.background = new THREE.TextureLoader().load(
      "https://images.pexels.com/photos/176851/pexels-photo-176851.jpeg"
    );

    // https://starcitizen.tools/images/thumb/1/12/Hurston-Icon.png/255px-Hurston-Icon.png

    const animate = () => {
      // planetObject.rotation.y += 0.002;
      pickPlanet();
      if (this.orbit) {
        let speed = this.calculateOrbitSpeed(this.orbitSpeed);
        sun.rotation.y += 0.002 + speed;
        planets.hurston.rotation.y += 0.006 + speed;
        planets.crusader.rotation.y += 0.005 + speed;
        planets.arccorp.rotation.y += 0.004 + speed;
        planets.microtech.rotation.y += 0.002 + speed;
      }

      requestAnimationFrame(animate);
      controls.update();
      renderer.render(this.scene, camera);
    };
    animate();
  },

  watch: {
    grid(newVal, oldVal) {
      this.addGrid();
    },
  },
};
</script>
