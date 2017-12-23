# GameInLife.github.io
<!DOCTYPE html>
<html>
<head>
    <style>
        .divCell {
            position: absolute;
            width: 10px;
            height: 10px;
            border-width: 1px;
            border-style: dotted;
            border-color: black;
            background: white;
        }

        #universe {
            position: relative;
            margin: 20px auto;
        }

        .buttons {
            width: 80%;
            margin: 0 auto;
        }
    </style>
    <script>
        var DEAD = 0,
            ALIVE = 1,
            CELL_X_COUNT = 60,
            CELL_Y_COUNT = 60,
            TIMEOUT = 100,
            interval;

        var temporaryField = createField();

        function createField() {
            var field = new Array();
            for (var i = 0; i < CELL_Y_COUNT; i++) {
                field.push(new Array(CELL_X_COUNT));
                for (var j = 0; j < CELL_X_COUNT; j++) {
                    field[i][j] = DEAD;
                }
            }
            return field;
        }

        function createUniverse() {
            var universe = document.getElementById('universe');
            for (var i = 0; i < CELL_X_COUNT; i++) {
                for (var j = 0; j < CELL_Y_COUNT; j++) {
                    var element = document.createElement('div');
                    element.className = 'divCell';
                    element.style.left = (i * 11) + 'px';
                    element.style.top = (j * 11) + 'px';
                    element.id = "x" + i + "y" + j;
                    element.setAttribute("state", DEAD);
                    universe.appendChild(element);
                }
            }
        }

        function cellClick(event) {
            if (event.target.tagName == "DIV") {
                if (event.target.getAttribute("state") == DEAD) {
                    event.target.setAttribute("state", ALIVE);
                    event.target.style.background = "green";
                }
                else {
                    event.target.setAttribute("state", DEAD);
                    event.target.style.background = "white"
                }
            }
            event.stopPropagation();
        }

        function getCellState(x, y) {
            if (x < 0 || x >= CELL_X_COUNT || y < 0 || y >= CELL_Y_COUNT) {
                return null;
            }
            var cell = document.getElementById("x" + x + "y" + y);
            return cell.getAttribute("state");
        }

        function setCellState(x, y, deadOrAlive) {

            if (x < 0 || x >= CELL_X_COUNT || y < 0 || y >= CELL_Y_COUNT) {
                return;
            }
            var cell = document.getElementById("x" + x + "y" + y);
            cell.setAttribute("state", deadOrAlive);
            cell.style.background =
                deadOrAlive == DEAD ? "white" : "green";
        }

        function lifeStep() {
            actuallyCompute();
            copyArray();
        }

        function actuallyCompute() {
            for (var i = 0; i < CELL_Y_COUNT; i++) {
                for (var j = 0; j < CELL_X_COUNT; j++) {
                    var neighboursCount = getNeighboursCount(i, j);
                    var cellState = getCellState(i, j);
                    if (cellState == ALIVE && (neighboursCount === 2 || neighboursCount === 3))
                        temporaryField[i][j] = ALIVE;
                    else if (cellState == DEAD && neighboursCount === 3)
                        temporaryField[i][j] = ALIVE;
                    else
                        temporaryField[i][j] = DEAD;
                }
            }
        }

        function getNeighboursCount(i, j) {
            var count = 0;
            for (var di = -1; di < 2; di++) {
                for (var dj = -1; dj < 2; dj++) {
                    if (di !== 0 || dj !== 0) {
                        var ai = i + di;
                        var aj = j + dj;
                        if (ai >= 0 && ai < CELL_X_COUNT && aj >= 0 && aj < CELL_Y_COUNT)
                            if (getCellState(ai, aj) == ALIVE)
                                count += 1;
                    }
                }
            }
            return count;
        }

        function copyArray() {
            for (var i = 0; i < CELL_Y_COUNT; i++)
                for (var j = 0; j < CELL_X_COUNT; j++)
                    setCellState(i, j, temporaryField[i][j]);
        }

        function startLife() {
            var iterationPerSecond = document.getElementById('input');
            interval = setInterval(lifeStep, iterationPerSecond.value * 1000);
        }

        function pauseLife() {
            clearInterval(interval);
        }

        function killLife() {
            pauseLife();
            for (var i = 0; i < CELL_Y_COUNT; i++) {
                for (var j = 0; j < CELL_X_COUNT; j++)
                    setCellState(i, j, DEAD);
            }
        }

    </script>
</head>
<body onload="createUniverse();" onclick="cellClick(event)">
    <div style="margin:10px auto; width:800px;">
        <h1>Conway's game of life</h1>
        <div state="0" class="buttons">
            <input type="text" id="input" />
            <button onclick="startLife()">Start Life</button>
            <button onclick="pauseLife()">Pause Life</button>
            <button onclick="lifeStep()">Life Step</button>
            <button onclick="killLife()">Kill Life</button>
        </div>
        <div id='universe'></div>
    </div>
</body>
</html>
