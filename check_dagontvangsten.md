---
layout: page
title: Check dagontvangsten
permalink: /check_dagontvangsten/
---

{% raw %}
<form id="uploadForm">
    <input type="file" id="fileInput" class="form-control mb-3">
    <button type="button" onclick="uploadFile()" class="btn btn-primary">Upload</button>
</form>

<h3 class="mt-4">Revenue Overview</h3>
<pre id="output"></pre>

<!-- <script>
    async function uploadFile() {
        const fileInput = document.getElementById("fileInput");
        if (!fileInput.files.length) {
            alert("Please select a file.");
            return;
        }

        const formData = new FormData();
        formData.append("file", fileInput.files[0]);

        const response = await fetch("https://check-dagontvangsten-backend.onrender.com/upload", {
            method: "POST",
            body: formData
        });

        const result = await response.json();
        document.getElementById("output").textContent = JSON.stringify(result, null, 2);
    }
</script> -->
<script>
    async function uploadFile() {
        const fileInput = document.getElementById("fileInput");
        if (!fileInput.files.length) {
            alert("Please select a file.");
            return;
        }

        const formData = new FormData();
        formData.append("file", fileInput.files[0]);

        try {
            const response = await fetch("https://check-dagontvangsten-backend.onrender.com/upload", {
                method: "POST",
                body: formData
            });

            if (!response.ok) {
                throw new Error(`HTTP error! Status: ${response.status}`);
            }

            const result = await response.json();
            console.log("Response received:", result);
            displayTable(result);  // Call function to render table
        } catch (error) {
            console.error("Upload failed:", error);
            document.getElementById("output").innerHTML = `<div class="text-danger">Error: ${error.message}</div>`;
        }
    }

    function displayTable(data) {
        if (!data.length) {
            document.getElementById("output").innerHTML = "<p>No data available.</p>";
            return;
        }

        let table = "<table class='table table-bordered'><thead><tr>";

        // Create table headers from JSON keys
        Object.keys(data[0]).forEach(key => {
            table += `<th>${key}</th>`;
        });
        table += "</tr></thead><tbody>";

        // Add table rows
        data.forEach(row => {
            table += "<tr>";
            Object.values(row).forEach(value => {
                table += `<td>${value}</td>`;
            });
            table += "</tr>";
        });

        table += "</tbody></table>";

        document.getElementById("output").innerHTML = table;
    }
</script>
{% endraw %}