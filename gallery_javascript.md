# JavaScript

This example is slightly longer. I would like to do something interesting, as well as showcase MyDef's capability in handling complexity.

MyDef:

```
macros:
    size: 200

page: js_example, js_frame
    title: JavaScript Example
    subcode:: content
        CSS: div.box {margin: 0 auto; text-align: center}
        $div box
            symmetry: <input type="text" id="symmetry_input" size=4>
        $tag canvas, #canvas, /

    subcode: js_onload
        $map load_element, symmetry_input
        symmetry_input.value=5+""
        $call 2d_init, canvas
        &call listen, symmetry_input, keyup
            $if event.which == 13
                $call math_set_alpha, parseFloat(symmetry_input.value)
                $call draw_reset

        &call animate
            $call draw_step, dt/1000

#---------------------------------------- 
subcode: 2d_init(canvas)
    $call load_element, $(canvas)
    canvas.width=window.innerWidth
    canvas.height=window.innerHeight
    $global surface
    surface=canvas.getContext("2d")
    surface.lineWidth=5
    surface.strokeStyle='#aaaaaa'
    surface.lineJoin="round"

    $global ox, oy, radius
    ox=canvas.width/2
    oy=$(size)/2
    radius=$(size)/2*0.8
    $call math_set_alpha, 0
    $call math_step, 0

subcode: draw_reset
    surface.clearRect(0,0, canvas.width, canvas.height)
    $call math_step, 0

subcode: draw_step(delta_time_in_second)
    $global n_lines=0
    $if n_lines>symmetry
	surface.clearRect(0,0, canvas.width, canvas.height)
	n_lines=0

    #-- draw $(speed) pixels per second, df is the one pixel fraction
    $(set:speed=$(size)/2)
    cf+=df*$(speed)*($(delta_time_in_second))
    surface.beginPath()
    surface.moveTo(x0+(x1-x0)*0.9, y0+(y1-y0)*0.9)
    surface.lineTo(x1, y1)
    $if cf>1.0
	surface.lineTo(x2,y2)
	beta1=beta2
	$call math_step, 1
	n_lines++
    $else
	surface.lineTo(x1+(x2-x1)*cf, y1+(y2-y1)*cf)
    surface.stroke();

macros:
    C: Math.cos($1)
    S: Math.sin($1)
    Pythogrean: Math.sqrt(($1)*($1)+($2)*($2))
    Pi: Math.PI

subcode: math_set_alpha(symmetry)
    $global alpha, symmetry=5
    symmetry=$(symmetry)
    $if isNaN(symmetry) || symmetry<2 || symmetry>100
	symmetry=5
    alpha=$(Pi)/symmetry

subcode: math_step(mode)
    $global beta1, beta2, x1, y1, x2, y2
    $global cf, df
    $(if:mode=0)
        beta1=-$(Pi)/2
        cf=0
        x0=ox+radius*$(C:beta1)
        y0=oy+radius*$(S:beta1)
    $(else)
        x0=x1
        y0=y1
    beta2=beta1+($(Pi)-alpha)
    x1=ox+radius*$(C:beta1)
    y1=oy+radius*$(S:beta1)
    x2=ox+radius*$(C:beta2)
    y2=oy+radius*$(S:beta2)
    df=1.0/$(Pythogrean:x2-x1, y2-y1)
    cf=0.0

#---------------------------------------- 
subcode: test_draw
    surface.beginPath()
    surface.moveTo(0, 0)
    surface.lineTo(ox, oy)
    surface.stroke()

```

Compile:
```
$ mydef_page -mwww js_example.def
PAGE: js_example
  --> [./js_example.html]
```

The JavaScript output:

```
<!doctype html>
<html>
    <head>
        <meta charset="utf-8">
        <title>JavaScript Example</title>
        <style>
            div.box  {margin: 0 auto; text-align: center}
        </style>
        <script type="text/javascript">
            var symmetry_input;
            var canvas;
            var surface;
            var ox;
            var oy;
            var radius;
            var alpha;
            var symmetry=5;
            var beta1;
            var beta2;
            var x1;
            var y1;
            var x2;
            var y2;
            var cf;
            var df;
            var time_old=0;
            var n_lines=0;
            window.onload = function(){
                symmetry_input = document.querySelector("#symmetry_input");
                symmetry_input.value=5+"";
                canvas = document.querySelector("#canvas");
                canvas.width=window.innerWidth;
                canvas.height=window.innerHeight;
                surface=canvas.getContext("2d");
                surface.lineWidth=5;
                surface.strokeStyle='#aaaaaa';
                surface.lineJoin="round";
                ox=canvas.width/2;
                oy=200/2;
                radius=200/2*0.8;
                symmetry=0;
                if(isNaN(symmetry) || symmetry<2 || symmetry>100){
                    symmetry=5;
                }
                alpha=Math.PI/symmetry;
                beta1=-Math.PI/2;
                cf=0;
                x0=ox+radius*Math.cos(beta1);
                y0=oy+radius*Math.sin(beta1);
                beta2=beta1+(Math.PI-alpha);
                x1=ox+radius*Math.cos(beta1);
                y1=oy+radius*Math.sin(beta1);
                x2=ox+radius*Math.cos(beta2);
                y2=oy+radius*Math.sin(beta2);
                df=1.0/Math.sqrt((x2-x1)*(x2-x1)+(y2-y1)*(y2-y1));
                cf=0.0;
                symmetry_input.addEventListener("keyup", function(event){
                    if(event.which == 13){
                        symmetry=parseFloat(symmetry_input.value);
                        if(isNaN(symmetry) || symmetry<2 || symmetry>100){
                            symmetry=5;
                        }
                        alpha=Math.PI/symmetry;
                        surface.clearRect(0,0, canvas.width, canvas.height);
                        beta1=-Math.PI/2;
                        cf=0;
                        x0=ox+radius*Math.cos(beta1);
                        y0=oy+radius*Math.sin(beta1);
                        beta2=beta1+(Math.PI-alpha);
                        x1=ox+radius*Math.cos(beta1);
                        y1=oy+radius*Math.sin(beta1);
                        x2=ox+radius*Math.cos(beta2);
                        y2=oy+radius*Math.sin(beta2);
                        df=1.0/Math.sqrt((x2-x1)*(x2-x1)+(y2-y1)*(y2-y1));
                        cf=0.0;
                    }
                }, false);
                function animate_update(time){
                    var dt=time-time_old;
                    if(dt>30){
                        if(n_lines>symmetry){
                            surface.clearRect(0,0, canvas.width, canvas.height);
                            n_lines=0;
                        }
                        cf+=df*200/2*(dt/1000);
                        surface.beginPath();
                        surface.moveTo(x0+(x1-x0)*0.9, y0+(y1-y0)*0.9);
                        surface.lineTo(x1, y1);
                        if(cf>1.0){
                            surface.lineTo(x2,y2);
                            beta1=beta2;
                            x0=x1;
                            y0=y1;
                            beta2=beta1+(Math.PI-alpha);
                            x1=ox+radius*Math.cos(beta1);
                            y1=oy+radius*Math.sin(beta1);
                            x2=ox+radius*Math.cos(beta2);
                            y2=oy+radius*Math.sin(beta2);
                            df=1.0/Math.sqrt((x2-x1)*(x2-x1)+(y2-y1)*(y2-y1));
                            cf=0.0;
                            n_lines++;
                        }
                        else{
                            surface.lineTo(x1+(x2-x1)*cf, y1+(y2-y1)*cf);
                        }
                        surface.stroke();
                        time_old=time;
                    }
                    window.requestAnimationFrame(animate_update);
                }
                animate_update(0);
            };
        </script>
    </head>
    <body>
        <div class="box">
            symmetry: <input type="text" id="symmetry_input" size=4>
        </div>
        <canvas id="canvas"></canvas>
    </body>
</html>
```
And in browser:

![counterpage](js_line.png)


