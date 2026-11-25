import os
import requests
from flask import Flask, request, jsonify, send_from_directory

app = Flask(__name__)

@app.get("/")
def home():
    return send_from_directory(".", "index.html")

@app.post("/send-photo")
def send_photo():
    token = os.environ.get("TELEGRAM_BOT_TOKEN")
    chat_id = os.environ.get("TELEGRAM_CHAT_ID")
    photo = request.files.get("photo")

    if not token or not chat_id or not photo:
        return jsonify({"error": "Missing settings or photo"}), 400

    r = requests.post(
        f"https://api.telegram.org/bot{token}/sendPhoto",
        data={"chat_id": chat_id},
        files={"photo": photo},
        timeout=30
    )
    return jsonify({"ok": r.ok}), (200 if r.ok else 502)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(os.environ.get("PORT", 8080)))