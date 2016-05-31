# letter-blast-simple-html5-canvas-game
A classic very simple typing game :D lets see how good you are with keyboard :D :D 

i just included the source here as it would be easier for those who wana have a look.

https://riazxrazor-graphics.herokuapp.com/letterblast.html

### Source code
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Blast The Letter</title>
	<style type="text/css">
		html,body{
			margin:0;
            width:100%;
            height:auto;
            background:#000;
		}

		canvas{
			display:block;
		}
        .score{
					position: absolute;
					right:2%;
					top:10px;
					color:#fff;
					font-size:18px;
					z-index:999;
        }

        .health{
					position: absolute;
					left:75px;
					top:15px;
					height:10px;
					width:15%;
					background:rgba(255,255,255,0.2);
					z-index:999;
					color:#fff;
        }

        .health > div{
					height:10px;
					background:rgb(102, 204, 255);
					width:100%;
					box-shadow:0 0 9px 0px rgb(102, 204, 255)
        }
        #container{
					position:absolute;
					width:50%;
					height:50%;
					top:0;
					left:0;
					right:0;
					bottom:0;
					background:rgba(255,255,255,0.6);
					margin:auto auto;
					text-align:center;
					padding: 20px;
					padding-top:1%;
					z-index:99999;
        }

        #gamestart button,#gameover button{
					display: inline-block;
	padding: 0px 9px;
	margin-right: 4px;
	border-radius: 3px;
	border: solid 1px #c0c0c0;
	background: #e9e9e9;
	box-shadow: inset 0px 1px 0px rgba(255,255,255, .8), 0px 1px 3px rgba(0,0,0, .1);
	font-size: 4em;
	font-weight: bold;
	text-decoration: none;
	color: #717171;
	text-shadow: 0px 1px 0px rgba(255,255,255, 1);
					margin-bottom:5px;
        }
				#gamestart button:hover,#gameover button:hover{
				    background: #fefefe;
				    background: -webkit-gradient(linear, 0% 0%, 0% 100%, from(#FEFEFE), to(#f0f0f0));
				    background: -moz-linear-gradient(0% 0% 270deg,#FEFEFE, #f0f0f0);
				    cursor: pointer;
				}

        #gamestart p{
            font-size:20px;
        }

        #gameover{
            display:none;
        }
        #gameover p{
           font-size:20px;
        }
	</style>
</head>
<body>
 <div class="score">
     Score : <span id="score">0</span>
 </div>
 <div class="health">
     <div id="health"></div>
 </div>
 <div id="container">
     <div id="gameover">
        <h1>
            GAME OVER <br>
            You Scored : <span id="afterscore">0</span>
         </h1>

         <button onclick="start_game();">
             Play again
         </button>
     </div>
     <div id="gamestart">
         <p>The game is simple press the letter on your keyboard inside the falling sphers to destroy them.
         Don't let the letter sphere hit the bottom.
         </p>
         <button onclick="start_game();">
             Start Game
         </button>
     </div>
 </div>
	<script>

        var canvas = document.createElement("canvas"),
			width = canvas.width = window.innerWidth,
			height = canvas.height = window.innerHeight,
			ctx = canvas.getContext("2d");

        var key = null,
             keypress = false,
             radius = 20,
             lastLettreSpawn = 0,
             letterRate = 5000,
             particles = [],
             enemyexplosionradius = 100,
             score = 0,
             scoreStep = 10,
             health = 100,
             healthloss = 10,
             updatehealth = false,
             updatescore = false,
             gameover = false,
             destroyed = 0,
            mouseclick = false,
            mouse = {
                x:0,
                y:0
            };
        var letters = generate(2,function(){
                          return {
                              x:Math.random(0,width)*width,
                              y:0,
                              speed : 0.5 + Math.random(0.5,1)*(1-0.5),
                              color:"#fff",
                              letter : randomLetter()

                          }
                      });

        function generate(length,fn)
        {
            var arr = [];
            for(var i = 0; i < length; i++)
            {
                    arr.push(fn());
            }

            return arr;
        }

        function randomLetter()
        {
            var text = "";
            var possible = "abcdefghijklmnopqrstuvwxyz";


                text = possible.charAt(Math.floor(Math.random() * possible.length));

            return text;
        }

        function map_range(value, low1, high1, low2, high2) {
			    return low2 + (high2 - low2) * (value - low1) / (high1 - low1);
			}
	  window.onload = function(){
			document.body.appendChild(canvas);

        document.body.addEventListener('keydown',function(e){
             key = e.keyCode;
            keypress = true;
        },false);

          document.body.addEventListener('keyup',function(e){
             keypress = false;
        },false);





          //update();




	}

 function generate_explosion(x,y)
			{
				var p = generate(50,function(){
					return {
						radius : 1,
						x:x,
						y:y,
						vx:0.5,
						vy:0.5,
						opacity:1,
						explosionradius:1,
						explosionDone:false
					}
				});

				particles.push(p);
			}

        function render_explosion()
			{

				for(var i = 0; i < particles.length; i++)
					{

						for(var j = 0; j < particles[i].length; j++)
						{
						 // draw explosion particls //
						ctx.beginPath();
						var alpha = map_range(particles[i][j].explosionradius,1,enemyexplosionradius,1,0);
						ctx.fillStyle = "rgba(255,255,255,"+alpha+")";

		var x = particles[i][j].x + particles[i][j].explosionradius * Math.cos(2 * Math.PI * j / particles[i].length);
		var y = particles[i][j].y + particles[i][j].explosionradius * Math.sin(2 * Math.PI * j / particles[i].length);
						ctx.arc(x,y,particles[i][j].radius,Math.PI*2,false);
						ctx.fill();
						 // draw explosion particls end //

							////////////////////////
							particles[i][j].explosionradius += 1;
							if(particles[i][j].explosionradius > enemyexplosionradius)
							{
								particles[i][j].explosionDone = true;
							}

							if(particles[i][j].explosionDone)
							{
								particles.splice(i,1);
								break;
							}
							//////////////////////////
					};

				}
			}

          function update_health()
          {
            document.getElementById('health').style.width  = health+'%';
          }

          function update_score()
          {
              document.getElementById('score').innerHTML  = score;
              document.getElementById('afterscore').innerHTML  = score;
          }


function update(){



            ctx.fillStyle = "rgba(0,0,0,0.2)";
            ctx.fillRect(0,0,width,height);


            for(var i = 0; i < letters.length; i++)
            {

                ctx.beginPath();
                ctx.fillStyle=letters[i].color;
                ctx.arc(letters[i].x,letters[i].y,radius,Math.PI*2,false);
                ctx.fill();

                ctx.fillStyle= "#000";
                  ctx.font =  "bold "+radius+"px serif";
                  ctx.textBaseline = "top";
                  ctx.fillText(letters[i].letter, letters[i].x-radius/4 ,letters[i].y-radius/2);

                if(keypress)
                {

                    if(letters[i].letter == String.fromCharCode(key).toLowerCase())
                    {
                        ctx.beginPath();
                        ctx.strokeStyle= "#fff";
                        ctx.moveTo(letters[i].x,height);
                        ctx.lineTo(letters[i].x,letters[i].y);
                        ctx.stroke();
                        generate_explosion(letters[i].x,letters[i].y);
                        letters.splice(i,1);
                        score += scoreStep;
                        destroyed++;

                        updatescore = true;
                        continue;
                    }
                }

                letters[i].y += letters[i].speed;

                if(letters[i].y+10 > height)
                {
                    updatehealth = true;
                    health -= healthloss;
                    letters[i].x = Math.random(0,width)*width;
                    letters[i].y = 0;
                    letters[i].speed =  0.5 + Math.random(0.5,2)*(2-0.5);
                    letters[i].letter =  randomLetter();
                 }
            }

           var now = Date.now() ;
           	if (now - lastLettreSpawn  > letterRate){
           	lastLettreSpawn = now;
            letters.push({
                              x:Math.random(0,width)*width,
                              y:0,
                              speed : 1 + Math.random(1,2)*(2-1),
                              color:"#fff",
                              letter : randomLetter()

                          });
            }

            if(destroyed >= 10)
            {
                lastLettreSpawn = now;
                destroyed = 0;
                letterRate -= 500;
            }

            if(letterRate <= 500)
            {
                letterRate = 500;
            }

            if(letters.length === 0)
            {
                letters.push({
                              x:Math.random(0,width)*width,
                              y:0,
                              speed : 0.5 + Math.random(0.5,1)*(1-0.5),
                              color:"#fff",
                              letter : randomLetter()

                          })
            }

            render_explosion();
            if(updatehealth)
            {
                updatehealth = false;
                update_health();
            }

            if(updatescore)
            {
                console.log(health);
                updatescore = false;
                update_score();
            }

            if(health <= 0)
            {
                update_health();
                gameover = true;
            }

            if(!gameover)
            {
			 requestAnimationFrame(update);
            }
            else
            {
                game_over();
            }
		}

    function start_game()
    {
            gameover = false;
             score = 0
             scoreStep = 10
             health = 100
             updatehealth = false,
             updatescore = false,
             gameover = false;
            letterRate = 5000;
            letters = generate(2,function(){
                          return {
                              x:Math.random(0,width)*width,
                              y:0,
                              speed : 0.5 + Math.random(0.5,1)*(1-0.5),
                              color:"#fff",
                              letter : randomLetter()

                          }
                      });
        document.getElementById('gamestart').style.display = "none";
        document.getElementById('container').style.display = "none";
        update_health();
        update_score();
        update();


    }

    function game_over()
    {
        document.getElementById('container').style.display = "block";
        document.getElementById('gameover').style.display = "block";
    }

	</script>
</body>
</html>


```
