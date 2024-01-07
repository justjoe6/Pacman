# Index.js

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

## Defining and Creating Maze
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


