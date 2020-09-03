<template>
  <div id="app">
    <div class="wrapper">
		<h1>Simon Says</h1>
			<div class="game-board">
				<div class="simon">
					<ul>
						<li class="tile red" id="red" @click="addToPlayer('red')"></li>
						<li class="tile blue" id="blue" @click="addToPlayer('blue')"></li>
						<li class="tile yellow" id="yellow" @click="addToPlayer('yellow')"></li>
						<li class="tile green" id="green" @click="addToPlayer('green')"></li>
					</ul>
				</div>
			</div>
			<div class="game-info">
				<h2>Round: <span class="rounds">{{game.count}}</span></h2>
				<button @click="newGame">Start</button>
				<p class="lose">Sorry, you lost after <span class="rounds">0</span> rounds!</p>
			</div>
			<div class="game-options">
				<h2>Game Options:</h2>
				<input type="radio" name="mode" value="easy" checked @click="changeDifficulty('easy')">Easy<br>
				<input type="radio" name="mode" value="normal" @click="changeDifficulty('normal')">Normal<br>
				<input type="radio" name="mode" value="hard" @click="changeDifficulty('hard')">Hard<br>
			</div>
    </div>
	</div>
</template>

<script>
export default {
  name: 'App',
  data: () => ({
    game: {
      count: 0,
      possibilities: ['#green','#blue', '#red', '#yellow'],
      currentGame: [],
      player: [],
      sound:{
        blue: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound1.mp3'), 
        red: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound2.mp3'), 
        yellow: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound3.mp3'), 
        green: new Audio('https://s3.amazonaws.com/freecodecamp/simonSound4.mp3')
      },
      difficulty: 1500
    }
  }),
  methods: {
    newGame() {
      this.clearGame()
    },
    clearGame() {
      this.game.currentGame = []
      this.game.count = 0
      let element = document.querySelectorAll('p.lose')
      element[0].style.display = 'none'
      this.addCount()
    },
    addCount() {
      this.game.count = this.game.count + 1
      let elements = document.querySelectorAll('.rounds')
      elements[1].innerHTML = this.game.count
      this.generateMove()
    },
    generateMove(){
      this.game.currentGame.push(this.game.possibilities[(Math.floor(Math.random()*4))])
      this.showMoves()
    },
    showMoves() {
      let i = 0
      let moves = setInterval(() => {
        this.playGame(this.game.currentGame[i])
        i++
        if (i >= this.game.currentGame.length) {
          clearInterval(moves)
        }
      }, this.game.difficulty)
      
      this.clearPlayer()
    },
    playGame(field) {
      let element = document.getElementById(field.substr(1, field.length))
      element.classList.add('lit')
      this.sound(field.substr(1, field.length))
      setTimeout(() => {
          let element = document.getElementById(field.substr(1, field.length))
          element.classList.remove('lit')
      }, this.game.difficulty)
    },
    clearPlayer() {
      this.game.player = []
    },
    addToPlayer(id) {
      if(this.game.count !== 0) {
        let field = "#"+id
        let element = document.getElementById(id)
        element.classList.add('lit')
        setTimeout(() => {
          let element = document.getElementById(id)
          element.classList.remove('lit')
        }, 300)
        this.sound(id)
        this.game.player.push(field)
        this.playerTurn(field)
      }
    },
    playerTurn(field) {
      if (this.game.player[this.game.player.length - 1] !== this.game.currentGame[this.game.player.length - 1]) {
        let element = document.querySelectorAll('p.lose')
        this.game.count = 0
        element[0].style.display = (element[0].style.display == 'none' || element[0].style.display == '') ? 'block' : 'none'
      } else {
        this.sound(field)
        let check = this.game.player.length === this.game.currentGame.length
        if (check) {
          this.nextLevel()
        }
      }
    },
    sound(name) {
      switch(name) {
        case'green':
          this.game.sound.green.play()
          break
        case 'blue':
          this.game.sound.blue.play()
          break
        case 'red':
          this.game.sound.red.play()
          break
        case 'yellow':
          this.game.sound.yellow.play()
          break
      }
    },
    nextLevel() {
      this.addCount()
    },
    changeDifficulty(difficult) {
      switch (difficult) {
        case 'easy':
          this.game.difficulty = 1500
          break
        case 'normal':
          this.game.difficulty = 1000
          break
        case 'hard':
          this.game.difficulty = 400
          break
        default:
          this.game.difficulty = 1500
          break
      }
    }
  }
}
</script>

<style>
@import 'https://fonts.googleapis.com/css?family=Muli';
body {
  background-color: #071013;
	color: white;
  font-family: 'Muli', sans-serif;
	-webkit-user-select: none; /* Chrome/Safari */        
	-moz-user-select: none; /* Firefox */
	-ms-user-select: none; /* IE10+ */
	-o-user-select: none;
	user-select: none;
}

h1 {
	margin: 1em 0 2em;
	text-align: center;
}

ul {
	list-style: none;
}

ul, li {
	padding: 0;
	margin: 0;
}

p[data-action="lose"] {
	display: none;
}

.active {
	opacity: 1 !important;
}

.clearfix {
	width: 100%;
	clear: both;
}

.wrapper {
	width: 540px;
	margin: 0 auto;
}
.container {
	width: 305px;
}

.simon {
	background: #fff;
	position: relative;
	float: left;
	margin-right: 3em;	
	width: 302px;
	height: 295px;
	-webkit-border-radius: 150px 150px 150px 150px;
	border-radius: 150px 150px 150px 150px;
    -moz-box-shadow: 2px 1px 12px #aaa;
    -webkit-box-shadow: 2px 1px 12px #aaa;
    -o-box-shadow: 2px 1px 12px #aaa;
    box-shadow: 2px 1px 12px #aaa;
}

.tile {
	opacity: 0.6;
	-webkit-transition: opacity 250ms ease;
	-moz-transition: opacity 250ms ease;
	-ms-transition: opacity 250ms ease;
	-o-transition: opacity 250ms ease;
	transition: opacity 250ms ease;
}

.tile.lit {
	opacity: 1;
}

.red, .blue, .yellow, .green {
	height: 290px;
	-webkit-border-radius: 150px 150px 150px 150px;
	border-radius: 150px 150px 150px 150px;
	position: absolute;
	text-indent: 10000px;
}

.red:hover, .blue:hover, .yellow:hover, .green:hover {
	border: 2px solid black
}

.red {
	background: #FF5643;
	clip: rect(0px, 300px, 150px, 150px);
	width: 296px;
}

.blue {
	background: dodgerblue;
	clip: rect(0px, 150px, 150px, 0px);
	width: 300px;
}

.yellow {
	background: #FEEF33;
	clip: rect(150px, 150px, 300px, 0px);
	width: 300px;
}

.green {
	background: #BEDE15;
	clip: rect(150px,300px, 300px, 150px);
	width: 296px;
}

.game-info {
	margin-top: 90px;
}

.game-info button {
	width: 5em;
	box-sizing: border-box;
	font-size: 1.4em;
	-webkit-border-radius: 10px 10px 10px 10px;
	border-radius: 10px 10px 10px 10px;
	background: #6DABE8;
	border: none;
	padding: 0.3em 0.6em;
}

.game-info button:hover {
	background: #78BCFF;
}

.game-options h2 {
	margin-top: 30px;
	margin-bottom: 0;
}

.game-options input[type="radio"] {
	margin-right: 10px;
}

.hoverable:hover {
	cursor: pointer;
}
</style>
