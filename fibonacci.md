---
layout: default
search_exclude: false
permalink: /fibonacci/
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
        fetch("http://localhost:8085/api/fibonacci/" + String(document.getElementById("fibonacci_box").value), {
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
            var result = data['result'];
            var names = ["Golden Ratio", "Matrix Exponentiation"];
            var goldenRatioTime = data['golden_Ratio'];
            var matrixExponentiationTime = data['matrix_Exponentiation'];
            var methodArray = [goldenRatioTime, matrixExponentiationTime];
            for (var i = 0; i < methodArray.length; i++) {
                var newRow = document.createElement("tr");
                var nameCol = document.createElement("td");
                nameCol.innerHTML = names[i];
                var executionCol = document.creteElement("td");
                executionCol.innerHTML = String(methodArray[i]);
                var resultCol = document.createElement("td");
                resultCol.innerHTML = String(result);
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