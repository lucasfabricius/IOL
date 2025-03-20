from flask import Flask, request, jsonify
import requests
from datetime import datetime, timedelta

app = Flask(__name__)

# Token de acceso proporcionado
TOKEN = "eyJhbGciOiJSUzI1NiIsInR5cCI6ImF0K2p3dCJ9.eyJzdWIiOiIzMTQ1MDIzIiwiSUQiOiIzMTQ1MDIzIiwianRpIjoiMTg5YzMwMGMtZGFjYS00ODQ0LWJlMmYtNDA5YjUyMTgwMWFmIiwiY29uc3VtZXJfdHlwZSI6IjEiLCJ0aWVuZV9jdWVudGEiOiJUcnVlIiwidGllbmVfcHJvZHVjdG9fYnVyc2F0aWwiOiJUcnVlIiwidGllbmVfcHJvZHVjdG9fYXBpIjoiVHJ1ZSIsInRpZW5lX1R5QyI6IlRydWUiLCJuYmYiOjE3NDI0OTk3NzUsImV4cCI6MTc0MjUwMDY3NSwiaWF0IjoxNzQyNDk5Nzc1LCJpc3MiOiJJT0xPYXV0aFNlcnZlciIsImF1ZCI6IklPTE9hdXRoU2VydmVyIn0.Hyarhb3wl4J0BRXwrT5l_x-Phxtp2ZO788FI7LOGpCC9tOvVOY0Oks-lQyajMkXfqcQBp-xDTfpbDQNVEqGfYjNlWPUMyckSKw2aHkrCJ0FOMGLAmCW7Lv4w-i_SzfQtbj4kBG6Pl3CBUuR6zmS9Ud-fruS9q-3hzXtdRTOOgk3o3bdZJzv41iIszzgJ5DbZNGobOOeAfN6EqjaH6grdBvhxj5anc5jwZeNAx47U0kY2mMx9yoHe2jfHboyfXQzQZ0UpjFs26NQle_aPShJBhAKHAgj5uVoZWj5x9jP3QJGmkiKKfSp_GJdQmZQWlW66TQFMkTpsARgezCgyja8EbQ"

# Cache para almacenar cotizaciones
cotizaciones_cache = {}
CACHE_DURATION = timedelta(minutes=5)  # Duración del cache: 5 minutos

def obtener_cotizacion_desde_api(ticker, mercado="bCBA"):
    # URL del endpoint para obtener cotizaciones
    url = f"https://api.invertironline.com/api/v2/{mercado}/titulos/{ticker}/cotizacion"

    # Cabeceras con el token de acceso
    headers = {
        "Authorization": f"Bearer {TOKEN}"
    }

    # Realizar la solicitud GET
    response = requests.get(url, headers=headers)

    if response.status_code == 200:
        return response.json()
    else:
        return None

@app.route("/cotizacion", methods=["GET"])
def obtener_cotizacion():
    ticker = request.args.get("ticker")
    mercado = request.args.get("mercado", "bCBA")  # Por defecto, mercado BCBA

    if not ticker:
        return jsonify({"error": "Debes proporcionar un ticker"}), 400

    # Verificar si la cotización está en cache y es reciente
    if ticker in cotizaciones_cache:
        cached_data, timestamp = cotizaciones_cache[ticker]
        if datetime.now() - timestamp < CACHE_DURATION:
            return jsonify(cached_data)

    # Obtener la cotización desde la API
    cotizacion = obtener_cotizacion_desde_api(ticker, mercado)

    if cotizacion:
        # Almacenar en cache
        cotizaciones_cache[ticker] = (cotizacion, datetime.now())
        return jsonify(cotizacion)
    else:
        return jsonify({"error": "No se pudo obtener la cotización"}), 500

if __name__ == "__main__":
    app.run(debug=True)
