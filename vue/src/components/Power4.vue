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
      ligne: 6,
      colonne: 7,
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
        tile[i] = [];
        for (let j = 0; j < tailleTaby; j++) {
          tile[i][j] = new Three.Mesh(geometry, mat);
          this.scene.add(tile[i][j]);
          //console.log(i + "  " +j)
          tile[i][j].position.x = i;
          tile[i][j].position.y = j;
        }
      }
      for (let line = 0; line < tailleTaby; line++) {
        this.plateauJeu[line] = [];
        for (let colulmn = 0; colulmn < tailleTabx; colulmn++) {
          this.plateauJeu[line][colulmn] = 0;
        }
      }
      console.log("INIT plateau jeu ", this.plateauJeu);

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
              console.log("gagne par colonne", caseMemeCouleur);
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
              console.log("gagne par ligne", caseMemeCouleur);
              return true;
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }
      //check diagonal type: /
      for (var i = 0; i < this.colonne - 3; i++) {
        for (var j = 0; j < this.ligne - 3; j++) {
          if (
            this.plateauJeu[j][i] == playerName &&
            this.plateauJeu[j + 1][i + 1] == playerName &&
            this.plateauJeu[j + 2][i + 2] == playerName &&
            this.plateauJeu[j + 3][i + 3] == playerName
          ) {
            return true;
          }
        }
      }
      //check diagonal type: \
      for (var i = 0; i < this.colonne - 3; i++) {
        for (var j = 3; j < this.ligne; j++) {
          if (
            this.plateauJeu[j][i] == playerName &&
            this.plateauJeu[j - 1][i + 1] == playerName &&
            this.plateauJeu[j - 2][i + 2] == playerName &&
            this.plateauJeu[j - 3][i + 3] == playerName
          ) {
            return true;
          }
        }
      }
      console.log("pas encore gagné ");
      return false;
    },
    findLineToColor(columnNumber) {
      let tabColorOfcolumn = [];
      for (let i = 0; i < this.tabWithColorCases.length; i++) {
        if (this.tabWithColorCases[i][0] === columnNumber) {
          tabColorOfcolumn[tabColorOfcolumn.length] =
            this.tabWithColorCases[i][1];
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
      console.log("je colore la case y, x  ", caseToColorY, caseToColorX);
      this.plateauJeu[caseToColorY][caseToColorX] = this.playerName;
      this.tabWithColorCases[this.tabWithColorCases.length] = tab[0];
      this.highlightCheckCase(this.scene, caseToColorX, caseToColorY, color);
      //check si toute la colonne est plein
      this.checkIfColumnIsFull();

      console.log("Le plateau de jeux est maintenant = ", this.plateauJeu);
      console.log("jcheck is wine pour le joueur", this.playerName);
      let isWine = this.isWine(this.playerName);
      console.log("resultat de is wine", isWine);
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
        return isWine; //retourne true seulement si gangé (pour arrete IA et le jeux) sinon false ca continue
      }
      this.playerName = this.playerName === 1 ? 2 : 1;
      this.playerColor =
        this.playerName === 1 ? "color: Tomato" : "color: Tomato";
      return false;
    },
    playerTurn(caseToColorX, caseToColorY) {
      let isFinish = this.colorForPlayer(caseToColorX, caseToColorY);
      if (this.optionIa && !isFinish) {
        this.algoForIA();
      }
    },
    checkIfColumnIsFull() {
      for (let column = 0; column < this.colonne; column++) {
        let colorLine = 0;
        for (let line = 0; line < this.ligne; line++) {
          if (this.plateauJeu[line][column] != 0) {
            console.log(
              "checkIfColumnIsFull je suis coloré ",
              line,
              column,
              this.plateauJeu[line][column]
            );
            console.log(
              "pour la colonne on a x case coloré",
              column,
              colorLine
            );
            colorLine = colorLine + 1;
          }
          if (colorLine === 6) {
            console.log("je suis full", column);
            document
              .getElementById(column)
              .setAttribute("disabled", "disabled");
            document.getElementById(column).style.borderColor = "#E8E8E8";
          }
        }
      }
    },
    algoForIA() {
      let casesNotToPlay = [];

      let otherPlayerValue = this.playerName === 1 ? 2 : 1;
      // si personne en 0,3 => joue là
      if (this.plateauJeu[0][3] === 0) {
        this.colorForPlayer(3, 0);
        return;
      }
      //si je peux gagner je joue quand 3 IA d'affilé
      //check colonne
      for (let column = 0; column < 7; column++) {
        let caseMemeCouleur = 0;
        for (let line = 0; line < 6; line++) {
          if (this.playerName === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 3) {
              if (line < 5 && this.plateauJeu[line + 1][column] == 0) {
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
          if (this.playerName === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 3) {
              console.log("JE PEUX GG SUR UNE LIGNE");
              if (
                column < 6 &&
                this.plateauJeu[line][column + 1] == 0 &&
                this.plateauJeu[line - 1][column + 1] != 0
              ) {
                console.log(
                  "Je check si je peux gg sur une ligne et je colore (colonne, ligne ) : ",
                  column,
                  line
                );
                this.colorForPlayer(column + 1, line);
                return;
              } else if (
                column < 6 &&
                this.plateauJeu[line][column - 1] == 0 &&
                this.plateauJeu[line - 1][column - 1] != 0
              ) {
                console.log(
                  "Je check si je peux gg sur une ligne et je colore (colonne, ligne ) : ",
                  column,
                  line
                );
                this.colorForPlayer(column - 1, line);
                return;
              }
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }

      //check diagonale /
      //check diagonale \

      
      //************************************** */
      //si il joue en 0,3 et 0,4  => joue en 0,2 ou 0,5
      if (
        this.plateauJeu[0][3] === otherPlayerValue &&
        this.plateauJeu[0][4] === otherPlayerValue
      ) {
        let values = [];
        if (this.plateauJeu[0][2] === 0) {
          values[values.length] = 2;
        }
        if (this.plateauJeu[0][5] === 0) {
          values[values.length] = 5;
        }
        if (values.length == 1) {
          this.colorForPlayer(values[0], 0);
          return;
        }
        if (values.length > 1) {
          let aleatoireNumber =
            values[Math.floor(Math.random() * values.length)];
          this.colorForPlayer(aleatoireNumber, 0);
          return;
        }
      }
      //si il joue en 0,3 et 0,2  => joue en 0,1 ou 0,4
      if (
        this.plateauJeu[0][3] === otherPlayerValue &&
        this.plateauJeu[0][2] === otherPlayerValue
      ) {
        let values = [];
        if (this.plateauJeu[0][1] === 0) {
          values[values.length] = 1;
        }
        if (this.plateauJeu[0][4] === 0) {
          values[values.length] = 4;
        }
        if (values.length == 1) {
          this.colorForPlayer(values[0], 0);
          return;
        }
        if (values.length > 1) {
          let aleatoireNumber =
            values[Math.floor(Math.random() * values.length)];
          this.colorForPlayer(aleatoireNumber, 0);
          return;
        }
      }
      //si je peux l'empecher de gagner on l'empeche
      //check colonne
      for (let column = 0; column < 7; column++) {
        let caseMemeCouleur = 0;
        for (let line = 0; line < 6; line++) {
          if (otherPlayerValue === this.plateauJeu[line][column]) {
            caseMemeCouleur = caseMemeCouleur + 1;
            if (caseMemeCouleur === 3) {
              if (line < 5 && this.plateauJeu[line + 1][column] == 0) {
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
            if (caseMemeCouleur === 2) {
              let caseRigth = undefined;
              let caseLeft = undefined;
              let caseRigthDown = undefined;
              let caseLeftDown = undefined;

              if (column < 6) {
                caseRigth = this.findRigthCase(line, column, otherPlayerValue);
              }
              if (column >= 2) {
                caseLeft = this.findLeftCase(
                  line,
                  column - 1,
                  otherPlayerValue
                );
              }

              if (line > 0 && column > 2 && caseLeft != undefined) {
                let caseLeftDownX = caseLeft[0] - 1;
                let caseLeftDownY = caseLeft[1];
                if (this.plateauJeu[caseLeftDownX][caseLeftDownY] != 0) {
                  caseLeftDown = [caseLeftDownX, caseLeftDownY];
                }
                console.log("final  caseLeftDown= ", caseLeftDown);
              }
              if (line > 0 && column < 6 && caseRigth != undefined) {
                let caseRigthDownX = caseRigth[0] - 1;
                let caseRigthDownY = caseRigth[1];
                if (this.plateauJeu[caseRigthDownX][caseRigthDownY] != 0) {
                  caseRigthDown = [caseRigthDownX, caseRigthDownY];
                }
                console.log("final  caseRigthDown= ", caseRigthDown);
              }

              if (caseLeftDown != undefined) {
                console.log("ajoute la case bas gch dans pas jouer = ");
                casesNotToPlay[casesNotToPlay] = [
                  caseLeftDown[0],
                  caseLeftDown[1],
                ];
              }
              if (caseRigthDown != undefined) {
                console.log("ajoute la case bas dr dans pas jouer = ");
                casesNotToPlay[casesNotToPlay] = [
                  caseRigthDown[0],
                  caseRigthDown[1],
                ];
              }

              if (
                caseLeft != undefined &&
                (caseLeftDown != undefined || caseLeft[0] === 0)
              ) {
                console.log("je joue a gch");
                this.colorForPlayer(caseLeft[1], caseLeft[0]);
                return;
              }
              if (
                caseRigth != undefined &&
                (caseRigthDown != undefined || caseRigth[0] === 0)
              ) {
                console.log("je joue a drt");
                this.colorForPlayer(caseRigth[1], caseRigth[0]);
                return;
              }
            }
          } else {
            caseMemeCouleur = 0;
          }
        }
      }

      //check diagonale /
      //check diagonale \

      //case  avecle plus de chance de gagner
      let caseToColor = this.findCaseWithMaxChanceToWin(casesNotToPlay);
      this.colorForPlayer(caseToColor[1], caseToColor[0]);
    },
    findCaseWithMaxChanceToWin(casesNotToPlay) {
      console.log("start findCaseWithMaxChanceToWin");
      let allPossibleCases = this.findAllPossibleCases(casesNotToPlay);
      let casesWithMaxChance = undefined;
      //***************OK***************
      for (let i = 0; i < allPossibleCases.length; i++) {
        //compte le nombre de chance de gagner pour chaque case
        allPossibleCases[i][2] = this.countChanceToWin(allPossibleCases[i]);
      }
      //trouve la case avec le plus de chance de gagner
      console.log("trouve la case avec le plus de chance de gagner");
      casesWithMaxChance = allPossibleCases[0];
      console.log("initial casesWithMaxChance = ", casesWithMaxChance);
      for (let i = 0; i < allPossibleCases.length; i++) {
        if (casesWithMaxChance[2] < allPossibleCases[i][2]) {
          casesWithMaxChance = allPossibleCases[i];
          console.log("met a jour casesWithMaxChance = ", casesWithMaxChance);
        }
      }
      console.log("case avec le plus de chance =", casesWithMaxChance);
      return casesWithMaxChance;
    },
    countChanceToWin(caseToCount) {
      let chanceToWin = 0;
      let caseToCountY = caseToCount[1];
      let caseToCountX = caseToCount[0];
      console.log(
        "countChanceToWin de la case x, y",
        caseToCountX,
        caseToCountY
      );
      //colonne
      if (
        caseToCountX < this.ligne - 3 &&
        this.plateauJeu[caseToCountX][caseToCountY] == 0 &&
        this.plateauJeu[caseToCountX + 1][caseToCountY] == 0 &&
        this.plateauJeu[caseToCountX + 2][caseToCountY] == 0 &&
        this.plateauJeu[caseToCountX + 3][caseToCountY] == 0
      ) {
        chanceToWin = chanceToWin + 1;
      }

      //ligne
      if (
        caseToCountY < 3 &&
        this.plateauJeu[caseToCountX][caseToCountY] == 0 &&
        this.plateauJeu[caseToCountX][caseToCountY + 1] == 0 &&
        this.plateauJeu[caseToCountX][caseToCountY + 2] == 0 &&
        this.plateauJeu[caseToCountX][caseToCountY + 3] == 0
      ) {
        chanceToWin = chanceToWin + 1;
      }

      //diagonal / en montant
      if (
        caseToCountY <= 3 &&
        caseToCountX <= 2 &&
        this.plateauJeu[caseToCountX][caseToCountY] == 0 &&
        this.plateauJeu[caseToCountX + 1][caseToCountY + 1] == 0 &&
        this.plateauJeu[caseToCountX + 2][caseToCountY + 2] == 0 &&
        this.plateauJeu[caseToCountX + 3][caseToCountY + 3] == 0
      ) {
        chanceToWin = chanceToWin + 1;
      }
      //diagonal / en descendant TODO

      //diagonal \ en montant
      if (
        caseToCountY <= 3 &&
        caseToCountX >= 3 &&
        this.plateauJeu[caseToCountX][caseToCountY] == 0 &&
        this.plateauJeu[caseToCountX - 1][caseToCountY + 1] == 0 &&
        this.plateauJeu[caseToCountX - 2][caseToCountY + 2] == 0 &&
        this.plateauJeu[caseToCountX - 3][caseToCountY + 3] == 0
      ) {
        chanceToWin = chanceToWin + 1;
      }
      //diagonal \ en descendant TODO
      return chanceToWin;
    },
    findAllPossibleCases(casesNotToPlay) {
      let allPossibleCases = [];
      for (let column = 0; column < this.colonne; column++) {
        console.log("findAllPossibleCase PORU LA COLONNE", column);
        let line = 0;
        let notFind = true;
        while (notFind) {
          console.log(
            " findAllPossibleCases Line Colonne",
            line,
            column,
            this.plateauJeu[line][column]
          );
          if (this.plateauJeu[line][column] === 0) {
            //&&this.checkCaseFindIsNotInCasesNotToPlay(casesNotToPlay, line, column)

            console.log("case aajouter = et BREAK TO DO ", [line, column]);
            allPossibleCases[allPossibleCases.length] = [line, column];
            notFind = false;
          } else if (line < 5) {
            line = line + 1;
            console.log("increment la ligne a ", line);
          } else {
            // ou on a pas trouvé = plus de place donc passe colonne apres
            notFind = false;
          }
        }
      }
      console.log("toutes les cases possibles", allPossibleCases);
      return allPossibleCases;
    },
    checkCaseFindIsNotInCasesNotToPlay(casesNotToPlay, line, column) {
      for (let i = 0; i < casesNotToPlay.length; i++) {
        if (casesNotToPlay[i][0] === line && casesNotToPlay[i][1] === column) {
          console.log("case parmis celle a ne  pas jouer");
          return false;
        }
      }
      return true;
    },
    findRigthCase(line, column, otherPlayerValue) {
      let caseRigth = undefined;
      column = column + 1;
      let caseValueToCheck = this.plateauJeu[line][column];
      if (column <= 6) {
        if (caseValueToCheck === 0) {
          // si celle de droite est vide on la prend
          caseRigth = [line, column];
        } else if (caseValueToCheck === otherPlayerValue) {
          // si celle de droite appartient à l'autre joueur on regarde celle encore a  droite
          caseRigth = this.findRigthCase(line, column, otherPlayerValue);
        }
      }
      console.log("caseRigth = ", caseRigth);
      return caseRigth;
    },
    findLeftCase(line, column, otherPlayerValue) {
      let caseLeft = undefined;
      column = column - 1;
      let caseValueToCheck = this.plateauJeu[line][column];
      if (column >= 0) {
        if (caseValueToCheck === 0) {
          // si celle de gauche est vide on la prend
          caseLeft = [line, column];
        } else if (caseValueToCheck === otherPlayerValue) {
          // si celle de gauche appartient à l'autre joueur on regarde celle encore a gauche
          caseLeft = this.findLeftCase(line, column, otherPlayerValue);
        }
      }
      console.log("caseLeft = ", caseLeft);
      return caseLeft;
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