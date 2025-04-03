<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Wistia Video Asset URL Extractor</title>
    <style>
      body {
        font-family: 'Arial', sans-serif;
        background-color: #f4f7fc;
        margin: 0;
        padding: 0;
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
      }

      .container {
        background-color: #fff;
        border-radius: 12px;
        padding: 20px;
        box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
        width: 80%;
        max-width: 1000px;
        box-sizing: border-box;
      }

      h1 {
        text-align: center;
        font-size: 24px;
        color: #333;
        margin-bottom: 20px;
      }

      .input-container {
        display: flex;
        justify-content: center;
        margin-bottom: 20px;
      }

      label {
        font-size: 16px;
        color: #555;
        margin-right: 10px;
      }

      input[type="text"] {
        width: 60%;
        padding: 12px;
        border-radius: 8px;
        border: 1px solid #ddd;
        font-size: 16px;
        margin-right: 10px;
        transition: border-color 0.3s ease;
      }

      input[type="text"]:focus {
        border-color: #007bff;
        outline: none;
      }

      button {
        padding: 12px 20px;
        background-color: #007bff;
        color: white;
        border-radius: 8px;
        border: none;
        cursor: pointer;
        font-size: 16px;
        transition: background-color 0.3s ease;
      }

      button:hover {
        background-color: #0056b3;
      }

      table {
        width: 100%;
        border-collapse: collapse;
        margin-top: 20px;
      }

      table,
      th,
      td {
        border: 1px solid #ddd;
        border-radius: 8px;
      }

      th,
      td {
        padding: 12px;
        text-align: left;
        font-size: 14px;
      }

      th {
        background-color: #f2f2f2;
        color: #333;
      }

      td {
        background-color: #fafafa;
      }

      .copy-btn,
      .download-btn {
        margin-left: 10px;
        cursor: pointer;
        color: #007bff;
        border: none;
        background: none;
        font-size: 14px;
      }

      .copy-btn:hover,
      .download-btn:hover {
        text-decoration: underline;
      }

      .copied {
        background-color: #d4edda;
        color: #155724;
        border: 1px solid #c3e6cb;
      }

      @media screen and (max-width: 768px) {
        .container {
          width: 90%;
        }

        input[type="text"] {
          width: 50%;
        }
      }
    </style>
  </head>
  <body>
    <div class="container">
      <h1>Wistia Video Asset URL Extractor</h1>
      <div class="input-container">
        <label for="videoId">Video ID:</label>
        <input type="text" id="videoId" placeholder="Enter Wistia video ID" />
        <button onclick="getUrls()">Get URLs</button>
      </div>

      <table id="assetsTable">
        <thead>
          <tr>
            <th>Name</th>
            <th>Size</th>
            <th>Width</th>
            <th>Height</th>
            <th>URL</th>
            <th>Copy URL</th>
            <th>Download</th>
          </tr>
        </thead>
        <tbody></tbody>
      </table>
    </div>

    <script>
      async function getUrls() {
        const videoId = extractWvideoValue(document.getElementById("videoId").value);
        if (!videoId) {
          alert("Please enter a video ID");
          return;
        }

        const response = await fetch(`https://fast.wistia.net/embed/iframe/${videoId}?videoFoam=true`);
        const text = await response.text();

        // Extract JSON using a regular expression
        const jsonMatch = text.match(/iframeInit\((.*?), \{\}/);
        if (!jsonMatch) {
          alert("Failed to extract JSON data");
          return;
        }

        const jsonData = jsonMatch[1];
        let assets;
        try {
          const data = JSON.parse(jsonData);
          assets = data.assets;
        } catch (error) {
          alert("Failed to parse JSON data");
          return;
        }

        // Update table with asset data
        const tableBody = document.getElementById("assetsTable").getElementsByTagName("tbody")[0];
        tableBody.innerHTML = ""; // Clear previous results

        assets.forEach((asset) => {
          const row = tableBody.insertRow();
          row.insertCell(0).innerText = asset.display_name || "N/A";
          row.insertCell(1).innerText = formatSize(asset.size) || "N/A";
          row.insertCell(2).innerText = asset.width || "N/A";
          row.insertCell(3).innerText = asset.height || "N/A";

          let extension = asset.ext ? "." + asset.ext : ".mp4";
          asset.url && (asset.url = asset.url.replace(".bin", extension));

          const urlCell = row.insertCell(4);
          const urlText = document.createElement("span");
          urlText.innerText = asset.url || "N/A";
          urlCell.appendChild(urlText);

          const copyButtonCell = row.insertCell(5);
          if (asset.url) {
            const copyButton = document.createElement("button");
            copyButton.innerText = "Copy URL";
            copyButton.className = "copy-btn";
            copyButton.onclick = () => copyToClipboard(copyButton, asset.url);
            copyButtonCell.appendChild(copyButton);
          }

          const downloadButtonCell = row.insertCell(6);
          if (asset.url) {
            const downloadButton = document.createElement("a");
            downloadButton.target = "_blank";
            downloadButton.setAttribute("download", asset.display_name || "download");
            downloadButton.innerText = "Download";
            downloadButton.className = "download-btn";
            downloadButton.href = asset.url;
            downloadButton.download = asset.display_name || "download";
            downloadButtonCell.appendChild(downloadButton);
          }
        });
      }

      function formatSize(size) {
        if (!size) return "N/A";
        if (size >= 1048576) {
          return (size / 1048576).toFixed(2) + " MB";
        } else if (size >= 1024) {
          return (size / 1024).toFixed(2) + " KB";
        } else {
          return size + " bytes";
        }
      }

      function copyToClipboard(button, text) {
        navigator.clipboard.writeText(text).then(
          () => {
            button.innerText = "Copied";
            button.classList.add("copied");
            setTimeout(() => {
              button.innerText = "Copy URL";
              button.classList.remove("copied");
            }, 3000);
          },
          (err) => {
            console.error("Failed to copy URL: ", err);
          }
        );
      }

      function extractWvideoValue(url) {
        const match = url.match(/wvideo=([\w\d]+)/);
        return match ? match[1] : url;
      }
    </script>
  </body>
</html>
