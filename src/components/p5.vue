<template>
  <div id="canvas"></div>
</template>

<script>
import P5 from 'p5';
export default {
  name: 'P5canvas',
  props: {
    msg: String
  },
  mounted(){
    const sketch =(p5)=> {
      class Wall {
        constructor() {
          this.wallx = 0;
          this.floory = 0;
        }
        display() {
          //     Draw Wall and Floor
          p5.rect(70, p5.windowHeight - 50, p5.windowWidth - 140, 10);
          p5.rect(70, p5.windowHeight - 50, 10, -p5.windowHeight / 2);
          //     Set Wall and Floor location
          this.wallx = 70 + 10;
          this.floory = p5.windowHeight - 50;
          p5.text('frictionless surface', p5.windowWidth / 2 - 50, p5.windowHeight - 15)
        }
      }
      class Score {
        constructor() {
          this.collisions = 0;
        }
        display() {
          p5.textSize(24);
          p5.text('Collisions = ' + this.collisions, p5.windowWidth - 210, 40);
        }
      }
      class Cube {
        constructor() {
          this.mass = 50;
          this.vx = 0;
          this.rhs = 0;
          this.lhs = 0;
          this.temp = 0;
          this.x = 0;
          this.y = 0;
          this.xConstraint = 0;
        }
        update() {
          this.x = this.x + this.vx;
          this.lhs = this.x;
          this.rhs = this.lhs + p5.map(this.mass, 1, 100, 50, 120);
        }
        display() {
          let x = p5.constrain(this.x, this.xConstraint, p5.windowWidth)
          p5.square(x, wall.floory - p5.map(this.mass, 1, 100, 50, 120), p5.map(this.mass, 1, 100, 50, 120));
          p5.textSize(15);
          p5.text(this.mass + 'kg', x + 8 + 0.5, wall.floory - 5 - p5.pow(this.mass, 1 / 3))
        }
      }
      let wall;
      let score;
      let smallBlock;
      let bigBlock;
      let button;
      let buttonText;
      let temp_small, temp_big, slider;

      p5.setup=()=> {
        p5.createCanvas(p5.windowWidth, p5.windowHeight);
        slider = p5.createSlider(1, 100, 1);
        slider.position(10, 30);
        slider.style('width', '80px');
        wall = new Wall();
        score = new Score();
        smallBlock = new Cube();
        smallBlock.x = 200;
        smallBlock.mass = 1;

        bigBlock = new Cube();
        bigBlock.x = p5.windowWidth * 0.6;

        buttonText = "Start Sketch"
        button = p5.createButton(buttonText);
        button.mousePressed(toggleStart);
        button.position(12, 50);

      }

      p5.draw=()=> {
        p5.background(252, 221, 209);
        smallBlock.xConstraint = wall.wallx;
        bigBlock.xConstraint = wall.wallx + smallBlock.rhs - smallBlock.lhs + 12;
        if (button.html() == "Start Sketch") {
          bigBlock.mass = slider.value();
        }
        let val = slider.value();
        p5.text('Mass ' + val + ' kg', 12, 20)
        wall.display();
        score.display();
        bigBlock.update();
        smallBlock.update();
        bigBlock.display();
        smallBlock.display();
        calcV(smallBlock, bigBlock);
        if (bigBlock.lhs >= p5.windowWidth) {
          p5.background(226, 236, 228);
          p5.text('Mass ' + val + ' kg', 12, 20);
          bigBlock.vx = 0;
          smallBlock.vx = 0;
          wall.display();
          score.display();
          p5.textSize(32);
          p5.text('Total collisions = ' + score.collisions, p5.windowWidth / 3, p5.windowHeight / 2)
          bigBlock.update();
          smallBlock.update();
          bigBlock.display();
          smallBlock.display();
        }

      }

      function calcV(small, big) {
        if (small.rhs >= big.lhs) {
          score.collisions++;
          temp_small = small.vx;
          temp_big = big.vx;
          small.vx = ((1 - big.mass) / (1 + big.mass)) * small.vx + ((2 * big.mass) / (1 + big.mass)) * big.vx;
          big.vx = (2 / (1 + big.mass)) * temp_small + ((big.mass - 1) / (1 + big.mass)) * temp_big;

        }
        if (small.lhs <= wall.wallx) {
          score.collisions++;
          small.vx = -small.vx
        }
      }


      function toggleStart() {
        if (button.html() == "Start Sketch") {
          p5.background(252, 221, 209);
          button.html('Reset Sketch');
          bigBlock.vx = -1;
        } else if (button.html() == "Reset Sketch") {
          button.html('Start Sketch');
          p5.background(252, 221, 209);
          smallBlock.x = 200;
          smallBlock.vx = 0;
          smallBlock.mass = 1;
          bigBlock = new Cube();
          bigBlock.x = p5.windowWidth * 0.6;
          bigBlock.vx = 0;
          score.collisions = 0;
        }
      }

    }
    new P5(sketch, 'canvas');
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
