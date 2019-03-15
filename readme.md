# Tetris DOM

This is tetris app built as a personal challange in JavaScript using only DOM elements. It is fairly easy building tetris in Canvas but it is a bit more complicated in pure browser. 

Tetris has all of the functionalities of other tetris games, including tetrabrick preview and score based on completed lines.

## Logic

In order for everything to work as intended HTML table has been used for tracking positions of tetrabricks and table. Each time movement is performed table is drawn again with no performance loss. Each tetrabrick has its own color and all possible shapes kept in object as an array. Here is example of one tetrabrick:

**I - tetrabrick**

    I: [
            [
                1, 1, 1, 1,
                0, 0, 0, 0,
                0, 0, 0, 0,
                0, 0, 0, 0
            ],
            [
                1, 0, 0, 0,
                1, 0, 0, 0,
                1, 0, 0, 0,
                1, 0, 0, 0
            ]
        ]

    - 1s are representing taken spots and zeros empty spots. Starting table in the beginging is filled with nothing but zeros until, that is, first tetrabricks start comming down from the sky.

- Each time tetrabrick is moved several checks are made. If they pass, tetrabrick is moved and next move is made. If not, brick has not moved and we need to see if it has stopped or it just cant be moved in that direction but has not stopped. If it has stopped we check for any completed lines and add score accordingly or end game if needed.

- We are making difference between moving tetrabricks down, when there is an obstacle we stop it and moving tetrabricks horizontally and rotating when nothing happens if there is an obstacle and tetrabrick can be moved again. All checks bellow are perfromed mostly in the same way, just that end result is different. Same code is used for both cases whenever possible.

- First, to see if tetrabrick is out of table bounds. Now, as each tetrabrick is a square, 2x2, 3x3 or 4x4 that shouldnt be hard, but we need to allow zeros to go out of bounds and 1s not. In order to do that separate function is needed to calculate taken width and heigth of the square. Instead of checking that whole square, we are now checking just taken spots.

- Method for calculating real tetrabrick width:

        function shapeWidth(shape) {
            let substraction = 0;
            return shape.reduce(function (acc, item, index) {
                if ((index + 1) % 4 === 0)++substraction;

                if (item === 1) {
                    if ((index + 1) % 4 === 0) acc = 4;
                    else if ((index + 1 - substraction) % 3 === 0 && acc < 3) acc = 3;
                    else if ((index + 1) % 2 === 0 && acc < 2) acc = 2;
                };

                return acc;
            }, 1);
        };

    - This function is not made to be universal as that would take too much time. There are limited number of shapes in tetris and it works with those perfectly.

- Main method for move validation:

    function checkIfValidMove(shapePosition, shape) {
        let currentShapeWidth = shapeWidth(shape);
        let currentShapeHeight = shapeHeight(shape);

        if (shapePosition[1] + emptyLinesInShapeLeft(shape) < 0 || shapePosition[1] + currentShapeWidth - 1 >= config.tableWidth) {
            return false;
        };

        if (shapePosition[0] + currentShapeHeight > config.tableHeight) {
            return false;
        };

        if (checkForOtherShapes(shapePosition, shape)) {
            return false;
        };

        return true;
    };

- If shape is not out of table bounds we are not checking to see if there are other shapes from before standing there blocking way. If any of our 1s are in a space of some other 1 in that table place we know move is false and are not allowing it.

- Scoring is done depending on how much lines have been completed with one tetrabrick. More lines - more points.

- Main values can be changed in config object:

        config = {
            speed: 600,
            level: 1,
            tableWidth: 10,
            tableHeight: 20,
            destroyedLinesBonus: 2
        };

- Main logic is not complex, as can be seen. Biggest problem here was that not all elements in DOM were behaving as we would expect and that is why so many lines of code here. Also, this tetris has been done in two days and third one to fix some minor bugs in behavior. Other updates were minor and most not related to logic itself.

## Tehnologies

Whole logic is in tetris.js file. Back End part is almost non-existent and serves only as a bare minimum so web page can be avilable online for testing. High scores is kept using Local Storage only. Everything is Client Side here.

### Created by Stefan Deak