---
layout: base
title: Fibonacci Sequence!
---

<input type="number" id="fibonacci_box" name="fibonacci_box" min="1" max="99">
<button onclick="fibonacciCall()">Click for Fibonacci</button>
<table id="fibonacci_data">
    <tr>
        <th>Method</th>
        <th>Execution Time (Nanoseconds)</th>
        <th>Result</th>
    </tr>
    <!--data goes here-->
</table>
<script>
    function fibonacciCall() {
        fetch("http://localhost:8115/api/fibonacci/" + String(document.getElementById("fibonacci_box").value), {
            method: 'GET',
            headers: {
                'Content-Type': 'application/json',
            }
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
            var names = ["For Loop", "While Loop", "Stream Loop", "Golden Ratio", "Matrix Exponentiation"];
            // var goldenRatioTime = data['golden_Ratio'];
            // var matrixExponentiationTime = data['matrix_Exponentiation'];
            // var methodArray = [goldenRatioTime, matrixExponentiationTime];
            for (var i = 0; i < names.length; i++) {
                var newRow = document.createElement("tr");
                var nameCol = document.createElement("td");
                nameCol.innerHTML = names[i];
                var executionCol = document.createElement("td");
                executionCol.innerHTML = String(data[names[i]]["executionTime"]);
                var resultCol = document.createElement("td");
                resultCol.innerHTML = String(data[names[i]]["result"]);
                newRow.appendChild(nameCol);
                newRow.appendChild(executionCol);
                newRow.appendChild(resultCol);
                document.getElementById("fibonacci_data").appendChild(newRow);
            }
        })
        .catch(error => {
            // handling errors
            console.error('Fetch error:', error);
        });
    }
</script>