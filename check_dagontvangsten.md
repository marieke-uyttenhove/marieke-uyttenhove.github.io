---
layout: page
title: Check dagontvangsten
permalink: /check_dagontvangsten/
---

<head>
    <!-- Include Bootstrap CSS -->
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
</head>

{% raw %}
<form id="uploadForm" class="mt-4">
    <div class="mb-3">
        <input type="file" id="fileInput" class="form-control">
    </div>
    <button type="button" onclick="uploadFile()" class="btn btn-primary btn-lg mb-3">Upload</button>
</form>

<!-- Placeholder element to put output in -->
<div id="output"></div>

<!-- Loading spinner -->
<div id="loadingSpinner" class="spinner-border text-primary mt-3" role="status" style="display: none;">
    <span class="sr-only">Loading...</span>
</div>

<!-- Include marked.js library -->
<script src="https://cdn.jsdelivr.net/npm/marked/marked.min.js"></script>

<script>
    async function uploadFile() {
        const fileInput = document.getElementById("fileInput");
        if (!fileInput.files.length) {
            alert("Please select a file.");
            return;
        }

        const formData = new FormData();
        formData.append("file", fileInput.files[0]);

        // Show the loading spinner
        document.getElementById("loadingSpinner").style.display = "block";

        try {
            const response = await fetch("https://check-dagontvangsten-backend.onrender.com/upload", {
                method: "POST",
                body: formData
            });

            if (!response.ok) {
                throw new Error(`HTTP error! Status: ${response.status}`);
            }

            const result = await response.text(); // Get the markdown string
            console.log("Response received:", result);
            displayMarkdown(result);  // Call function to render markdown
        } catch (error) {
            console.error("Upload failed:", error);
            document.getElementById("output").innerHTML = `<div class="text-danger">Error: ${error.message}</div>`;
        } finally {
            // Hide the loading spinner
            document.getElementById("loadingSpinner").style.display = "none";
        }
    }

    function displayMarkdown(markdown) {
        const html = marked.parse(markdown); // Convert markdown to HTML
        document.getElementById("output").innerHTML = html;
    }
</script>
{% endraw %}