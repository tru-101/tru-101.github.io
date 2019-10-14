<html>
  <head>
  <style>
    input
    {
        display: block;
        margin: 4px;  
        width: 100px;
    }
    
    button
    {
        margin: 4px;        
    }
  </style>
  </head>
  <body>
    <div>
        <table>
            <tr><td>Длительность дня (в часах)</td><td><input type="text" id="i_day" value="24"></td></tr>
            <tr><td>Длительность года (в днях)</td><td><input type="text" id="i_year" value="365"></td></tr>
            <tr><td>Оборот вокруг своей оси</td><td><input type="text" id="i_star_day" disabled value="22"></td></tr>
            <tr><td>Текущее время</td><td><input type="text" id="i_time" disabled value="22"></td></tr>
            <tr><td>Угол</td><td><input type="text" id="i_angle" disabled value="0"></td></tr>
        </table>
   
        <button id="go">Применить</button>
    </div>
  
    Управление временем:
    <div>
        <button id="faster">быстрее</button>
        <button id="slower">медленнее</button>
        <button id="negative">обратно</button>
        <button id="pause">пауза</button>
    </div>
    <canvas id="canvas" width="800" height="400"></canvas>
    <script>
        var context;

        var moment=0;
        var delta=0.5*3600;
        var day=24*3600;
        var year=365*24*3600;
        
        var starDay=day*year/(year+day);
        
        var is_pause=false;
        
        var t;
        var a;
        var b;

        var sun_r=35;
        var planet_r=25;
        var orbit=140;
        var owch_r=4;
        
        var bubble = []
        
        function create_bubble(phi, d, r, color)
        {
            this.phi=phi;
            this.d=d;
            this.r=r;
            this.color=color;
        }
        
        pause.onclick =function()
        {
            is_pause=!is_pause;
            //requestAnimationFrame(drawFrame);
        }
        faster.onclick =function(){delta*=2;}
        slower.onclick =function(){delta*=0.5;}
        negative.onclick =function(){delta*=-1.0;}
        go.onclick =function()
        {
            day=3600*i_day.value;
            year=day*i_year.value;
            starDay=day*year/(year+day);
            i_star_day.value=time_to_string(starDay);
            moment=0;
            is_pause=false;
            //requestAnimationFrame(drawFrame);
        }

        window.onload = function() 
        {
            bubble.push(new create_bubble(0.3, 0.8, 0.2, "#007F00"));
            bubble.push(new create_bubble(1, 0.5, 0.4, "#007F00"));
            bubble.push(new create_bubble(1.8, 0.7, 0.2, "#007F00"));
            bubble.push(new create_bubble(3, 0.3, 0.25, "#007F00"));
            bubble.push(new create_bubble(3.9, 0.8, 0.2, "#007F00"));
            bubble.push(new create_bubble(5, 0.9, 0.1, "#007F00"));
            
            i_star_day.value=time_to_string(starDay);
            context = canvas.getContext("2d");  
            t=performance.now();
            requestAnimationFrame(drawFrame)
        }
        
        function time_to_string(t)
        {
            var cm=t;
            var od=Math.floor(cm/day);
            cm-=od*day;
            var oh=Math.floor(cm/3600);
            cm-=oh*3600;
            var om=Math.floor(cm/60);
            
            return (od!=0?(od+"д "):"")+(oh<10?"0":"")+oh+":"+(om<10?"0":"")+om;        
        }
        
        function drawScene(x, y, alpha)
        {
            context.rotate(alpha);
            context.translate(x, y);
            
            
            context.beginPath();
            context.arc(0, 0, sun_r, 0, 2.0*Math.PI);
            context.fillStyle = "#FFFF00";
            context.fill();
            context.stroke();
	
            context.beginPath();
            context.arc(orbit*Math.cos(a), orbit*Math.sin(a), planet_r, a+0.5*Math.PI, a+1.5*Math.PI);
            context.fillStyle = "#007FFF";
            context.fill();
            context.stroke();
            
            context.beginPath();
            context.arc(orbit*Math.cos(a), orbit*Math.sin(a), planet_r, a-0.5*Math.PI, a+0.5*Math.PI);
            context.fillStyle = "#003FAF";
            context.fill();
            context.stroke();
            
                        
            for(var i=0; i<bubble.length; i++)
            {
                context.beginPath();
                context.arc(orbit*Math.cos(a)+planet_r*bubble[i].d*Math.cos(b+bubble[i].phi), orbit*Math.sin(a)+planet_r*bubble[i].d*Math.sin(b+bubble[i].phi), bubble[i].r*planet_r, 0, 2*Math.PI);
                context.fillStyle = bubble[i].color;
                context.fill();
                context.stroke();
            }

            context.beginPath();
            context.arc(orbit*Math.cos(a)+planet_r*Math.cos(b), orbit*Math.sin(a)+planet_r*Math.sin(b), owch_r, 0, 2*Math.PI);
            context.fillStyle = "#FFFFFF";
            context.fill();
            context.stroke();
           
            
            context.translate(-x, -y);
            context.rotate(-alpha);
        }
        
        function drawFrame(nt) 
        {
            if(is_pause) 
            {
                t=nt;
                requestAnimationFrame(drawFrame);
                return;
            }
            
            moment+=delta*(nt-t)/1000.0;
            t=nt;
            
            // вращение против часовой стрелки - отрицательное направление.
            a=-2*Math.PI*(moment/year);
            b=-2*Math.PI*(moment/starDay);
            
            context.clearRect(0, 0, canvas.width, canvas.height);
            
            context.translate(canvas.width/4, canvas.height/2);
            drawScene(0, 0, 0);
            context.translate(canvas.width/2, 0);
            drawScene(-orbit*Math.cos(a), -orbit*Math.sin(a), -b+Math.PI*1.5);
            context.translate(-3*canvas.width/4, -canvas.height/2);
            
            i_time.value=time_to_string(moment);
            i_angle.value=(-a*180.0/Math.PI)%360;
            
            requestAnimationFrame(drawFrame);
        }
    </script>
  </body>
</html>
    
