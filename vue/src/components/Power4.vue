<template>
  <div id="all">
    <h1>Puissance 4</h1>
    <p id="message" class="messageFinal"></p>
    <div id="container">
      <button
        id="0"
        v-on:click="colorForPlayer(0, findLineToColor(0), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="1"
        v-on:click="colorForPlayer(1, findLineToColor(1), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="2"
        v-on:click="colorForPlayer(2, findLineToColor(2), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="3"
        v-on:click="colorForPlayer(3, findLineToColor(3), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="4"
        v-on:click="colorForPlayer(4, findLineToColor(4), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="5"
        v-on:click="colorForPlayer(5, findLineToColor(5), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="6"
        v-on:click="colorForPlayer(6, findLineToColor(6), 1)"
        class="buttonClass"
      >
        PLAY
      </button>
    </div>
  </div>
</template>

<script>
import * as Three from "three";

export default {
  name: "Power4",
  data() {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined,
      tabWithColorCases: [],
      allCasesNumber: 0,
    };
  },
  methods: {
    init: function () {
      let container = document.getElementById("container");
      let tailleTabx = 7;
      let tailleTaby = 6;
      this.allCasesNumber = tailleTaby * tailleTabx;
      this.camera = new Three.PerspectiveCamera(
        70,
        container.clientWidth / container.clientHeight,
        0.01,
        20
      );
      this.camera.position.z = 4.3;
      this.camera.position.x = 3;
      this.camera.position.y = 2.5;

      this.scene = new Three.Scene();
      let tile = [];
      let tab = [];

      const geometry = new Three.PlaneGeometry(1, 1);
      const viergeCase = new Three.MeshBasicMaterial({
        color: "#1E90FF",
        wireframe: true,
        side: Three.DoubleSide,
      });
      const buttonCase = new Three.MeshBasicMaterial({
        color: "#D3D3D3",
        wireframe: true,
        side: Three.DoubleSide,
      });
      for (let i = 0; i < tailleTabx; i++) {
        let mat = viergeCase;
        tab[i] = [];
        tile[i] = [];
        for (let j = 0; j < tailleTaby; j++) {
          tab[i][j] = 0;
          tile[i][j] = new Three.Mesh(geometry, mat);
          this.scene.add(tile[i][j]);
          //console.log(i + "  " +j)
          tile[i][j].position.x = i;
          tile[i][j].position.y = j;
        }
      }
      //------------------------------------------------------------------------------------------------------------------
      this.renderer = new Three.WebGLRenderer({ antialias: true });
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);
      //algo

      console.log(this.tabWithColorCases);
    },
    isWine(playerName) {
      console.log("IS WINE tabWithColorCases", this.tabWithColorCases);
      //   console.log("IS WINE tab", this.tab);

      //   for (let column = 0; i < 6; column++) {
      //     for (let i = 0; i < this.tabWithColorCases.length; i++) {
      //       console.log("IS WINE all", this.tabWithColorCases[i]);
      //       //[Log] IS WINE all – [[0, 0, 1], [0, 1, 1], [0, 2, 1]] tabWithColorCases
      //       // playerName === this.tabWithColorCases[i][2] && column === this.tabWithColorCases[i][0]
      //       if (
      //         playerName === this.tabWithColorCases[i][2] &&
      //         playerName === this.tabWithColorCases[i][2]
      //       ) {
      //         //check si les 4 case appartiennet au meme joueur et sont sur la meme ligne
      //         return true;
      //       }
      //       //   console.log("IS WINE", this.tabWithColorCases[column])
      //       //1 4 couleurs sur meme colonne
      //       //2 4 couleurs sur meme ligne
      //       //3 4 couleurs sur meme diagonal /
      //       //4 4 couleurs sur meme diagonal \
      //     }
      //   }
      this.tabWithColorCases;
      if (this.tabWithColorCases === 4) {
        return "Victoire pour le joueur " + playerName + " !!";
      }
    },
    findLineToColor(columnNumber) {
      let tabColorOfcolumn = [];
      for (let i = 0; i < this.tabWithColorCases.length; i++) {
        if (this.tabWithColorCases[i][0] === columnNumber) {
          tabColorOfcolumn[tabColorOfcolumn.length] =
            this.tabWithColorCases[i][1];
        }
        if (tabColorOfcolumn.length == 5) {
          document.getElementById(columnNumber).setAttribute('disabled','disabled');
          document.getElementById(columnNumber).style.borderColor = "#E8E8E8";
        }
      }
      return tabColorOfcolumn.length === 0
        ? 0
        : tabColorOfcolumn[tabColorOfcolumn.length - 1] + 1;
    },
    colorForPlayer(caseToColorX, caseToColorY, playerName) {
      let color = playerName === 1 ? "#ff1a1a" : "#ffff00";
      let tab = [];
      tab[0] = [caseToColorX, caseToColorY, playerName];
      this.tabWithColorCases[this.tabWithColorCases.length] = tab[0];
      this.highlightCheckCase(this.scene, caseToColorX, caseToColorY, color);
      let isWine = this.isWine(playerName);
      let allCaseColored = this.allCasesNumber == this.tabWithColorCases.length;
      if (isWine || allCaseColored) {
        for (let columnNumber = 0; columnNumber < 7; columnNumber++) {
          document.getElementById(columnNumber).setAttribute('disabled','disabled');
          document.getElementById(columnNumber).style.borderColor = "#E8E8E8";
        }
        let message = isWine
          ? "Joueur " + playerName + " Vous avez gagnez !"
          : "Oups ! Match nul...";
        document.getElementById("message").innerHTML = message;
      }
    },
    algoForIA(tabWithColorCases, playerName) {
      console.log("Algo");
      //trouver  les case possible a jouer
      //chercher la meilleur cas pour ia
    },
    highlightCheckCase(scene, x, y, colortile) {
      const geometry = new Three.PlaneGeometry(0.7, 0.7);
      const mat = new Three.MeshBasicMaterial({
        color: colortile,
        side: Three.DoubleSide,
      });
      const plane = new Three.Mesh(geometry, mat);
      scene.add(plane);
      plane.position.x = x;
      plane.position.y = y;
    },
    animate: function () {
      requestAnimationFrame(this.animate);
      this.renderer.render(this.scene, this.camera);
    },
  },
  mounted() {
    this.init();
    this.animate();
  },
};
</script>

<style scoped>
#container {
  width: 1000px;
  height: 700px;
  margin-left: auto;
  margin-right: auto;
}
.buttonClass {
  margin-right: 60px;
  border: 1px solid blue;
  border-radius: 5px;
  background-color: white;
  padding: 10px;
}
.messageFinal {
  font-size: 20px;
  color: red;
}
</style>