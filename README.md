<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<title>Toshtín - Guerra Imperial</title>
<style>
body { margin:0; background:black; color:white; text-align:center; font-family:Arial;}
canvas { display:block; margin:auto; background:#111;}
h1 { color:gold;}
</style>
</head>
<body>

<h1>👑 TOSHTÍN: GUERRA IMPERIAL</h1>
<p>← → moverte | ESPACIO disparar | SHIFT ralentizar tiempo</p>

<canvas id="game" width="800" height="400"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

/* ESTADO */
let player, enemies, bullets, score, lives, slowTime, gameOver;

/* INICIAR */
function init(){
player = {x:370,y:300,w:50,h:50,speed:6};
enemies = [];
bullets = [];
score = 0;
lives = 3;
slowTime = 0;
gameOver = false;
}
init();

/* CONTROLES */
let keys = {};
document.addEventListener("keydown",e=>keys[e.key]=true);
document.addEventListener("keyup",e=>keys[e.key]=false);

/* SPAWN ENEMIGOS */
function spawnEnemy(){
enemies.push({
x:Math.random()*760,
y:0,
w:40,
h:40,
speed:2 + Math.random()*2 + score*0.05
});
}

/* UPDATE */
function update(){

if(gameOver) return;

/* MOVIMIENTO */
if(keys["ArrowLeft"] && player.x>0) player.x-=player.speed;
if(keys["ArrowRight"] && player.x<750) player.x+=player.speed;

/* DISPARO */
if(keys[" "]){
bullets.push({x:player.x+20,y:player.y,w:10,h:20});
keys[" "] = false;
}

/* RALENTIZAR TIEMPO */
let speedFactor = 1;
if(keys["Shift"] && slowTime < 100){
speedFactor = 0.4;
slowTime += 1;
}else{
slowTime -= 0.5;
if(slowTime<0) slowTime=0;
}

/* ENEMIGOS */
enemies.forEach((e,i)=>{
e.y += e.speed * speedFactor;

/* COLISION CON JUGADOR */
if(
e.x < player.x + player.w &&
e.x + e.w > player.x &&
e.y < player.y + player.h &&
e.y + e.h > player.y
){
enemies.splice(i,1);
lives--;
}

/* SALEN DE PANTALLA */
if(e.y > 400){
enemies.splice(i,1);
lives--;
}
});

/* BALAS */
bullets.forEach((b,i)=>{
b.y -= 8;

enemies.forEach((e,j)=>{
if(
b.x < e.x + e.w &&
b.x + b.w > e.x &&
b.y < e.y + e.h &&
b.y + b.h > e.y
){
enemies.splice(j,1);
bullets.splice(i,1);
score++;
}
});

if(b.y<0) bullets.splice(i,1);
});

/* GAME OVER */
if(lives <= 0){
gameOver = true;
}
}

/* DIBUJO */
function draw(){
ctx.clearRect(0,0,800,400);

/* JUGADOR */
ctx.fillStyle="gold";
ctx.fillRect(player.x,player.y,player.w,player.h);

/* ENEMIGOS */
ctx.fillStyle="red";
enemies.forEach(e=>ctx.fillRect(e.x,e.y,e.w,e.h));

/* BALAS */
ctx.fillStyle="cyan";
bullets.forEach(b=>ctx.fillRect(b.x,b.y,b.w,b.h));

/* HUD */
ctx.fillStyle="white";
ctx.fillText("Puntos: "+score,10,20);
ctx.fillText("Vidas: "+lives,10,40);
ctx.fillText("Tiempo lento: "+Math.floor(slowTime),10,60);

/* GAME OVER */
if(gameOver){
ctx.fillStyle="white";
ctx.font="30px Arial";
ctx.fillText("💀 GAME OVER",300,200);
ctx.font="20px Arial";
ctx.fillText("Presiona R para reiniciar",290,240);
}
}

/* LOOP */
function loop(){
update();
draw();
requestAnimationFrame(loop);
}
loop();

/* SPAWN LOOP */
setInterval(()=>{
if(!gameOver) spawnEnemy();
},1000);

/* REINICIAR */
document.addEventListener("keydown",e=>{
if(e.key==="r" && gameOver){
init();
}
});
</script>

</body>
</html>
