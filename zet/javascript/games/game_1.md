# Create JavaScript Games Day 1

Let's play with html, CSS, and JavaScript.
* We create an index.html page
* We add the Canvas

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/favicon.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Game 1</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <canvas id="canvas1"></canvas>
 
    <script type="module" src="/main.js"></script>
  </body>
</html>
```

* We create a style.CSS page
* We add the following CSS below:

```css
#canvas1 {
  border: 5px solid black;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 600px;
  height: 600px;
}
```

* We create a Main.js JavaScript file
* We select the canvas in JavaScript like below:

```javascript
const canvas = document.getElementById('canvas1');
const ctx = canvas.getContext('2d');
console.log(ctx);
```

* Sprite Animation is the goal
* We add a global canvas style

```javascript
const CANVAS_WIDTH = canvas.width = 600;
const CANVAS_HEIGHT = canvas.height = 600;
```

* Add the image to the JavaScript file

```javascript
const playerImage = new Image();
playerImage.src = './shadow_dog.png';
```

* We make the animate function and call animate();

```javascript
function animate() {
  ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
  ctx.fillRect(50, 50, 100, 100);
  requestAnimationFrame(animate);
}
animate();
```

* Let us add the image onto the canvas
* The image has lots of sprite movement pictures

```javascript
function animate() {
  ctx.clearRect(0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
//   ctx.fillRect(50, 50, 100, 100);
  //   ctx.drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh);
  ctx.drawImage(playerImage, 0, 0, CANVAS_WIDTH, CANVAS_HEIGHT);
  requestAnimationFrame(animate);
}
```

* Now we want to get the player Image into the box
* `ctx.drawImage(image, sx, sy, sw, sh, dx, dy, dw, dh);`
* Width of page is 6876 for the image
* 12 columns
* `6876/12 = 573`
* We set the sprite width to 575
* `const spriteWidth = 575;`

```javascript
ctx.drawImage(playerImage, 0, 0, spriteWidth, spriteHeight, 0, 0, spriteWidth, spriteHeight);
```

* To move to the next frame

`ctx.drawImage(playerImage, 1 * spriteWidth, 0, spriteWidth, spriteHeight, 0, 0,
spriteWidth, spriteHeight);`


* To move to the next frame down 

`ctx.drawImage(playerImage, 1 * spriteWidth, 1 * spriteHeight, spriteWidth, spriteHeight, 0, 0,
spriteWidth, spriteHeight);`



https://youtu.be/GFO_txvwK_c?t=905
