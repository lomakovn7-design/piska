<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Панель публикаций</title>
    <style>
        body {
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
            background-color: #f0f2f5;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            margin: 0;
        }
        .card {
            background: #ffffff;
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 450px;
        }
        label {
            font-weight: bold;
            font-size: 13px;
            color: #606770;
            display: block;
            margin-top: 12px;
            margin-bottom: 5px;
        }
        input, select, textarea {
            width: 100%;
            padding: 10px;
            border: 1px solid #ccd0d5;
            border-radius: 8px;
            box-sizing: border-box;
            font-size: 14px;
            background-color: #fff;
        }
        textarea {
            height: 120px;
            resize: vertical;
        }
        button {
            width: 100%;
            background-color: #0088cc;
            color: white;
            border: none;
            padding: 12px;
            margin-top: 18px;
            border-radius: 8px;
            font-weight: bold;
            font-size: 15px;
            cursor: pointer;
            transition: background 0.2s;
        }
        button:hover {
            background-color: #006699;
        }
        #status {
            margin-top: 15px;
            text-align: center;
            font-weight: bold;
            font-size: 14px;
        }
    </style>
</head>
<body>

<div class="card">
    <label for="postType">Тип публикации:</label>
    <select id="postType">
        <option value="post">✏️ Пост</option>
        <option value="article">📄 Статья</option>
        <option value="poll">☑️ Опрос</option>
        <option value="video_note">⭕ Кружок</option>
        <option value="voice">🎙️ Голосовое</option>
        <option value="sticker">😊 Стикер</option>
        <option value="giveaway">🎁 Розыгрыш</option>
    </select>

    <label for="token">Токен бота:</label>
    <input type="password" id="token" placeholder="123456789:ABCdef...">

    <label for="chatId">ID чата:</label>
    <input type="text" id="chatId" placeholder="-100123456789">

    <label for="message">Текст сообщения:</label>
    <textarea id="message" placeholder="Напиши текст..."></textarea>

    <button onclick="sendMessage()">Опубликовать</button>

    <div id="status"></div>
</div>

<script>
    document.getElementById('token').value = localStorage.getItem('bot_token') || '';
    document.getElementById('chatId').value = localStorage.getItem('chat_id') || '';

    async function sendMessage() {
        const token = document.getElementById('token').value.trim();
        const chatId = document.getElementById('chatId').value.trim();
        const text = document.getElementById('message').value.trim();
        const statusDiv = document.getElementById('status');

        if (!token || !chatId || !text) {
            statusDiv.style.color = "red";
            statusDiv.innerText = "Заполни все поля!";
            return;
        }

        localStorage.setItem('bot_token', token);
        localStorage.setItem('chat_id', chatId);

        statusDiv.style.color = "gray";
        statusDiv.innerText = "Отправка...";

        try {
            const response = await fetch(`https://api.telegram.org/bot${token}/sendMessage`, {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({
                    chat_id: chatId,
                    text: text
                })
            });

            const data = await response.json();

            if (data.ok) {
                statusDiv.style.color = "green";
                statusDiv.innerText = "✅ Опубликовано!";
                document.getElementById('message').value = '';
            } else {
                statusDiv.style.color = "red";
                statusDiv.innerText = "❌ Ошибка: " + data.description;
            }
        } catch (error) {
            statusDiv.style.color = "red";
            statusDiv.innerText = "❌ Ошибка сети!";
        }
    }
</script>

</body>
</html>
