<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تطبيق عرض الأكواد</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/pyodide/v0.23.4/full/pyodide.js"></script>
    <style>
        body {
            font-family: 'Cairo', sans-serif;
            background-color: #f4f4f4;
            margin: 0;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        .container {
            width: 100%;
            max-width: 800px;
            background: white;
            padding: 20px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
            border-radius: 10px;
            text-align: center;
        }
        textarea {
            width: 100%;
            height: 100px;
            margin-bottom: 10px;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            resize: vertical;
        }
        button {
            padding: 10px 20px;
            background: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
            margin-top: 10px;
            transition: background 0.3s ease;
        }
        button:hover {
            background: #0056b3;
        }
        .output {
            display: none;
            width: 100%;
            height: 100vh;
            position: relative;
        }
        iframe {
            width: 100%;
            height: 100%;
            border: none;
        }
        .icon {
            color: #007bff;
            font-size: 2rem;
            margin-bottom: 10px;
        }
        .back-button {
            position: absolute;
            top: 10px;
            left: 10px;
            background: #dc3545;
        }
        .back-button:hover {
            background: #c82333;
        }
        .python-output {
            background: #f8f9fa;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 5px;
            margin-top: 10px;
            text-align: left;
            white-space: pre-wrap;
            font-family: monospace;
        }
        @media (max-width: 600px) {
            body {
                padding: 10px;
            }
            .container {
                padding: 15px;
            }
            button {
                font-size: 14px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="icon"><i class="fas fa-code"></i></div>
        <h2>تطبيق عرض الأكواد</h2>
        <textarea id="htmlInput" placeholder="أدخل كود HTML هنا"></textarea>
        <textarea id="cssInput" placeholder="أدخل كود CSS هنا"></textarea>
        <textarea id="jsInput" placeholder="أدخل كود JavaScript هنا"></textarea>
        <textarea id="pythonInput" placeholder="أدخل كود Python هنا"></textarea>
        <button onclick="renderCode()">عرض الأكواد</button>
    </div>
    <div class="output">
        <button class="back-button" onclick="goBack()"><i class="fas fa-arrow-left"></i> رجوع</button>
        <iframe id="resultFrame"></iframe>
        <div id="pythonResult" class="python-output"></div>
    </div>
    <script>
        async function renderCode() {
            const htmlCode = document.getElementById('htmlInput').value;
            const cssCode = document.getElementById('cssInput').value;
            const jsCode = document.getElementById('jsInput').value;
            const pythonCode = document.getElementById('pythonInput').value;

            if (!htmlCode && !cssCode && !jsCode && !pythonCode) {
                alert("الرجاء إدخال كود على الأقل في أحد الحقول.");
                return;
            }

            // عرض HTML, CSS, JS
            const combinedCode = `
                <style>${cssCode}</style>
                ${htmlCode}
                <script>${jsCode}<\/script>
            `;

            const iframe = document.getElementById('resultFrame');
            const iframeDoc = iframe.contentDocument || iframe.contentWindow.document;
            iframeDoc.open();
            iframeDoc.write(combinedCode);
            iframeDoc.close();

            // تنفيذ كود Python باستخدام Pyodide
            if (pythonCode) {
                await loadPyodide();
                let output = "";
                try {
                    const result = await pyodide.runPythonAsync(pythonCode);
                    output = result !== undefined ? result.toString() : "تم التنفيذ بنجاح بدون إخراج.";
                } catch (error) {
                    output = `خطأ: ${error.message}`;
                }
                document.getElementById('pythonResult').innerText = output;
            } else {
                document.getElementById('pythonResult').innerText = "";
            }

            document.querySelector('.container').style.display = 'none';
            document.querySelector('.output').style.display = 'block';
        }

        function goBack() {
            document.querySelector('.container').style.display = 'block';
            document.querySelector('.output').style.display = 'none';
        }

        // تحميل Pyodide عند بدء التطبيق
        let pyodide;
        async function loadPyodide() {
            if (!pyodide) {
                pyodide = await loadPyodide({ indexURL: "https://cdn.jsdelivr.net/pyodide/v0.23.4/full/" });
            }
        }
    </script>
</body>
</html>
