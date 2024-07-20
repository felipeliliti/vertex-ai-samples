# PROJETO ANTI APAGÃO AVANÇADO 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

Welcome to the Google Cloud [Vertex AI](https://cloud.google.com/vertex-ai/docs/) sample repository.

## Overview

The repository contains [notebooks](https://github.com/GoogleCloudPlatform/vertex-ai-samples/tree/master/notebooks) and [community content](https://github.com/GoogleCloudPlatform/vertex-ai-samples/tree/master/community-content) that demonstrate how to develop and manage ML workflows using Google Cloud Vertex AI.

## Repository structure

```bash
├── community-content - Sample code and tutorials contributed by the community
├── notebooks
│   ├── community - Notebooks contributed by the community
│   ├── official - Notebooks demonstrating use of each Vertex AI service
│   │   ├── automl
│   │   ├── custom
│   │   ├── ...
```

## Contributing

Contributions welcome! See the [Contributing Guide](https://github.com/GoogleCloudPlatform/vertex-ai-samples/blob/master/CONTRIBUTING.md).

## Getting help

Please use the [issues page](https://github.com/GoogleCloudPlatform/vertex-ai-samples/issues) to provide feedback or submit a bug report.

## Disclaimer

This is not an officially supported Google product. The code in this repository is for demonstrative purposes only.

## Feedback

Please feel free to fill out our [survey](https://bit.ly/vertex-ai-samples-survey) to give us feedback on the repo and its content.
import pandas as pd
import numpy as np
import requests

def collect_data(api_url):
    response = requests.get(api_url)
    data = response.json()
    df = pd.DataFrame(data)
    return df

# Exemplo de URL de API para coleta de dados
api_url = "https://example.com/api/system_status"
data = collect_data(api_url)from sklearn.ensemble import IsolationForest

def detect_anomalies(data):
    model = IsolationForest(contamination=0.01)
    model.fit(data)
    data['anomaly'] = model.predict(data)
    anomalies = data[data['anomaly'] == -1]
    return anomalies

anomalies = detect_anomalies(data)from twilio.rest import Client

def send_alert(anomalies):
    if not anomalies.empty:
        account_sid = 'your_account_sid'
        auth_token = 'your_auth_token'
        client = Client(account_sid, auth_token)
        message = client.messages.create(
            body=f"Anomalias detectadas: {anomalies}",
            from_='+1234567890',
            to='+0987654321'
        )
        print(message.sid)

send_alert(anomalies)
from flask import Flask, render_template
import pandas as pd

app = Flask(__name__)

@app.route('/')
def index():
    data = collect_data(api_url)
    anomalies = detect_anomalies(data)
    return render_template('index.html', tables=[data.to_html(classes='data', header="true"), anomalies.to_html(classes='data', header="true")])

if __name__ == '__main__':
    app.run(debug=True)from apscheduler.schedulers.blocking import BlockingScheduler

scheduler = BlockingScheduler()

@scheduler.scheduled_job('interval', minutes=5)
def scheduled_job():
    data = collect_data(api_url)
    anomalies = detect_anomalies(data)
    send_alert(anomalies)

scheduler.start()
<!DOCTYPE html>
<html>
<head>
    <title>TechGuardian Dashboard</title>
</head>
<body>
    <h1>Status dos Sistemas</h1>
    <h2>Dados Coletados</h2>
    <div>{{ tables[0]|safe }}</div>
    <h2>Anomalias Detectadas</h2>
    <div>{{ tables[1]|safe }}</div>
</body>
</html>