
# Index.js
<img width="900" alt="PacmanSS" src="https://github.com/justjoe6/Pacman/assets/68125991/e65b4d27-ed1e-4f2c-94c7-8b2203030c9d">


## Boundary Class
```
class Boundary {
    static height = 40
    static width = 40
    constructor({position,image}) {
        this.position = position
        this.width = 40
        this.height = 40
        this.image = image
    }
    draw() {
        c.drawImage(this.image,this.position.x,this.position.y)

    }
}
```

## Pacman Class
```
class Pacman {
    constructor({position,velocity}) {
        this.position = position
        this.velocity = velocity
        this.radius = 15
        this.radians = 0.75
        this.openRate = 0.05
        this.rotation = 0
    }
    draw() {

        c.save()
        c.translate(this.position.x,this.position.y)
        c.rotate(this.rotation)
        c.translate(-this.position.x,-this.position.y)
        c.beginPath()
        c.arc(this.position.x,this.position.y,this.radius,this.radians,
            Math.PI*2 -this.radians)
        c.lineTo(this.position.x,this.position.y)
        c.fillStyle = 'yellow'
        c.fill()
        c.closePath()
        c.restore()
    }
    update() {
        this.draw()
        this.position.x += this.velocity.x
        this.position.y += this.velocity.y

        if(this.radians < 0 || this.radians > 0.75)
        {
            this.openRate = -this.openRate
        }
        this.radians += this.openRate
    }
}
```

## Chost Class
```
class Ghost {
    static speed = 2
    constructor({position,velocity,color = 'red'}) {
        this.position = position
        this.velocity = velocity
        this.color = color
        this.radius = 15
        this.prevCollisions = []
        this.speed = 2
        this.scared = false
    }
    draw() {
        c.beginPath()
        c.arc(this.position.x,this.position.y,this.radius,0,Math.PI*2)
        c.fillStyle = this.scared ? 'blue' : this.color
        c.fill()
        c.closePath()
    }
    update() {
        this.draw()
        this.position.x += this.velocity.x
        this.position.y += this.velocity.y
    }
}
```

## Pellet Class
```
class Pellet {
    constructor({position}) {
        this.position = position
        this.radius = 3
    }
    draw() {
        c.beginPath()
        c.arc(this.position.x,this.position.y,this.radius,0,Math.PI*2)
        c.fillStyle = 'white'
        c.fill()
        c.closePath()
    }
}
```

## PowerUp Class
```
class PowerUp {
    constructor({position}) {
        this.position = position
        this.radius = 7
    }
    draw() {
        c.beginPath()
        c.arc(this.position.x,this.position.y,this.radius,0,Math.PI*2)
        c.fillStyle = 'white'
        c.fill()
        c.closePath()
    }
}
```

## Creating Ghosts
Here we create a list of new Ghost Objects with their respective positions on the board and an initial velocity.
```
const ghosts = [
    new Ghost({
        position:{
            x:Boundary.width*4 + Boundary.width/2,
            y:Boundary.height*7 + Boundary.height/2
        },
        velocity:{
            x:-Ghost.speed,
            y:0
        }
    }),
    new Ghost({
        position:{
            x:Boundary.width*6 + Boundary.width/2,
            y:Boundary.height*5 + Boundary.height/2
        },
        velocity:{
            x:Ghost.speed,
            y:0
        },
        color:'pink'
    }),    
    new Ghost({
        position:{
            x:Boundary.width*6 + Boundary.width/2,
            y:Boundary.height*7 + Boundary.height/2
        },
        velocity:{
            x:Ghost.speed,
            y:0
        },
        color:'green'
    }),
    new Ghost({
        position:{
            x:Boundary.width*4 + Boundary.width/2,
            y:Boundary.height*5 + Boundary.height/2
        },
        velocity:{
            x:-Ghost.speed,
            y:0
        },
        color:'orange'
    })
]
```

## Creating PacMan
Similar to the Ghosts we create a PacMan Object with its respective position on the board and its initial velocity of 0 in both directions.
```
const pacman = new Pacman({
    position: {
        x:Boundary.width + Boundary.width/2,
        y:Boundary.height + Boundary.height/2
    },
    velocity: {
        x:0,
        y:0
    }
})
```


## Detecting Collisions
Here the code uses the position of a ghosts/pacman then we add or subtract the radius of the circle so we can see if the border of the circle hits one the boundaries. It also takes into consideration the velocity of the ghost/pacman and adds some padding so that they all stop just before the boundaries and thus are not just hugging the boundary the whole time.
```
function isCollision({circle,rect}){
    const padding = Boundary.width/2 - circle.radius - 1

    return circle.position.y - circle.radius + circle.velocity.y <= rect.
        position.y + rect.height + padding && 
        circle.position.x+circle.radius + circle.velocity.x >= rect.
        position.x - padding && circle.position.y + circle.radius 
        + circle.velocity.y >= rect.position.y - padding && 
        circle.position.x-circle.radius + circle.velocity.x <= rect.
        position.x+rect.width + padding
}
```

## Maze

### Defining Maze

A array of arrays is used in order to define the Maze such that each subarray in the array corresponds to a row of the gameboard.
```
const map = [
    ['1', '-', '-', '-', '-', '-', '-', '-', '-', '-', '2'],
    ['|', '', '.', '.', '.', '.', '.', '.', '.', 'p', '|'],
    ['|', '.', 'b', '.', '[', 'T', ']', '.', 'b', '.', '|'],
    ['|', '.', '.', '.', '.', '|', '.', '.', '.', '.', '|'],
    ['|', '.', '[', ']', '.', '>', '.', '[', ']', '.', '|'],
    ['|', '.', '.', '.', '.', '.', '.', '.', '.', '.', '|'],
    ['|', '.', 'b', '.', '[', '-', ']', '.', 'b', '.', '|'],
    ['|', '.', '.', '.', '.', '.', '.', '.', '.', '.', '|'],
    ['|', '.', '[', ']', '.', '<', '.', '[', ']', '.', '|'],
    ['|', '.', '.', '.', '.', '|', '.', '.', '.', '.', '|'],
    ['|', '.', 'b', '.', '[', 'Y', ']', '.', 'b', '.', '|'],
    ['|', '.', '.', '.', '.', '.', '.', '.', '.', 'p', '|'],
    ['4', '-', '-', '-', '-', '-', '-', '-', '-', '-', '3']
  ]
```

### Displaying Maze

Here I loop through each subarray and then each element of each subarray and then display an image on the canvas corresponding to the symbol stored in the array. For example, in the case that the symbol '-' is stored then a horizional pipe will be displayed at the corresponding position(Decided by the index of the subarray * Boundary.width and the index of the array * Boundary.height).

```
map.forEach((row,i) => {
    row.forEach((symbol,j) => {
        switch (symbol){
            case '-':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeHorizontal.png')
                }))
                break
            case '|':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeVertical.png')
                }))
                break
            case '1':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeCorner1.png')
                }))
                break
            case '2':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeCorner2.png')
                }))
                break
            case '3':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeCorner3.png')
                }))
                break
            case '4':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeCorner4.png')
                }))
                break
            case 'b':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/block.png')
                }))
                break
            case ']':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/capRight.png')
                }))
                break
            case '[':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/capLeft.png')
                }))
                break
            case '<':
                boundaries.push(new Boundary({
                        position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/capTop.png')
                }))
                break
            case '>':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/capBottom.png')
                }))
                break
            case 'T':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeConnectorBottom.png')
                }))
                break
            case 'Y':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeConnectorTop.png')
                }))
                break
            case 'X':
                boundaries.push(new Boundary({
                    position:{
                        x:j*Boundary.width,
                        y:i*Boundary.height
                    },
                    image: createImage('./imgs/pipeCross.png')
                }))
                break
            case '.':
                pellets.push(new Pellet({
                    position: {
                        x:j*Boundary.width + Boundary.width/2,
                        y:i*Boundary.height + Boundary.height/2
                    }
                }))
                break
            case 'p':
                powerUps.push(new PowerUp({
                    position: {
                         x:j*Boundary.width + Boundary.width/2,
                        y:i*Boundary.height + Boundary.height/2
                    }
                }))
                break
        }
    })
})
```

## Animation Function

### PacMan Movement & Boundary Collision Detection

Here I have provided an example of how collision detection between PacMan and the boundaries is implemented along with PacMan using the W key or Upward motino as an example. If the PacMan will not collide with any boundaries then its velocity in the y direction is set to -5 thus moving it upwards on the screen. Otherwise, if a collision is detected then the velocity of the PacMan is set to 0 in that direction thus preventing it from passing any boundaries.

```
    if(keys.w.pressed && lastKey =='w')
    {
        for(let i = 0; i < boundaries.length; i++)
        {            
            const boundary = boundaries[i]    
            if(isCollision({circle:{...pacman, velocity:{
                x:0,
                y:-5
            }},rect:boundary})){
                pacman.velocity.y=0
                break
            }
            else {
                pacman.velocity.y=-5
            }
        }
    }
```

### Implementing PacMan PowerUps
<img width="917" alt="Screenshot 2024-01-07 121041" src="https://github.com/justjoe6/Pacman/assets/68125991/1834fe37-8ced-4773-8d99-5980b5b78c1d">


A list of PowerUp objects is looped through and if the PacMan makes contact with a PowerUp then that PowerUp object is removed from the list of PowerUps and from the board. Also each ghost becomes scared or in other words the color of the ghosts all become blue and the PacMan is able to eat the ghosts removing them from the board.

```
    for(let i = powerUps.length-1; i>=0;i--)
    {
        const powerUp = powerUps[i]
        powerUp.draw()
        if(Math.hypot(powerUp.position.x-pacman.position.x,
            powerUp.position.y-pacman.position.y) < powerUp.radius + pacman.radius)
            {
                powerUps.splice(i,1)

                ghosts.forEach(ghost => {
                    ghost.scared = true

                    setTimeout(() => {
                        ghost.scared = false
                    },3000)
                })
            }
    }
```

### Displaying & Implementing Pellet Mechanics

Pellets are implemented similarly to PowerUps only real difference is that when the PacMan makes contact with them they add 10 points the the users score.

```
    for(let i = pellets.length-1; i>=0;i--)
    {
        const pellet = pellets[i]
        pellet.draw()
        if(Math.hypot(pellet.position.x-pacman.position.x,
            pellet.position.y-pacman.position.y) < pellet.radius + pacman.radius)
            {
                pellets.splice(i,1)
                score+=10
                scoreEl.innerHTML = 'Score: ' + score.toString()
            }

    }
```

### Ghost Movement & Collision Detection With Boundaries & PacMan
<img width="919" alt="Screenshot 2024-01-07 121223" src="https://github.com/justjoe6/Pacman/assets/68125991/e0bc3332-4360-4cab-baf7-013902effb11">


If a ghost collides with PacMan then the code checks first if the ghost is scared if it is then it is removed from the board and the list of ghosts, however, if it is not scared then the animation is stopped and a GAME OVER text is displayed on the screen. Ghost collision with Boundaries is similar to how it is implemented with PacMan only difference is that we keep track of previous and current collisions by the ghosts. Current collisions are added if they haven't been already to a collisions list then if the length of this list is greater than the previous collsions list then the previous collisions list is set equal to the collisions list. Since the collision list is reset if a ghosts list of previous collisions does not equal the current collisions(Meaning the collsions list is less than a ghosts previous collisions list) this means that a pathway has opened up and thus the code randomly decides whether the ghost takes that pathway or not. In other words from the available pathways the code chooses the direction the ghosts will move randomly based on the previous and current collisions.

```
 ghosts.forEach((ghost,i) => {
        ghost.update()
        
        if(Math.hypot(ghost.position.x-pacman.position.x,
            ghost.position.y-pacman.position.y) < ghost.radius + pacman.radius)
            {
                if(!ghost.scared)
                {
                    cancelAnimationFrame(animationId)
                    scoreEl.innerHTML='GAME OVER'
                }
                else
                {
                    ghosts.splice(i,1)
                }
            }

        const collisions = []
        boundaries.forEach(boundary => {
            if(!collisions.includes('right') && isCollision({circle:{...ghost, 
                velocity:{x:Ghost.speed,y:0}},rect:boundary}))
            {
                collisions.push('right')
            }
            if(!collisions.includes('left') && isCollision({circle:{...ghost, velocity:{x:-Ghost.speed,y:0
            }},rect:boundary}))
            {
                collisions.push('left')
            }
            if(!collisions.includes('up') && isCollision({circle:{...ghost, velocity:{x:0,y:-Ghost.speed
            }},rect:boundary}))
            {
                collisions.push('up')
            }
            if(!collisions.includes('down') && isCollision({circle:{...ghost, velocity:{x:0,y:Ghost.speed
            }},rect:boundary}))
            {
                collisions.push('down')
            }
        })
        if(collisions.length > ghost.prevCollisions.length)
        {
            ghost.prevCollisions = collisions
        }
        if(JSON.stringify(collisions) !== JSON.stringify(ghost.prevCollisions))
        {
            if(ghost.velocity.x > 0) ghost.prevCollisions.push('right')
            else if(ghost.velocity.x < 0) ghost.prevCollisions.push('left')
            else if(ghost.velocity.y > 0) ghost.prevCollisions.push('down')
            else if(ghost.velocity.y < 0) ghost.prevCollisions.push('up')
            const pathways = ghost.prevCollisions.filter(collision =>
                {
                    return !collisions.includes(collision)
                })
            const direction = pathways[Math.floor(Math.random()*pathways.length)]

            switch (direction) {
                case 'down':
                    ghost.velocity.y = Ghost.speed
                    ghost.velocity.x = 0
                    break
                case 'up':
                    ghost.velocity.y = -Ghost.speed
                    ghost.velocity.x = 0
                    break
                case 'left':
                    ghost.velocity.y = 0
                    ghost.velocity.x = -Ghost.speed
                    break
                case 'right':
                    ghost.velocity.y = 0
                    ghost.velocity.x = Ghost.speed
                    break
            }
            ghost.prevCollisions = []
        }

    })
}
```

## Win Condtiion
<img width="917" alt="Screenshot 2024-01-07 121450" src="https://github.com/justjoe6/Pacman/assets/68125991/c7fa2cfe-f2f1-4480-b360-b6c92cc5e1da">


If the pellets list is empty or in other words all pellets have been removed from the board then the animation ends and a you won text is displayed on the browser.
```
    if(pellets.length==0)
    {
        cancelAnimationFrame(animationId)
        scoreEl.innerHTML='you won'
    }
```

# Index.html

## Difficulty Buttons

When a difficulty button is pressed the difficulty() function from index.js is called with the arguments being the difficulty along with the button.
```
<button id = "ezBtn" class = "btn pad" onclick = "difficulty('easy',this)">EASY</button>
<button id = "hrdBtn" class = "btn pad" onclick = "difficulty('hard',this)">HARD</button>
<button id = "impBtn" class = "btn" onclick = "difficulty('imp',this)">IMPOSSIBLE</button>
```

The speed of ghosts is set based on the difficulty with a speed of 2 being easy a speed of 2.5 being hard and a speed of 5 being impossible. When the function is called all the difficulty buttons are hidden after the difficulty has been set.
```
function difficulty(diff)
  {

    if(diff == 'easy')
    {
        Ghost.speed=2
    }
    else if(diff == 'hard')
    {
        Ghost.speed=2.5
    }
    else if(diff == 'imp')
    {
        Ghost.speed=5
    }
    else
    {
        Ghost.speed=2
    }
    document.getElementById("ezBtn").style.display='none' 
    document.getElementById("hrdBtn").style.display='none' 
    document.getElementById("impBtn").style.display='none' 
  }
```

## Start Button

Start button calls the function start from index.js with the button as an argument.
```
<button class = "btn" onclick = "start(this)">START</button>
```
Here we set the display of all buttons on the browser to none such that they are no longer visible when the start button is pressed and then call the animate function to start the game. If the start button is pressed before any of the difficulty buttons then the default difficulty is easy.
```
function start(btn){
    btn.style.display='none'
    document.getElementById("ezBtn").style.display='none' 
    document.getElementById("hrdBtn").style.display='none' 
    document.getElementById("impBtn").style.display='none' 
    animate()
}
```

Inspiration: https://www.youtube.com/watch?v=5IMXpp3rohQ 
