<template>
  <div id="all">
    <h1>Puissance 4</h1>
    <p id="message" class="messageFinal"></p>
    <div id="player">
      <p v-if="1 === this.playerName">
        Tour du joueur {{ this.playerName }}:
        <font-awesome-icon icon="circle" style="color: red" />
      </p>
      <p v-else>
        Tour du joueur {{ this.playerName }}:
        <font-awesome-icon icon="circle" style="color: yellow" />
      </p>
    </div>
    <button id="8" v-on:click="optionIA(true)" class="buttonClass">
      JOUEUR VS IA
    </button>
    <button id="7" v-on:click="optionIA(false)" class="buttonClass">
      JOUEUR VS JOUEUR
    </button>

    <div id="container">
      <button
        id="0"
        v-on:click="playerTurn(0, findLineToColor(0))"
        class="buttonPlayClass buttonClass"
      >
        PLAY
      </button>
      <button
        id="1"
        v-on:click="playerTurn(1, findLineToColor(1))"
        class="buttonPlayClass buttonClass"
      >
        PLAY
      </button>
      <button
        id="2"
        v-on:click="playerTurn(2, findLineToColor(2))"
        class="buttonPlayClass buttonClass"
      >
        PLAY
      </button>
      <button
        id="3"
        v-on:click="playerTurn(3, findLineToColor(3))"
        class="buttonPlayClass buttonClass"
      >
        PLAY
      </button>
      <button
        id="4"
        v-on:click="playerTurn(4, findLineToColor(4))"
        class="buttonPlayClass buttonClass"
      >
        PLAY
      </button>
      <button
        id="5"
        v-on:click="playerTurn(5, findLineToColor(5))"
        class="buttonPlayClass buttonClass"
      >
        PLAY
      </button>
      <button
        id="6"
        v-on:click="playerTurn(6, findLineToColor(6))"
        class="buttonPlayClass buttonClass"
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
      optionIa: undefined,
      playerColor: undefined,
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
    optionIA(optionIA) {
      this.optionIa = optionIA;
      document.getElementById("0").style.visibility = "visible";
      document.getElementById("1").style.visibility = "visible";
      document.getElementById("2").style.visibility = "visible";
      document.getElementById("3").style.visibility = "visible";
      document.getElementById("4").style.visibility = "visible";
      document.getElementById("5").style.visibility = "visible";
      document.getElementById("6").style.visibility = "visible";
      document.getElementById("7").style.visibility = "hidden";
      document.getElementById("8").style.visibility = "hidden";
      document.getElementById("player").style.visibility = "visible";
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
        document.getElementById("player").style.visibility = "hidden";
        document.getElementById("message").innerHTML = message;
      }
      this.playerName = this.playerName === 1 ? 2 : 1;
      this.playerColor =
        this.playerName === 1 ? "color: Tomato" : "color: Tomato";
    },
    playerTurn(caseToColorX, caseToColorY) {
      this.colorForPlayer(caseToColorX, caseToColorY);
      if (this.optionIa) {
        this.algoForIA();
      }
    },
    algoForIA(tabWithColorCases, playerName) {
      let casesNotToPlay = [];

      let otherPlayerValue = this.playerName === 1 ? 2 : 1;
      // si personne en 0,3 => joue là
      if (this.plateauJeu[0][3] === 0) {
        this.colorForPlayer(3, 0);
        return;
      }
      //si je peux gagner je joue

      // TODO
      //si il joue en 0,3 et 0,4  => joue en 0,2 ou 0,5
      if (
        this.plateauJeu[0][3] === otherPlayerValue &&
        this.plateauJeu[0][4] === otherPlayerValue
      ) {
          if (this.plateauJeu[0][3] === 0) {
              // avant de jouer check si ya deja quelquun !!!!!!çç!!!!!!!!!
          }
        let values = [2, 5];
        let aleatoireNumber = values[Math.floor(Math.random() * values.length)];
        this.colorForPlayer(aleatoireNumber, 0);
        return;
      }
      //si il joue en 0,3 et 0,2  => joue en 0,1 ou 0,4
      if (
        this.plateauJeu[0][3] === otherPlayerValue &&
        this.plateauJeu[0][2] === otherPlayerValue
      ) {
        let values = [1, 4];
        let aleatoireNumber = values[Math.floor(Math.random() * values.length)];
        this.colorForPlayer(aleatoireNumber, 0);
        return;
      }
      //si je peux l'empecher de gagner on l'empeche
      //check colonne
      for (let column = 0; column < 7; column++) {
        let caseMemeCouleur = 0;
        for (let line = 0; line < 6; line++) {
          if (otherPlayerValue === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 3) {
              if (line < 6) {
                this.colorForPlayer(column, line + 1);
                return;
              }
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }
      //check lignes
      for (let line = 0; line < 6; line++) {
        let caseMemeCouleur = 0;
        for (let column = 0; column < 7; column++) {
          if (otherPlayerValue === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            console.log("jai x case pour joueur", caseMemeCouleur, otherPlayerValue);
            if (caseMemeCouleur === 3) {
              console.log("jai case rouge", caseMemeCouleur);
              let caseRigth = this.plateauJeu[line][column + 1];
              let caseleft = this.plateauJeu[line][column - 3];
              let caseLeftDown = this.plateauJeu[line - 1][column - 3];
              let caseRigthDown = this.plateauJeu[line - 1][column + 1];
               console.log("dr, gch, bgh, bdr", caseRigth, caseleft, caseLeftDown, caseRigthDown);
              if (caseLeftDown === 0) {
                casesNotToPlay[casesNotToPlay] = [line - 1, column - 1];
              }
              if (caseRigthDown === 0) {
                casesNotToPlay[casesNotToPlay] = [line + 1, column - 1];
              }
              if (caseLeftDown != 0 && caseRigth === 0) {
                this.colorForPlayer(column - 1, line);
                return;
              }
              if (caseRigthDown != 0 && caseleft === 0) {
                this.colorForPlayer(column + 1, line);
                return;
              }
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }

      //3 je trouve la cas ou il peut le plus gangé
      //4 si pls = 1 parmis celle trouvé
      this.colorForPlayer(0, 0);
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
.buttonPlayClass {
  visibility: hidden;
}
.messageFinal {
  font-size: 20px;
  color: red;
}
#player {
  visibility: hidden;
}
</style>