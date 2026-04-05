<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ShareDrop | Envio de Arquivos</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap');
        body { font-family: 'Inter', sans-serif; }
    </style>
</head>
<body class="bg-slate-900 text-white min-h-screen flex items-center justify-center p-6">

    <div class="max-w-md w-full bg-slate-800 rounded-2xl shadow-2xl p-8 border border-slate-700">
        <div class="text-center mb-8">
            <h1 class="text-3xl font-bold text-blue-400">ShareDrop</h1>
            <p class="text-slate-400 mt-2">Envie arquivos e gere links temporários instantaneamente.</p>
        </div>

        <div id="drop-area" class="border-2 border-dashed border-slate-600 rounded-xl p-10 text-center hover:border-blue-500 transition-colors cursor-pointer">
            <input type="file" id="fileElem" class="hidden">
            <label for="fileElem" class="cursor-pointer">
                <svg class="mx-auto h-12 w-12 text-slate-500 mb-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
                </svg>
                <span class="text-sm font-medium">Clique para selecionar ou arraste o arquivo</span>
            </label>
        </div>

        <div id="progress-container" class="hidden mt-6">
            <div class="w-full bg-slate-700 rounded-full h-2.5">
                <div id="progress-bar" class="bg-blue-500 h-2.5 rounded-full" style="width: 0%"></div>
            </div>
            <p class="text-xs text-center mt-2 text-slate-400">Fazendo upload...</p>
        </div>

        <div id="result" class="hidden mt-6 p-4 bg-slate-700 rounded-lg break-all">
            <p class="text-xs text-slate-400 mb-1">Seu link está pronto:</p>
            <a id="download-link" href="#" target="_blank" class="text-blue-300 hover:underline font-mono"></a>
            <button onclick="copyLink()" class="mt-3 w-full bg-blue-600 hover:bg-blue-500 py-2 rounded-lg font-semibold transition">Copiar Link</button>
        </div>
    </div>

    <script>
        const fileElem = document.getElementById("fileElem");
        const progressContainer = document.getElementById("progress-container");
        const progressBar = document.getElementById("progress-bar");
        const resultDiv = document.getElementById("result");
        const downloadLink = document.getElementById("download-link");

        fileElem.addEventListener("change", function(e) {
            const file = e.target.files[0];
            if (file) uploadFile(file);
        });

        function uploadFile(file) {
            const formData = new FormData();
            formData.append("file", file);

            // Reset UI
            progressContainer.classList.remove("hidden");
            resultDiv.classList.add("hidden");
            progressBar.style.width = "0%";

            const xhr = new XMLHttpRequest();
            xhr.open("POST", "https://file.io", true);

            xhr.upload.onprogress = (e) => {
                if (e.lengthComputable) {
                    const percent = (e.loaded / e.total) * 100;
                    progressBar.style.width = percent + "%";
                }
            };

            xhr.onload = function() {
                if (xhr.status === 200) {
                    const response = JSON.parse(xhr.responseText);
                    progressContainer.classList.add("hidden");
                    resultDiv.classList.remove("hidden");
                    downloadLink.href = response.link;
                    downloadLink.innerText = response.link;
                } else {
                    alert("Erro ao enviar o arquivo.");
                }
            };

            xhr.send(formData);
        }

        function copyLink() {
            navigator.clipboard.writeText(downloadLink.innerText);
            alert("Link copiado para a área de transferência!");
        }
    </script>
</body>
</html>
