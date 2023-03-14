<template>
    <div id="fenshu">分数：{}</div>
</template>

<script setup>
// 导入pixi.js
import * as PIXI from "pixi.js";
// 创建应用
const app = new PIXI.Application({
    width: window.innerWidth,
    height: window.innerHeight,
    backgroundColor: 'white',
    resolution: window.devicePixelRatio || 1,
    antialias: true // 抗锯齿
});
const frameWidth = 88;
const frameHeight = 100;

// 将应用画布添加到DOM中
document.body.appendChild(app.view);

// 创建一个容器
const container = new PIXI.Container()
app.stage.addChild(container)


// 创建一个纹理
const baseTexture = PIXI.BaseTexture.from('./textures/game.png')

// 恐龙纹理
const dinoTexture = new PIXI.Texture(
    baseTexture,
    new PIXI.Rectangle(75, 0, 88, 100)
)
// 创建恐龙精灵
const dino = new PIXI.Sprite(dinoTexture)
dino.visible = false
container.addChild(dino)

// 恐龙跑步动画
let runTextures = [];
for (let i = 0; i < 6; i++) {
    runTextures.push(
        new PIXI.Texture(
            baseTexture,
            new PIXI.Rectangle(1680 + i * frameWidth, 0, 82, frameHeight)
        )
    )
}
const runAnimation = new PIXI.AnimatedSprite(runTextures)
runAnimation.visible = false
runAnimation.animationSpeed = 0.2
runAnimation.play()

container.addChild(runAnimation)

//跳跃精灵
const jumpTexture = new PIXI.Texture(
    baseTexture,
    new PIXI.Rectangle(1680, 0, 82, frameHeight)
)

const jumpSprite = new PIXI.Sprite(jumpTexture)
jumpSprite.visible = false
jumpSprite.x = 60
jumpSprite.y = window.innerHeight - 140
container.addChild(jumpSprite)

// 创建地面精灵
const groundTexture = new PIXI.Texture(
    baseTexture,
    new PIXI.Rectangle(50, 100, 2300, 30)
)
const groundSprite = new PIXI.Sprite(groundTexture)
groundSprite.width = window.innerWidth * 2
groundSprite.height = 30
groundSprite.position.set(0, window.innerHeight - 50)
container.addChild(groundSprite)
// 创建仙人掌精灵
const cactusTexture = new PIXI.Texture(
    baseTexture,
    new PIXI.Rectangle(515, 0, 30, 60)
)
const cactusSprite = new PIXI.Sprite(cactusTexture)
cactusSprite.x = window.innerWidth / 2
cactusSprite.y = window.innerHeight - 110
container.addChild(cactusSprite)

// 创建文字
const startText = new PIXI.Text('开始游戏', {
    fontFamily: 'Arial',
    fontSize: 36,
    fill: 0x333333,
    align: "center"
})
startText.x = window.innerWidth / 2
startText.y = window.innerHeight / 2
startText.anchor.set(0.5)
container.addChild(startText)

startText.interactive = true
startText.on('click', () => {
    playGame()
})
let isGaming = false
function playGame() {
    isGaming = true
    startText.visible = false
    runAnimation.visible = true
    runAnimation.x = 60
    runAnimation.y = window.innerHeight - 140
}
let score = 0
let jumpSpeed = 20
let gravity = 1
let gameOver = false
let groundSpeed = 10
// 动画循环
app.ticker.add(() => {
    if (gameOver) return;
    if (isGaming) {
        cactusSprite.x -= groundSpeed
        groundSprite.x -= groundSpeed
        if (cactusSprite.x < 0) {
            cactusSprite.x = window.innerWidth
            score++
        }
        if (groundSprite.x < -window.innerWidth) {
            groundSprite.x = 0
        }
        if (jumpSprite.visible) {
            jumpSprite.y -= jumpSpeed
            jumpSpeed -= gravity
            if (jumpSprite.y > window.innerHeight - 140) {
                jumpSprite.y = window.innerHeight - 140
                jumpSpeed = 20
                runAnimation.visible = true
                jumpSprite.visible = false
            }
        }
        // 碰撞检测 
        if (
            jumpSprite.y > cactusSprite.y - 60 &&
            jumpSprite.x > cactusSprite.x - 60 &&
            jumpSprite.x < cactusSprite.x + 60
        ) {
            gameOverfn()
        }
    }
})
function gameOverfn() {
    gameOver = true
    startText.visible = false
    const endText = new PIXI.Text(`游戏结束，你的得分是${score}分！`, {
        fontFamily: 'Arial',
        fontSize: 36,
        fill: 0x333333,
        align: "center"
    })
    endText.x = window.innerWidth / 2
    endText.y = window.innerHeight / 2
    endText.anchor.set(0.5)
    container.addChild(endText)
    endText.interactive = true
    endText.on('click', () => {
        location.reload()
    })
}
window.addEventListener('keydown', (e) => {
    if (e.code === 'Space') {
        if (!isGaming) playGame()
        runAnimation.visible = false
        jumpSprite.visible = true
    }
})
</script>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

canvas {
    width: 100vw;
    height: 100vh;
    position: fixed;
    left: 0;
    top: 0;
}
</style>
