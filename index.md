---
layout: base
title: Student Home 
description: Home Page
hide: true
---

My journey starts here- Kickin' **Projects 1.01 Daksha Gowda**

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Mario Game</title>
    <link rel="stylesheet" href="style.css">
    <style>
        .sprite {
            height: 256px;
            width: 256px;
            background-image: url('{{sprite_file}}');
            background-repeat: no-repeat;
        }
        #mario {
            background-position: calc({{animations[0].col}} * -256px) calc({{animations[0].row}} * -256px);
            position: absolute;
        }
    </style>
</head>
<body>
    <p id="mario" class="sprite"></p>
    <script>
        var mario_metadata = {};
        {% for key in hash %}
        mario_metadata["{{key | first}}"] = {
            row: {{key.row}},
            col: {{key.col}},
            frames: {{key.frames}}
        };
        {% endfor %}

        class Mario {
            constructor(meta_data) {
                this.tID = null;
                this.positionX = 0;
                this.currentSpeed = 0;
                this.marioElement = document.getElementById("mario");
                this.pixels = 256;
                this.interval = 100;
                this.obj = meta_data;
                this.marioElement.style.position = "absolute";
            }

            animate(obj, speed) {
                let frame = 0;
                const row = obj.row * this.pixels;
                this.currentSpeed = speed;

                this.tID = setInterval(() => {
                    const col = (frame + obj.col) * this.pixels;
                    this.marioElement.style.backgroundPosition = `-${col}px -${row}px`;
                    this.marioElement.style.left = `${this.positionX}px`;

                    this.positionX += speed;
                    frame = (frame + 1) % obj.frames;

                    const viewportWidth = window.innerWidth;
                    if (this.positionX > viewportWidth - this.pixels) {
                        document.documentElement.scrollLeft = this.positionX - viewportWidth + this.pixels;
                    }
                }, this.interval);
            }

            startWalking() {
                this.stopAnimate();
                this.animate(this.obj["Walk"], 3);
            }

            startRunning() {
                this.stopAnimate();
                this.animate(this.obj["Run1"], 6);
            }

            startPuffing() {
                this.stopAnimate();
                this.animate(this.obj["Puff"], 0);
            }

            startCheering() {
                this.stopAnimate();
                this.animate(this.obj["Cheer"], 0);
            }

            startFlipping() {
                this.stopAnimate();
                this.animate(this.obj["Flip"], 0);
            }

            startResting() {
                this.stopAnimate();
                this.animate(this.obj["Rest"], 0);
            }

            stopAnimate() {
                clearInterval(this.tID);
            }
        }

        const mario = new Mario(mario_metadata);

        window.addEventListener("keydown", (event) => {
            if (event.key === "ArrowRight") {
                event.preventDefault();
                mario.currentSpeed === 0 ? mario.startWalking() : mario.startRunning();
            } else if (event.key === "ArrowLeft") {
                event.preventDefault();
                mario.startPuffing();
            }
        });

        window.addEventListener("touchstart", (event) => {
            event.preventDefault();
            mario.currentSpeed === 0 ? mario.startWalking() : mario.startRunning();
        });

        window.addEventListener("blur", () => {
            mario.stopAnimate();
        });

        window.addEventListener("focus", () => {
            mario.startFlipping();
        });

        document.addEventListener("DOMContentLoaded", () => {
            const scale = window.devicePixelRatio;
            document.querySelector(".sprite").style.transform = `scale(${0.2 * scale})`;
            mario.startResting();
        });
    </script>
</body>
</html>