<template>
  <div id="all">
    <h1>Puissance 4</h1>
    <p id="message" class="messageFinal"></p>
    <div id="container">
      <button
        id="0"
        v-on:click="colorForPlayer(0, findLineToColor(0))"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="1"
        v-on:click="colorForPlayer(1, findLineToColor(1))"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="2"
        v-on:click="colorForPlayer(2, findLineToColor(2))"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="3"
        v-on:click="colorForPlayer(3, findLineToColor(3))"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="4"
        v-on:click="colorForPlayer(4, findLineToColor(4))"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="5"
        v-on:click="colorForPlayer(5, findLineToColor(5))"
        class="buttonClass"
      >
        PLAY
      </button>
      <button
        id="6"
        v-on:click="colorForPlayer(6, findLineToColor(6))"
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
      plateauJeu: [],
      playerName: 1, //1 = rouge 2 = jaune 0 = vide
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
      //   let tab = [];

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
        this.plateauJeu[i] = [];
        tile[i] = [];
        for (let j = 0; j < tailleTaby; j++) {
          this.plateauJeu[i][j] = 0;
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
      //check colonne
      for (let column = 0; column < 7; column++) {
        let caseMemeCouleur = 0;
        for (let line = 0; line < 6; line++) {
          if (playerName === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 4) {
              return true;
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }
      //check ligne
      for (let line = 0; line < 6; line++) {
        let caseMemeCouleur = 0;
        for (let column = 0; column < 7; column++) {
          if (playerName === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 4) {
              return true;
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }
      //check diagonal type: /
      for (let line = -1; line < 5; line++) {
        let caseMemeCouleur = 0;
        for (let column = -1; column < 6; column++) {
          let lineAdd = 2 + line + column;
          if (lineAdd < 7) {
            let columnAdd = column + 1;
            if (playerName === this.plateauJeu[lineAdd][columnAdd]) {
              caseMemeCouleur = caseMemeCouleur + 1;
              if (caseMemeCouleur === 4) {
                return true;
              }
            } else {
              caseMemeCouleur = 0;
            }
          }
        }
      }
      //check diagonal type: \
      for (let column = 8; column > 0; column--) {
        let caseMemeCouleur = 0;
        for (let line = -1; line < 5; line++) {
          let lineAdd = line + 1;
          let columnMinus = column - 2 - line;
          if (playerName === this.plateauJeu[lineAdd][columnMinus]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 4) {
              return true;
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }
      return false;
    },
    findLineToColor(columnNumber) {
      let tabColorOfcolumn = [];
      for (let i = 0; i < this.tabWithColorCases.length; i++) {
        if (this.tabWithColorCases[i][0] === columnNumber) {
          tabColorOfcolumn[tabColorOfcolumn.length] =
            this.tabWithColorCases[i][1];
        }
        if (tabColorOfcolumn.length == 5) {
          document
            .getElementById(columnNumber)
            .setAttribute("disabled", "disabled");
          document.getElementById(columnNumber).style.borderColor = "#E8E8E8";
        }
      }
      return tabColorOfcolumn.length === 0
        ? 0
        : tabColorOfcolumn[tabColorOfcolumn.length - 1] + 1;
    },
    colorForPlayer(caseToColorX, caseToColorY) {
      let color = this.playerName === 1 ? "#ff1a1a" : "#ffff00";
      let tab = [];
      tab[0] = [caseToColorX, caseToColorY, this.playerName];
      this.plateauJeu[caseToColorY][caseToColorX] = this.playerName;
      this.tabWithColorCases[this.tabWithColorCases.length] = tab[0];
      this.highlightCheckCase(this.scene, caseToColorX, caseToColorY, color);
      let isWine = this.isWine(this.playerName);
      let allCaseColored = this.allCasesNumber == this.tabWithColorCases.length;
      if (isWine || allCaseColored) {
        for (let columnNumber = 0; columnNumber < 7; columnNumber++) {
          document
            .getElementById(columnNumber)
            .setAttribute("disabled", "disabled");
          document.getElementById(columnNumber).style.borderColor = "#E8E8E8";
        }
        let message = isWine
          ? "Joueur " + this.playerName + " Vous avez gagnez !"
          : "Oups ! Match nul...";
        document.getElementById("message").innerHTML = message;
      }
      this.playerName = this.playerName === 1 ? 2 : 1;
    },
    // algoForIA(tabWithColorCases, playerName) {
    //   console.log("Algo");
    //   //trouver  les case possible a jouer
    //   //chercher la meilleur cas pour ia
    // },
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