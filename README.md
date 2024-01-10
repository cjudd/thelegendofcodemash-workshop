# The Legend of Codemash Workshop

Hands-on workshop for creating a Classic Legend of Zelda clone for Codemash using Phasor.io (https://phaser.io/) JavaScript game framework.

Ages - 10+  
Cost - $0

## Prerequisites

* Web Browser
* Internet connection
* [Visual Studio Code](https://code.visualstudio.com/) or another text editor
* Live Server (Visual Studio Code Extension)
* [Tiled](https://www.mapeditor.org/)

## Setup

1. Clone or download [https://github.com/cjudd/thelegendofcodemash](https://github.com/cjudd/thelegendofcodemash) repository.
2. Open the directory with Visual Studio Code.
3. Start Live Server by pressing Go Live on status bar. It should open a browser at [http://127.0.0.1:5500](http://127.0.0.1:5500) or some other port that will be listed in the status bar.

## Configure Game

The Classic Legend of Zelda was a top down role playing game (RPG). As such, we have make a minor change to the game config in GameConfig.js to remove gravity.

1. In GameConfig.js change gravity's y from 200 to 0.
2. Save file (ctrl+S).

##  Load Existing Map

To load the dungeon, we are going to create a Preloader class to load all assets such as the tiles that make up the dungeon. We will also edit the DungeonScene class to remove the old boilerplate code that made the logo fly. Then we will edit it to set up the dungeon visually on the canvas.

1. Edit Preloader.js. In the preload, loads the wall_sheet.png image and dungeon-01.json tile map. Then tell the create to go to the dungeon scene (DungeonScene).

```
class Preloader extends Phaser.Scene {
    constructor() {
        super('preloader')
    }

    preload() {
        this.load.image('tiles', '../../assets/tiles/wall_sheet.png')
        this.load.tilemapTiledJSON('dungeon', '../../assets/tiles/dungeon-01.json')
    }

    create() {
        this.scene.start('dungeon')
    }
}
```

2. Edit GameConfig.js. Add the Preloader as the first scene in the scene list.
```
scene: [Preloader,DungeonScene]
```

3. Edit DungeonScene.js. Clean the old code out of preload and create.

```
class DungeonScene extends Phaser.Scene {
    
	constructor() {
		super('dungeon')
	} // end constructor

	preload() {

    } // end preload

    create() {

    } //end create

    update(time, delta) {
        
    } //end update
        
}
```

3. Continue editing DungeonScene.js by adding to the create function the making of the tilemap, adding the tileset image, creating layers for ground and wall and configuring collision.
```
    create() {
        const map = this.make.tilemap({ key:'dungeon' })
        const tileset = map.addTilesetImage('wall_sheet', 'tiles')
        map.createLayer('ground', tileset)
        const wallsLayer = map.createLayer('walls', tileset)

        wallsLayer.setCollisionByProperty({ collides: true})
    } //end create
```

## Create Gearhead

Create the hero, Gearhead and make them run around the map.

### Display Hero

1. Edit DungeonScene. At the end of the create function, add the gearhead sprite.
```
// Display Gearhead
const gearhead = this.add.sprite(120,120,'gearhead','gearhead-walk-down-01.png')
```

### Animate Hero

1. Edit DungeonScene. Add the following after displaying the gearhead.
```
        // Animate Gearhead
        this.anims.create({
            key: 'gearhead-walk-down-01.png',
            frames: [{ key: 'gearhead', frame: 'gearhead-walk-down-01.png'}]
        })

        this.anims.create({
            key: 'gearhead-walk-down',
            frames: this.anims.generateFrameNames('gearhead', {start: 1, end: 3, prefix: 'gearhead-walk-down-0', suffix: '.png'} ),
            repeat: -1,
            frameRate: 15
        })

        this.anims.create({
            key: 'gearhead-idle-down',
            frames: [{ key: 'gearhead', frame:'gearhead-walk-down-01.png' }]
        })

        this.anims.create({
            key: 'gearhead-walk-up',
            frames: this.anims.generateFrameNames('gearhead', {start: 1, end: 3, prefix: 'gearhead-walk-up-0', suffix: '.png'} ),
            repeat: -1,
            frameRate: 15
        })

        this.anims.create({
            key: 'gearhead-idle-up',
            frames: [{ key: 'gearhead', frame:'gearhead-walk-up-01.png' }]
        })

        this.anims.create({
            key: 'gearhead-walk-left',
            frames: this.anims.generateFrameNames('gearhead', {start: 1, end: 4, prefix: 'gearhead-walk-left-0', suffix: '.png'} ),
            repeat: -1,
            frameRate: 15
        })

        this.anims.create({
            key: 'gearhead-idle-left',
            frames: [{ key: 'gearhead', frame:'gearhead-walk-left-01.png' }]
        })

        this.anims.create({
            key: 'gearhead-walk-right',
            frames: this.anims.generateFrameNames('gearhead', {start: 1, end: 4, prefix: 'gearhead-walk-right-0', suffix: '.png'} ),
            repeat: -1,
            frameRate: 15
        })

        this.anims.create({
            key: 'gearhead-idle-right',
            frames: [{ key: 'gearhead', frame:'gearhead-walk-right-01.png' }]
        })

        gearhead.anims.play('gearhead-idle-down')
```

### Move Hero

1. Edit Dungeon.js. Add cursor and gearhead fields after class declaration and before constructor.
```
    cursors;
    gearhead;
```
2. Change gearhead references in create function to be this.gearhead.
```
this.gearhead = this.physics.add.sprite(120,120,'gearhead','gearhead-walk-down-01.png')
```
```
this.gearhead.anims.play('gearhead-idle-down')
```
3. Add Cursor Key Inputs in preload function.
```
preload() {
    // Cursor Key Inputs
    this.cursors = this.input.keyboard.createCursorKeys();
} // end preload
```
4. Add hero movement by updating the update function.
```
    update(time, delta) {
        if (!this.cursors || !this.gearhead) { return }

		const speed = 100;
		if (this.cursors.left?.isDown) {
			this.gearhead.anims.play('gearhead-walk-left', true)
			this.gearhead.setVelocity(-speed, 0)
		} else if (this.cursors.right?.isDown) {
			this.gearhead.anims.play('gearhead-walk-right', true)
			this.gearhead.setVelocity(speed, 0)
		} else if (this.cursors.up?.isDown) {
			this.gearhead.anims.play('gearhead-walk-up', true)
			this.gearhead.setVelocity(0, -speed)
		} else if (this.cursors.down?.isDown) {
			this.gearhead.anims.play('gearhead-walk-down', true)
			this.gearhead.setVelocity(0, speed)
		} else {
			const parts = this.gearhead.anims.currentAnim.key.split('-')
			parts[1] = 'idle'
			this.gearhead.play(parts.join('-'))
			this.gearhead.setVelocity (0,0)
		}
    } //end update
```

### Add Collisions with walls

1. Edit DungeonScene.js. At the end of the create function add collision code.
```
this.physics.add.collider(this.gearhead, wallsLayer)
//debugDraw(wallsLayer, this)
```
2. To debug collisions, uncomment debugDraw line in DungeonScene and uncomment debug property in GameConfig.js.

3. Adjust the collision box around hero by adding body size at the end of create function.
```
// Collisions
this.gearhead.body.setSize(this.gearhead.width * 0.4, this.gearhead.height * 0.9)
this.gearhead.body.offset.y = 10
```
### Camera Follow

1. To make camera follow hero, add the following to theend of the create function.
```
// Camera Follow
this.cameras.main.startFollow(this.gearhead, true)
this.cameras.main.setBounds(0, 0, wallsLayer.displayWidth, wallsLayer.displayHeight);
```
