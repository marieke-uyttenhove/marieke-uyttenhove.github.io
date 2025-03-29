---
layout: page
title: Check PodoCloud afrekening
permalink: /check_afrekening/
---

<head>
    <!-- Include Bootstrap CSS -->
    <link href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css" rel="stylesheet">
</head>

{% raw %}
<form id="uploadForm" class="mt-4">
    <div class="mb-3">
        <label for="weekSelector" class="form-label">Select a week:</label>
        <div class="d-flex align-items-center">
            <button type="button" id="prevWeek" class="btn btn-secondary me-2">&larr;</button>
            <span id="weekDisplay" class="form-control text-center">Loading...</span>
            <button type="button" id="nextWeek" class="btn btn-secondary ms-2">&rarr;</button>
        </div>
    </div>
    <button type="button" onclick="uploadIndex()" class="btn btn-primary btn-lg mb-3">Scan</button>
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
    let currentWeekIndex = 0; // 0 for the current week, -1 for last week, etc.

    function updateWeekDisplay() {
        const today = new Date();
        const startOfWeek = new Date(today);
        const endOfWeek = new Date(today);

        // Calculate the start and end of the selected week
        startOfWeek.setDate(today.getDate() - today.getDay() + 1 + currentWeekIndex * 7); // Monday
        endOfWeek.setDate(today.getDate() - today.getDay() + 7 + currentWeekIndex * 7); // Sunday

        // Format the dates
        const options = { year: 'numeric', month: 'short', day: 'numeric' };
        const weekRange = `${startOfWeek.toLocaleDateString(undefined, options)} - ${endOfWeek.toLocaleDateString(undefined, options)}`;

        // Update the display
        document.getElementById("weekDisplay").textContent = weekRange;
    }

    document.getElementById("prevWeek").addEventListener("click", () => {
        currentWeekIndex--;
        updateWeekDisplay();
    });

    document.getElementById("nextWeek").addEventListener("click", () => {
        currentWeekIndex++;
        updateWeekDisplay();
    });

    async function uploadIndex() {
        // Show the loading spinner
        document.getElementById("loadingSpinner").style.display = "block";

        try {
            const formData = new FormData();
            formData.append("week_index", currentWeekIndex); // Send the week index as an integer

            const response = await fetch("https://podocloud-scraper-backend.onrender.com/upload", {
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

    // Initialize the week display
    updateWeekDisplay();
</script>
{% endraw %}