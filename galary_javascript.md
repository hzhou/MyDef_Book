# JavaScript

For JavaScript, let's look at a game I adapted from http://www.kittykatattack.com/ -- Monster Attack!

First, checkout the game at http://www.kittykatattack.com/gallery/monsterSmash/src/monsterSmashFinished.html

![monsterattack](http://www.kittykatattack.com/images/monsterSmash.jpg)

Here is the source code in MyDef:

```
page: monster_example, js_frame
    title: Monster Attack!
    subcode:: content
        CSS: .game {width: 600px; text-align: center; margin: auto; padding: 20px; border: 4px double gray}
        CSS: @font-face {font-family: Vanilla; src: url("http://www.kittykatattack.com/gallery/monsterSmash/fonts/Vanilla.ttf");}
        CSS: .game * {font-family: Vanilla; font-size: 16px;}
        CSS: #button {margin-bottom: 20px;}
        $div game
            $button #button, "Start"
            $div #stage, /
            $p #output, /

    subcode: js_onload
        $map load_element, stage, output, button
        $call setup_slots
        $call load_tilesheet

        $global SPEED=120, MAXTIME=10000
        $global n_time=0
        $global b_game_running=0

        &call listen, button, click
            $if b_game_running==0
                $if n_time>MAXTIME
                    n_time=0
                    n_hit=0
                &call setInterval, timer_game, SPEED
                    n_time+=SPEED
                    $if n_time>MAXTIME
                        $call game_end
                    $for i=0:ROWS*COLS
                        $call update_monster, slots[i]
                    $call render
                b_game_running=1
                button.innerHTML="Pause"
            $else
                clearInterval(timer_game)
                b_game_running=0
                button.innerHTML="Start"

    subcode: render
        $for i=0:ROWS*COLS
            $call draw_monster, slots[i]
        tn_time=Math.floor((MAXTIME-n_time)/1000)
        output.innerHTML="Monsters smashed: $n_hit  Time left: $tn_time"

    subcode: game_end
        clearInterval(timer_game)
        b_game_running=0
        button.innerHTML="Restart"

    subcode: setup_slots
        $global COLS=4, ROWS=3, SIZE=128, SPACE=10, WAIT=30
        $global slots=[]
        $for i=0:ROWS
            $for j=0:COLS
                CSS: #stage {width:542; height:404; position: relative; margin: auto}
                CSS: canvas.slot {position: absolute; width: 128px; height: 128px; border: 1px solid black; display: block}
                var canvas=document.createElement("canvas")
                canvas.setAttribute("class", "slot")
                canvas.setAttribute("width", SIZE)
                canvas.setAttribute("height", SIZE)
                canvas.style.top=i*(SIZE+SPACE)+"px"
                canvas.style.left=j*(SIZE+SPACE)+"px"
                stage.appendChild(canvas)
                &call listen, canvas, mousedown
                    $call @hit_monster

                var surface=canvas.getContext("2d")
                var wait=Math.floor(Math.random()*WAIT)

                var monster={
                    canvas: canvas,
                    surface: surface,
                    frame: 0,
                    wait: wait,
                    dir: 1,
                }
                slots.push(monster)

    subcode: load_tilesheet
        button.disabled=true
        $global image_monster
        image_monster=new Image()
        &call listen, image_monster, load
            button.disabled=false
            render()
        image_monster.src="http://www.kittykatattack.com/gallery/monsterSmash/images/monsterTileSheet.png"

    subcode: draw_monster(obj)
        var obj=$(obj)
        var x=Math.floor(obj.frame % 3) * SIZE
        var y=Math.floor(obj.frame / 3) * SIZE
        obj.surface.drawImage(image_monster, x, y, SIZE, SIZE, 0, 0, SIZE, SIZE)

    subcode: update_monster(obj)
        var obj=$(obj)
        $if obj.wait>1
            obj.wait--
        $elif obj.frame==6 # HIT!
            obj.frame=0
            obj.dir=1
            obj.wait=Math.floor(Math.random()*WAIT)
        $elif obj.dir>0
            $if obj.frame==5
                obj.dir=-1
            $else
                obj.frame++
        $elif obj.dir<0
            $if obj.frame==0
                obj.dir=1
                obj.wait=Math.floor(Math.random()*WAIT)
            $else
                obj.frame--

    subcode: hit_monster
        $global n_hit=0
        var obj=event.target
        $for i=0:ROWS*COLS
            $if slots[i].canvas===obj
                var tn_frame=slots[i].frame
                $if tn_frame>0 && tn_frame<6
                    slots[i].frame=6
                    slots[i].wait=2
                    n_hit++

#----------------------------------------
jscode: load_element(name)
    $global $(name)
    $(name) = document.querySelector("#$(name)");
```


