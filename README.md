// Constants
final int PIZZA_SIZE = 50;
final int TOPPING_SIZE = 10;
final int OBSTACLE_SIZE = 20;
final int RECTANGLE_WIDTH = 20;
final int RECTANGLE_HEIGHT = 40;
final int LIVES = 3;
final int SCORE_PER_TOPPING = 10;

// Game Variables
int score = 0;
int lives = LIVES;

// Abstract class representing a generic game object
abstract class GameObject {
  float x, y;

  GameObject(float x, float y) {
    this.x = x;
    this.y = y;
  }

  abstract void draw();
  abstract void update();
}

// Pizza class
class Pizza extends GameObject {
  int toppingCount = 0;
  
  Pizza(float x, float y) {
    super(x, y);
    
  }

  void draw() {
    
    fill(255, 204, 0); // Pizza color
    ellipse(x, y, PIZZA_SIZE, PIZZA_SIZE); // Drawing the pizza
  }

  void update() {
    x = mouseX;
    y = mouseY;
  }

  void addTopping() {
    toppingCount++;
    score += SCORE_PER_TOPPING;
  }
}

// Topping class
class Topping extends GameObject {
  Topping(float x, float y) {
    super(x, y);
  }

  void draw() {
    fill(255, 0, 0); // Topping color
    ellipse(x, y, TOPPING_SIZE, TOPPING_SIZE); // Drawing the topping
  }

  void update() {
    y += 2; // Topping falls down
  }
}

// Obstacle class
class Obstacle extends GameObject {
  Obstacle(float x, float y) {
    super(x, y);
  }

  void draw() {
    fill(0, 0, 255); // Obstacle color
    rect(x, y, OBSTACLE_SIZE, OBSTACLE_SIZE); // Drawing the obstacle
  }

  void update() {
    
  }
}

// MovingRectangle class
class MovingRectangle extends GameObject {
  MovingRectangle(float x, float y) {
    super(y, x);
  }

  void draw() {
    fill(0, 255, 0); // Rectangle color
    rect(x, y, 100, 10); // Drawing the rectangle
  }

  void update() {
    x += 3; // Move the rectangle downwards
  }
}

ArrayList<GameObject> gameObjects;
Pizza pizza;

void setup() {
  size(800, 600);
  gameObjects = new ArrayList<GameObject>();
  pizza = new Pizza(width / 2, height / 2);
  gameObjects.add(pizza);
}

void draw() {
  background(64);
  pizza.update();

  // Spawn toppings, obstacles, and moving rectangles at random intervals
  if (frameCount % 60 == 0) {
    gameObjects.add(new Topping(random(width), 0));
  }
  if (frameCount % 120 == 0) {
    gameObjects.add(new Obstacle(random(width), random(height)));
  }
  if (frameCount % 90 == 0) { // Every 1.5 seconds
    gameObjects.add(new MovingRectangle(random(width - RECTANGLE_WIDTH), 0));
  }

  for (int i = 0; i < gameObjects.size(); i++) {
    GameObject obj = gameObjects.get(i);
    obj.update();
    obj.draw();
  }

  displayScoreAndLives();
}

void mousePressed() {
  for (int i = gameObjects.size() - 1; i >= 0; i--) {
    GameObject obj = gameObjects.get(i);
    if (obj instanceof Topping && dist(mouseX, mouseY, obj.x, obj.y) < PIZZA_SIZE / 2) {
      pizza.addTopping();
      gameObjects.remove(i);
    } else if (obj instanceof Obstacle && dist(mouseX, mouseY, obj.x, obj.y) < OBSTACLE_SIZE) {
      lives--;
      gameObjects.remove(i);
      if (lives <= 0) {
        gameOver();
      }
    }
  }
}

void displayScoreAndLives() {
  textSize(16);
  fill(255);
  text("Score: " + score, 10, 20);
  text("Lives: " + lives, 10, 40);
}

void gameOver() {
    textSize(32);
    fill(255, 0, 0);
    text("Game Over", width / 2 - 100, height / 2);
    noLoop(); // Stop the game loop
  }
