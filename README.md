# coding-assignment
let scl = 20;

let score = 0;

let playerCol = (255,255,255);

let player;

let food;

let foodCol;

let walls = [];

function setup() {
  createCanvas(500, 500);
  frameRate(18);
  textSize(18);
  noStroke();

  generateFood();
  player = new Player();

  background(30);
}

// generating food
function generateFood() {
  let cols = floor(width / scl);
  let rows = floor(height / scl);
  food = createVector(floor(random(cols)), floor(random(rows)));
  food.mult(scl);

  foodCol = color(random(30, 255), random(30, 255), random(30, 255));
}

// generating random wall
function generateWall() {
  let cols = floor(width / scl);
  let rows = floor(height / scl);

  let wall = createVector(floor(random(cols)), floor(random(rows)));
  wall.mult(scl);

  walls.push(wall);
}

function draw() {
  //background(30);
  noStroke();
  fill(30, 150);
  rect(0, 0, width, height);

  //eating food
  if (player.eat(food)) {
    generateFood();
    generateWall();
    score++;
  }

  //touching wall
  for (let w of walls) {
    let d = dist(w.x, w.y, player.x, player.y);
    if (d < 1) {
      walls = [];
      score = 0;
      player.dir(0, 0);
      generateFood();
    }
  }

  // food
  fill(foodCol);
  ellipse(food.x + scl * 0.5, food.y + scl * 0.5, scl * 0.7, scl * 0.7);

  // player
  player.update();
  player.draw();

  // wall
  noFill();
  stroke(240);
  for (let w of walls) {
    rect(w.x, w.y, scl, scl, 1);
  }

  // score
  fill(240);
  text("SCORE: " + score, 5, 25);
}

// directions
function keyPressed() {
  if (keyCode === UP_ARROW) {
    player.dir(0, -1);
  } else if (keyCode === DOWN_ARROW) {
    player.dir(0, 1);
  } else if (keyCode === RIGHT_ARROW) {
    player.dir(1, 0);
  } else if (keyCode === LEFT_ARROW) {
    player.dir(-1, 0);
  }
}

function Player() {
  this.x = 0;
  this.y = 0;
  this.xspeed = 1;
  this.yspeed = 0;

  // 
  this.update = function () {
    this.x = this.x + this.xspeed * scl;
    this.y = this.y + this.yspeed * scl;

    // stop player froming leaving canvas
    this.x = constrain(this.x, 0, width - scl);
    this.y = constrain(this.y, 0, height - scl);
  };

  // drawing player
  this.draw = function () {
    fill(playerCol);
    rect(this.x, this.y, scl, scl, 2);
  };

  // eaten food
  this.eat = function (f) {
    let d = dist(this.x, this.y, f.x, f.y);
    return d < 1;
  };

  // changing direction
  this.dir = function (x, y) {
    this.xspeed = x;
    this.yspeed = y;
  };
}
