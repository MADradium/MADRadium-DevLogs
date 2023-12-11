---
layout: default
search_exclude: false
permalink: /bigSim/
---

<div id="box_container">
  <!--contains boxes-->
</div>
<div id="color_options" class="button_container" style="display: none;">
    Which color would you like to sort by?
    <button class="sort_button" onclick="setWhichColor(0)">Red</button>
    <button class="sort_button" onclick="setWhichColor(1)">Green</button>
    <button class="sort_button" onclick="setWhichColor(2)">Blue</button>
</div>
<div id="sort_options" class="button_container" style="display: none;">
    Which type of sort would you like to simulate?
    <button class="sort_button" onclick="sortCall('bubble')">Bubble Sort</button>
    <button class="sort_button" onclick="sortCall('selection')">Selection Sort</button>
    <button class="sort_button" onclick="sortCall('insertion')">Insertion Sort</button>
    <button class="sort_button" onclick="sortCall('merge')">Merge Sort</button>
</div>
<table id="data_container" style="display: none; justify-content: center;">
    <tr>
        <th>Sort Type</th>
        <th>Time Complexity</th>
        <th>Average Time Elapsed (Nanoseconds)</th>
        <th>Number of Comparisons</th>
        <th>Number of Swaps/Insertions</th>
    </tr>
    <!--contains data from the sort-->
</table>
<style>
    #box_container {
        margin: 0px;
        padding: 0px;
        justify-content: center;
        text-align: center;
    }
    #data_container {
        display: none;
        margin-top: 2%;
        padding-bottom: 10%;
    }
    .button_container {
        display: flex;
        justify-content: space-around;
        text-align: center;
        margin-top: 1%;
    }
    .sort_button {
        font-size: 20px;
    }
    .row {
        display: flex;
    }
    .box {
        width: 10px;
        height: 10px;
        border: 0.5px solid black;
        background-color: #F792B3;
        padding: 0px;
        margin: 0px;
    }
</style>
<script>
    var cont = document.getElementById("box_container");
    var colorData = {};
    var whichColor = 0;
    const sortTypes = ["bubble", "selection", "insertion", "merge"];
    function getRandomNumber(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }
    function capitalizeFirstLetter(word) {
        return word.charAt(0).toUpperCase() + word.slice(1);
    }
    function setWhichColor(color) {
        document.getElementById("color_options").style.display = "none";
        whichColor = color;
        document.getElementById("sort_options").style.display = "flex";
    }
    function createBoxes() {
        var currentIndex = 0;
        for (let i = 0; i < 50; i++) {
            var newDiv = document.createElement("div");
            newDiv.setAttribute("id", "box_row_" + String(i));
            newDiv.setAttribute("class", "row");
            cont.appendChild(newDiv);
            for (let j = 0; j < 100; j++) {
                var box = document.createElement("div");
                box.setAttribute("class", "box");
                box.setAttribute("id", String(currentIndex));
                // generating random RGB values
                var red = getRandomNumber(0, 255);
                var green = getRandomNumber(0, 255);
                var blue = getRandomNumber(0, 255);
                colorData[currentIndex] = [red, green, blue];
                // setting the background color of the element
                box.style.backgroundColor = `rgb(${red}, ${green}, ${blue})`;
                newDiv.appendChild(box);
                currentIndex++;
            }
        }
        document.getElementById("color_options").style.display = "flex";
    }
    function sortCall(sortType) {
        document.getElementById("sort_options").style.display = "none";
        var requestBody = {
            rgbData: colorData,
            color: whichColor,
            sortType: sortType,
            big: true
        };
        //"https://palettepuzzle.stu.nighthawkcodingsociety.com/api/sortingAlgorithm/"
        fetch("http://localhost:8115/api/sortingAlgorithm/", {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json',
            },
            body: JSON.stringify(requestBody, (key, value) => {
                // Convert keys to strings
                if (typeof key === 'number') {
                    return key.toString();
                }
                return value;
            })
        })
        .then(response => {
            if (!response.ok) {
                throw new Error(`HTTP error! Status: ${response.status}`);
            }
            return response.json();
        })
        .then(data => {
            // handling the response data
            console.log(data);
            steps = data['steps'];
            if (sortType != "merge") {
                simulateSort(steps);
            } else {
                simulateMergeSort(steps);
            }
            for (var type of sortTypes) {
                var newRow = document.createElement("tr");
                var nameCol = document.createElement("td");
                nameCol.innerHTML = capitalizeFirstLetter(type);
                var complexityCol = document.createElement("td");
                if (type != "merge") {
                    complexityCol.innerHTML = "O(n<sup>2</sup>)";
                } else {
                    complexityCol.innerHTML = "O(nlog(n))";
                }
                var timeCol = document.createElement("td");
                timeCol.innerHTML = String(data[type]["averageTime"]);
                var compCol = document.createElement("td");
                compCol.innerHTML = String(data[type]['comparisons']);
                var swapCol = document.createElement("td");
                swapCol.innerHTML = String(data[type]['swaps']);
                newRow.appendChild(nameCol);
                newRow.appendChild(complexityCol);
                newRow.appendChild(timeCol);
                newRow.appendChild(compCol);
                newRow.appendChild(swapCol);
                document.getElementById("data_container").appendChild(newRow);
            }
            document.getElementById("data_container").style.display = "block";
        })
        .catch(error => {
            // handling errors
            console.error('Fetch error:', error);
        });
    }
    function simulateSort(steps) {
        var index = 1;
        var intervalId = setInterval(() => {
            if (index < steps.length) {
                for (var i = 0; i < steps[index].length; i++) {
                    document.getElementById(String(i)).style.backgroundColor = `rgb(${colorData[steps[index][i]][0]}, ${colorData[steps[index][i]][1]}, ${colorData[steps[index][i]][2]})`;
                }
                index++;
            } else {
                clearInterval(intervalId);
            }
        }, 0.01);
    }
    function simulateMergeSort(steps) {
        var index = 0;
        var frontIndex = 0;
        var intervalId = setInterval(() => {
            if (index < steps.length) {
                var currentStep = steps[index];
                frontIndex = Math.min(...currentStep); // the minimum index in the array is the starting point
                for (var j = 0; j < currentStep.length; j++) {
                    document.getElementById(String(frontIndex)).style.backgroundColor = `rgb(${colorData[currentStep[j]][0]}, ${colorData[currentStep[j]][1]}, ${colorData[currentStep[j]][2]})`;
                    frontIndex++;
                }
                index++;
            } else {
                clearInterval(intervalId);
            }
        }, 0.01);
    }
    createBoxes();
    //for (var i = 0; i < 5000; i++) {
    //     document.getElementById(String(steps[steps.length - 1][i])).style.backgroundColor = `rgb(${colorData[steps[steps.length - 1][i]][0]}, ${colorData[steps[steps.length - 1][i]][1]}, ${colorData[steps[steps.length - 1][i]][2]})`;
    // }
    // // var frontIndex = 0;
    // // var dataIndex = 0;
    // // var intervalId = setInterval(() => {
    // //     if (dataIndex < steps.length) {
    // //         for (var i = 0; i < steps[dataIndex].length; i++) {
    // //             document.getElementById(String(frontIndex)).style.backgroundColor = `rgb(${colorData[steps[dataIndex][i]][0]}, ${colorData[steps[dataIndex][i]][0]}, ${colorData[steps[dataIndex][i]][0]})`;
    // //             frontIndex++;
    // //         }
    // //         dataIndex++;
    // //     } else {
    // //         clearInterval(intervalId);
    // //     }
    // // }, 0.01)
</script>