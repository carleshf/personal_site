---
title: "St. Jordi 2019 (Bostod Ed.)"
date: 2019-04-23T00:00:00+02:00
draft: false
tags:
  - St. Jordi
---

Saint Gorge is the patron of Catalonia (a northeast region in Spain). It is the national day of the region and it is celebrated by offering books and roses to your intimate and loved ones (more information about it on [this page](https://www.barcelona.de/en/barcelona-sant-jordi.html) from Barcelona's City council).

This is the first year outside Catalonia for this day. So, I made this super simple drawing in _HTML5_ and _javacript_ to celebrate this amazing Catalan festivity.
    
{{< rawhtml >}}
    <canvas id="canvas" width="500" height="500"></canvas>
    <style>
        * { padding: 0; margin: 0; }
        canvas { background: #fff; display: block; margin: 0 auto; }
        h1 { text-align: center; font-family: cursive; font-size: 24px; font-style: normal; font-variant: normal; font-weight: 700; line-height: 26.4px; }
  	</style>
    <h1>Feliç Diada de Sant Jordi</h1>
    <script type="text/javascript">
        const ctx = canvas.getContext("2d");
        const petal = [
            [ [0, 0],[0.3, -1], [0.7, -1], [1, 0], [0.7, 1], [0.3, 1], [0, 0] ],
            [ [0, 0], [1, 0] ],
        ];

        function drawPetal(path, width, height, col) {
            var ii = 0;
            do {
                const p = path[ii];
                let jj = 0;
                ctx.moveTo(p[jj][0] * width, p[jj++][1] * height);
                while (jj < p.length - 1) {
                    ctx.lineTo(p[jj][0] * width, p[jj++][1] * height);
                }
                if (p[jj][0] === p[0][0] && p[jj][1] === p[0][1]) {
                    ctx.fillStyle = col;
                    ctx.fill();
                    ctx.closePath();
                } else {
                    ctx.lineTo(p[jj][0] * width, p[jj][1] * height)
                }
            } while (++ii < path.length);
        }

        function drawPetals(x, y, count, col, startAt, petal, width, height) {
            const step = (Math.PI * 2) / count;
            ctx.setTransform(1, 0, 0, 1, x, y);
            ctx.rotate(startAt);
            for (var ii = 0; ii < count; ++ii) {
                drawPetal(petal, width, height, col);
                ctx.rotate(step);
            }
            ctx.setTransform(1, 0, 0, 1, 0, 0);
        }

        function drawFlower(col_border, col_fill, lineWidth, fitScale, petalCount) {
            ctx.strokeStyle = col_border;
            ctx.lineWidth = lineWidth;
            const size = Math.min(ctx.canvas.width, ctx.canvas.height) * fitScale * 0.5;
            ctx.beginPath();

            drawPetals(ctx.canvas.width / 2, ctx.canvas.height / 2, 5, col_fill, -Math.PI / 2, petal, size, size * 0.2);
            ctx.stroke();
            ctx.beginPath();
            ctx.arc(ctx.canvas.width / 2, ctx.canvas.height / 2, size * 0.15, 0, Math.PI * 2);
            ctx.fillStyle = col_border;
            ctx.fill();
        }

        drawFlower("crimson", "lightpink", 4, 0.95, 5);
    </script>
{{< /rawhtml >}}